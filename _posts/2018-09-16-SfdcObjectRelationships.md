---
layout: post
title: "Salesforce Object间的几种关联关系"
date: 2018-09-16 
description: "The relationships between Salesforce objects"
tag: Salesforce
---   

## 关联Salesforce Object间的几种关系

我们在给Salesforce Object建field的时候，用作Objects间关联关系的field有三种：

1. Lookup Relationship
2. Master-Detail Relationship
3. External Lookup Relationship

但作为Salesfroce自带Object的Account对象，如果不是Personal Account。
(也就是Account有两种：一般的Acccount，和Peronal Account。其实就是我们平常理解的‘企业客户’和‘个人客户’。)
还有另外一种关系，及：父子关系。用作一个大公司下面可能有多个子公司的关联关系。
这个不在我们讨论范围内.
[具体见](https://success.salesforce.com/answers?id=90630000000h2t2AAA)

’Lookup Relationship‘和‘Master-Detail Relationship’用作Salesforce Objects间关系的，而‘External Lookup Relationship’用于与外部系统数据库表间的关系。

## Lookup Relationship 与 Master-Detail Relationship 区别

Lookup Relationship 和 Master-Detail Relationship都是用作Salesforce Objects间的关联关系。在字段层面其实就是在建关联关系字段的那个Object上多出一个字段用来存放被关联Object的SalesforceID。
区别在于Lookup Relationship不是强制的，而Master-Detail Relationship关系是强制的。

*Lookup Relationship：*
比如一个‘Account’下面可能关联多个‘Contact’，我们一般用Lookup Relationship来处理他们间的关系，也即在‘Contact’上建一个Lookup Relationship字段关联‘Account’。但这个关联关系不是强制的，也就是说一个‘Contact’可以独立存在，他可以不关联任何‘Account’。在Salesfroce平台删除了一个‘Account’，与之关联的‘Contact’并不连带删除。

*Master-Detail Relationship：*
比如一个‘订单Object’关联多个‘订单明细Object’，我们一般用Master-Detail Relationship来处理他们间的关系，也即‘订单明细Object’上建一个Master-Detail Relationship字段关联‘订单Object’。但这个关联关系是强制的，也就是说一个‘订单明细Object’不可以独立存在，他必须关联一个‘订单Object’。在Salesfroce平台删除了一个‘订单Object’，与之关联的‘订单明细Object’也一并连带删除。

## 关于External Lookup Relationship

External Lookup Relationship,看名字就知道它是用于处理存放在Salesforce平台外的数据库表与Salesforce Objects间关联关系的。

比如：（*注：下面说的‘BDR Salesforce系统’代表一个Salesforce系统，‘WCCS系统’代表一个第三方系统*）

我们当前’BDR Salesforce系统‘的有效售点来自’WCCS系统‘，这些存放在’WCCS系统‘上的售点有自己primary key(‘主键’)体系，而我们知道Salesforce中所有数据都有一个SalesforceID来唯一标示。如何处理两个系统间主键体系的不同，就是通过External Lookup Relationship。

在对应Salesforce的售点Object中建好一个External Lookup Relationship字段来对应’WCCS系统‘中的售点的主键。
’WCCS系统‘中的售点第一次导入到’BDR Salesforce系统‘，填入’WCCS系统‘的主键。
下次’WCCS系统‘中该售点有更新要同步到Salesforce中，就拿这个External Lookup Relationship字段作为唯一标示来更新。

## 如何建立Many-to-Many Relationship

如上所述，在Salesforce Object间起关联关系的字段一般为Lookup Relationship和Master-Detail Relationship。但Lookup Relationship和Master-Detail Relationship都是One-to-Many的关系，比如一个Account下挂多个Contact，在一个订单下挂多个订单明细。

那如何建立Many-to-Many Relationship呢？那就是建起中间作用的Object。

*比如:*
做一个人事聘用系统，需要几个表：

1. 代表职位的Object,'Position'.
2. 代表职位发布网站的Object, 'Employment Website'.

一个'Employment Website'可以发布多个'Position'，一个'Position'可以发布到多个'Employment Website'。
我们就在两者间建一个中间Object,'PositionWebsiteAssciation'.在'PositionWebsiteAssciation'分别建两个Master-Detail Relationship的字段分别关联到'Position'和'Employment Website'。
从而实现'Position'和'Employment Website'间Many-to-Many Relationship。

也就是说我们一般使用中间Object,利用Master-Detail来实现Many-to-Many。本质上说还使用的One-to-Many。
