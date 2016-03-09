---
layout: post
title: 基于用户投票的排名算法：Delicious和Hacker News (Python、Redis简单实现)
category: 算法
---



[基于用户投票的排名算法（一）：Delicious和Hacker News](http://www.ruanyifeng.com/blog/2012/02/ranking_algorithm_hacker_news.html)

来自阮一峰的博客

具体思想参照其博客的分析

##一、Delicious

> 最直觉、最简单的算法，莫过于按照单位时间内用户的投票数进行排名。得票最多的项目，自然就排在第一位。
下面使用Python、Redis简单的实现一些核心思想


    # -*- coding: utf-8 -*-
    import redis
    MAX_EXPIRE_TIME = 60
    cache = redis.Redis(host='localhost',port=6379,db=0)

    """
    all_votes:{
        'video_1': x,
        'video_2': y,
        ....
    }


    """

    def user_vote(video_id):
        key = 'video_%d', video_id
        cache.hincrby('all_votes', key, 1)    # video_xx 的 value(即投票数加1)

    def get_rank():
        """"
        脚本每隔 MAX_EXPIRE_TIME分钟运行一次，作为这段时间内投票数的统计


        """
        rank_dic = cache.hgetall('all_votes')
        rank_list = rank_dic.item().sort(key = lambda k: k[1])  # 根据video_xx的 value(投票数) 排名
        cache.delete('all_votes')                               # 清除这次统计
        return rank_list


<!-- more -->

##二、Hacker News

> 每个帖子前面有一个向上的三角形，如果你觉得这个内容很好，就点击一下，投上一票。根据得票数，系统自动统计出热门文章排行榜。但是，并非得票最多的文章排在第一位，还要考虑时间因素，新文章应该比旧文章更容易得到好的排名。

![](/img/2016-3-5/chart.png)

其中，

+ P表示帖子的得票数，减去1是为了忽略发帖人的投票。
+ T表示距离发帖的时间（单位为小时），加上2是为了防止最新的帖子导致分母过小（之所以选择2，可能是因为从原始文章出现在其他网站，到转贴至Hacker News，平均需要两个小时）。
+ G表示"重力因子"（gravityth power），即将帖子排名往下拉的力量，默认值为1.8，后文会详细讨论这个值。

从这个公式来看，决定帖子排名有三个因素：



    # -*- coding: utf-8 -*-
    import time
    import redis
    cache = redis.Redis(host='localhost',port=6379,db=0)
    G = 1.8

    """
    video_x:
    {
        'votes':   得票 
        'created': 创建时间
        'score'
    }

    """

    def set_hot(video_id):
        key = 'video_%d', video_id 
        value = cache.hgetall(key)

        P = value.get('votes')
        T = (time.time() - value.get('created')) / (60 * 60) 
        Score = (P - 1) / ((T + 2) ** G)

        cache.hset(key, 'score', Score)



*知道了算法的构成，就可以调整参数的值，以适用你自己的应用程序。*