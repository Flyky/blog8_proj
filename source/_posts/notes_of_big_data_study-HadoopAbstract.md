---
title: 大数据学习笔记| Hadoop - Hadoop概述
date: 2017-09-27 00:48:52
tags: 
	- hadoop
	- bigdata
	- note
categories:
	- Tech
	- BigData
	- Hadoop
---

Hadoop作为Apache的一个顶级项目，开源实现了Google的MapReduce、GFS(Google File System)、Bigtable等核心技术，成为目前大数据技术领域的核心软件平台，围绕着hadoop的一系列工具促使了hadoop生态圈的迅速发展

随着web和移动互联网的高速发展，人们创造出的数据量越来越庞大，面对这些海量的数据和信息，如何存储如何处理，如何提取出用户想要了解的有价值的信息，这些问题的产生和解决，成为了大数据时代的重要的发展线索。
随着云计算的发展，大量的数据存储在了云端，人们寻求着高效存储与处理信息，并且低成本的解决方案。Hadoop的发展与应用无疑是诸多数据发掘者的不二之选。
<!-- more -->

<strong>Hadoop是以HDFS（Hadoop Distributed File System）和MapReduce为核心，以及一些支持Hadoop的其他子项目的通用工具组成的分布式计算系统。</strong>主要用于海量数据的高效存储、管理和分析。HDFS具有高容错性、高伸缩性等优点，使得Hadoop可以分布式的部署在价格低廉的硬件设备上；而MapReduce分布式计算框架可以充分利用hadoop集群的威力高速运算和解析数据，无需了解分布式底层细节。这种结构实现了存储于计算的高度耦合，十分有利于面向数据的系统架构，因此已经成为大数据技术领域的标准。
