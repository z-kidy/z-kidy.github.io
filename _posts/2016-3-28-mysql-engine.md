---
layout: post
title: Mysql的各种存储引擎
category: Mysql
---



## 什么是存储引擎？

  有的表简单，有的表复杂，有的表根本不用来存储任何长期的数据，有的表读取时非常快，但是插入数据时去很差；而我们在实际开发过程中，就可能需要各种各样的表，不同的表，就意味着存储不同类型的数据，数据的处理上也会存在着差异，那么。
  对于MySQL来说，它提供了很多种类型的存储引擎，我们可以根据对数据处理的需求，选择不同的存储引擎，从而最大限度的利用MySQL强大的功能。

	linxu mysql下
	>>show engines;
	查看支持的存储引擎
    >>show create table student;
    查看student表的生成，最后一行有关存储引擎，字符等 

<!-- more --> 

## MyISAM

  每当我们建立一个MyISAM引擎的表时，就会在本地磁盘上建立三个文件，文件名就是表名。例如，我建立了一个MyISAM引擎的tb_Demo表，那么就会生成以下三个文件：

+ tb_demo.frm，存储表定义；
+ tb_demo.MYD，存储数据；
+ tb_demo.MYI，存储索引。


  MyISAM表无法处理事务，**这就意味着有事务处理需求的表，不能使用MyISAM存储引擎**。MyISAM存储引擎特别适合在以下几种情况下使用：

+ 选择密集型的表。MyISAM存储引擎在筛选大量数据时非常迅速，这是它最突出的优点。
+ 插入密集型的表。MyISAM的并发插入特性允许同时选择和插入数据。例如：MyISAM存储引擎很适合管理邮件或Web服务器日志数据。


## InnoDB

  InnoDB是一个健壮的事务型存储引擎，这种存储引擎已经被很多互联网公司使用，为用户操作非常大的数据存储提供了一个强大的解决方案。我的电脑上安装的MySQL 5.6.13版，InnoDB就是作为默认的存储引擎。InnoDB还引入了行级锁定和外键约束，在以下场合下，使用InnoDB是最理想的选择：

+ 更新密集的表。InnoDB存储引擎特别适合处理多重并发的更新请求。
+ 事务。InnoDB存储引擎是支持事务的标准MySQL存储引擎。
+ 自动灾难恢复。与其它存储引擎不同，InnoDB表能够自动从灾难中恢复。
+ 外键约束。MySQL支持外键的存储引擎只有InnoDB。
+ 支持自动增加列AUTO_INCREMENT属性。

**Django orm 默认的存储引擎**

**一般来说，如果需要事务支持，并且有较高的并发读取频率，InnoDB是不错的选择。**

## MEMORY

  使用MySQL Memory存储引擎的出发点是速度。为得到最快的响应时间，采用的逻辑存储介质是系统内存。虽然在内存中存储表数据确实会提供很高的性能，但当mysqld守护进程崩溃时，所有的Memory数据都会丢失。获得速度的同时也带来了一些缺陷。它要求存储在Memory数据表里的数据使用的是长度不变的格式，这意味着不能使用BLOB和TEXT这样的长度可变的数据类型，VARCHAR是一种长度可变的类型，但因为它在MySQL内部当做长度固定不变的CHAR类型，所以可以使用。

一般在以下几种情况下使用Memory存储引擎：

+ 目标数据较小，而且被非常频繁地访问。在内存中存放数据，所以会造成内存的使用，可以通过参数max_heap_table_size控制Memory表的大小，设置此参数，就可以限制Memory表的最大大小。
+ 如果数据是临时的，而且要求必须立即可用，那么就可以存放在内存表中。
+ 存储在Memory表中的数据如果突然丢失，不会对应用服务产生实质的负面影响。
+ Memory同时支持散列索引和B树索引。<strong>B树索引的优于散列索引的是，可以使用部分查询和通配查询，也可以使用 <、>和>=等操作符方便数据挖掘。散列索引进行“相等比较”非常快，但是对“范围比较”的速度就慢多了，因此散列索引值适合使用在=和<>的操作符中，不适合在<或>操作符中，也同样不适合用在order by子句中。

## ARCHIVE

  Archive是归档的意思，在归档之后很多的高级功能就不再支持了，仅仅支持最基本的插入和查询两种功能。在MySQL 5.5版以前，Archive是不支持索引，但是在MySQL 5.5以后的版本中就开始支持索引了。Archive拥有很好的压缩机制，它使用zlib压缩库，在记录被请求时会实时压缩，所以它经常被用来当做仓库使用。
  
## 未完待续