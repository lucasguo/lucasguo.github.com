---
layout: post
category : Hadoop
tagline: Hadoop基本结构
tags : [Hadoop]
---
{% include JB/setup %}

## 一般需要两个Name节点
NameNode和SecondaryNameNode
管理文件系统
NameNode属于管理节点，客户端要求写入或读取时，向NameNode请求需要操作的DataNode，NameNode返回后，客户端直接访问DataNode。
NameNode启动时会读取fsimage_*文件恢复文件系统状态，中途有修改时，计入edit_*文件。
SecondaryNameNode用于定期维护fsimage_*文件，合并edit_*的修改，防止NameNode挂掉重启需要重新加载fsimage_*时消耗过多时间。
允许有多个NameNode节点，每个节点管理不同的命名空间，称为NameNode联邦，联邦间共享相同的DataNode，不同命名空间的文件可能落在相同的DataNode上

## 需要数据节点
DataNode
文件带有冗余，类似于硬盘raid，在不同DataNode节点分别存储部分数据，在某些节点挂掉仍能保证读取到完整的文件。副本数量可设置，一般八个节点或以上，设置为3个副本。
