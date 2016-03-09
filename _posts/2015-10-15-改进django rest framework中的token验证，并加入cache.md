---
layout: post
title: 改进django rest framework中的token验证，并加入cache
category: django
---

最近项目中使用django-rest-framework作为后台框架，给客户端返回json结果。

在用户验证方面用到token验证，这是一种安卓/iso/..手机客户端常用的，方便的验证方式。

原理是客户端给我发一段字符串,这段字符串是用户在注册，登入的时候、服务器生成的，并关联到用户。保存到数据库，然后返回给客户端，客户端之后呢，就可以凭借这个字符串来确认**“我是我，不是别人”**。而不用每次验证都要通过账号密码。
_ _ _

django-rest-framework 有一套默认的token验证机制[dfs token验证](http://www.django-rest-framework.org/api-guide/authentication/#tokenauthentication) 具体用法不再细讲了，官方文档写得很清楚。

但是笔者发现一个问题，这个token验证机制存的token，一旦生成就保持不变。这样就引发一些问题，万一某人拿到你的token不就为所欲为了吗，就像别人拿到你的密码一样。

解决方案：
<!-- more -->
**给token设置过期时间，超过存活时间，这段token不再具有验证功能，每次用户重新登入，刷新token(这段新token的有存活时间)。**这样，重新登入后，你的token更新了，某些居心不良的人即便拿着之前抢来的token也没用。[stackoverflow](http://stackoverflow.com/questions/14567586/token-authentication-for-restful-api-should-the-token-be-periodically-changed)上已经有了token过期时间的讨论。
参考他们的代码我这样写。

##改进

    #coding=utf-8   auth.py
    from django.utils.translation import ugettext_lazy as _
    from django.core.cache import cache

    import datetime

    from rest_framework.authentication import BaseAuthentication
    from rest_framework import exceptions
    from account.models import Token
    from rest_framework import HTTP_HEADER_ENCODING

    def get_authorization_header(request):
        """
        Return request's 'Authorization:' header, as a bytestring.

        Hide some test client ickyness where the header can be unicode.
        """
        auth = request.META.get('HTTP_AUTHORIZATION', b'')
        if isinstance(auth, type('')):
            # Work around django test client oddness
            auth = auth.encode(HTTP_HEADER_ENCODING)
        return auth

    class ExpiringTokenAuthentication(BaseAuthentication):
        model = Token

        def authenticate(self, request):
            auth = get_authorization_header(request)

            if not auth:
                return None
            try:
                token = auth.decode()
            except UnicodeError:
                msg = _('Invalid token header. Token string should not contain invalid characters.')
                raise exceptions.AuthenticationFailed(msg)

            return self.authenticate_credentials(token)

        def authenticate_credentials(self, key):
            try:
                token = self.model.objects.get(key=key)
            except self.model.DoesNotExist:
                raise exceptions.AuthenticationFailed('认证失败')

            if not token.user.is_active:
                raise exceptions.AuthenticationFailed('用户被禁止')

            utc_now = datetime.datetime.utcnow()

            if token.created < utc_now - datetime.timedelta(hours=24 * 14):
                raise exceptions.AuthenticationFailed('认证信息过期')

        def authenticate_header(self, request):
            return 'Token'




还要配置settings文件

    REST_FRAMEWORK = {
        'DEFAULT_AUTHENTICATION_CLASSES': (
            'yourmodule.auth.ExpiringTokenAuthentication',
        ),
    }


##再改进
使用了cache缓存对token和关联的用户进行了缓存，因为token验证经常需要从数据库读取，加入缓存，大幅提高速度。


    def authenticate_credentials(self, key):
        
        token_cache = 'token_' + key
        cache_user = cache.get(token_cache)
        if cache_user:
            return cache_user     # 首先查看token是否在缓存中，若存在，直接返回用户

        try:
            token = self.model.objects.get(key=key)
        except self.model.DoesNotExist:
            raise exceptions.AuthenticationFailed('认证失败')

        if not token.user.is_active:
            raise exceptions.AuthenticationFailed('用户被禁止')

        utc_now = datetime.datetime.utcnow()

        if token.created < utc_now - datetime.timedelta(hours=24 * 14):  # 设定存活时间 14天
            raise exceptions.AuthenticationFailed('认证信息过期')

        if token:
            token_cache = 'token_' + key
            cache.set(token_cache, token.user, 24 * 7 * 60 * 60)  # 添加 token_xxx 到缓存

        return (token.user, token)


我的login函数是这样写的

    @api_view(['POST'])
    def login_views(request):
        receive = request.data   
        if request.method == 'POST':
            username = receive['username']
            password = receive['password']
            user = auth.authenticate(username=username, password=password)
            if user is not None and user.is_active:
                # update the token
                token = Token.objects.get(user=user)  
                token.delete()
                token = Token.objects.create(user=user)
                user_info = UserInfo.objects.get(user=user)
                serializer = UserInfoSerializer(user_info)

                response = serializer.data             
                response['token'] = token.key

                return json_response({
                    "result": 1,
                    "user_info":response, # response contain user_info and token 
                    })
            else:
                try:
                    User.objects.get(username=username)
                    cause = u'密码错误'
                except User.DoesNotExist:
                    cause = u'用户不存在'

                return json_response({
                    "result": 0,
                    "cause":cause,
                    })

