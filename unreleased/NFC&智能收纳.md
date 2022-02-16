---
layout: post
title: NFC&智能收纳
categories:
  - Idea
draft: true
id: 411
date: 2015-07-14 16:23:35
tags:
permalink:
description:
cover_img:
toc-disable:
comments:
---

# NFC&智能收纳

## NFC技术简介

> [Near field communication (NFC) is a set of ideas and technology that enables smartphones and other devices to establish radio communication with each other by touching the devices together or bringing them into proximity to a distance of typically 10 cm (3.9 in) or less.](http://en.wikipedia.org/wiki/Near_field_communication)

1.  是一种短距离的高频无线通信技术，允许电子设备之间进行非接触式点对点数据传输（`十厘米内`）
2.  向下兼容RFID，主要用于手机等手持设备中提供M2M(Machine to Machine)的通信
3.  交换数据双方分别处于“主动模式”（提供RF感应域）与“被动模式”（`可在不通电下工作`）

### 工作模式

1.  仿卡模式（Card emulation mode）[被动模式]：类似一张采用RFID技术的IC卡。一张NFC芯片即可替代大量IC卡在不同场合（如公交、门禁、支付）下使用。
2.  读卡器模式(Reader/writer mode)[主动模式]：作为非接触读卡器使用，例如从海报或者展览信息电子标签上读取相关信息。
3.  `点对点模式（P2P mode）`[双向模式]：与红外、蓝牙、WIFI相似，用于数据交换，传输距离较短，传输创建速度较快，传输速度也快些，功耗低（蓝牙也类似）。将两个具备NFC功能的设备链接，能实现`数据点对点传输`。

_`我们重点考察点对点模式，考虑到两者（手机与收纳箱）之间完成数据交换，确认物品状态`_

### 发展现状

1.  2014年有数据显示，每三台手机里面就有一台是带NFC功能的
2.  支持NFC的安卓手机大约占总激活量的15%；而iPhone在今年发布的6和6 plus上开始提供支持

### 硬件标准

#### 分类

1.  ~~~手机NFC芯片~~~ 智能手机提供，不在考察范围内
2.  标签芯片，实现被NFC读卡器或者NFC手机搜索读取里面的内容

#### 规格

1.  Tag 1 Type ：具有可读、重新写入的能力，可配置为只读。存储能力为96字节，用来存网址URL或其他小量数据（可被扩充到2k字节）。通信速度为106 kbit/s。此类标签简洁，故成本效益好
2.  Tag 2 Type ：具有可读、重新写入的能力，可配置为只读。基本内存大小为48字节（可扩充到2k字节）。通信速度106 kbit/s
3.  Tag 3 Type ：具有2k字节内存容量，数据通讯速度为212 kbit/s。适合较复杂的应用，成本较高
4.  Tag 4 Type ：制造时被预先设定为可读/可重写、或者只读。内存容量可达32k字节，通信速度介于106 kbit/s和424 kbit/s之间

第1与第2类标签是双态的，可为`读/写`或只读。第3与第4类则是只读，数据在生产时写入或者通过特殊的标签写入器来写入。

#### 成本

标签芯片的成本大约在人民币1-3元。 [参考](http://s.1688.com/selloffer/offer_search.htm?keywords=nfc+%B1%EA%C7%A9&amp;n=y&amp;from=industrySearch&amp;industryFlag=zmdz)

#### 主流供应商

1.  恩智浦
2.  博通
3.  联发科、三星电子

### 应用实例

#### 三星利用NFC推Holiday Inn酒店服务App

三星手机就将在伦敦奥运期间针对NFC提供了新型的服务。它推出了一个为Holiday Inn的伦敦Statford分店设计的酒店服务解决方案，通过Galaxy SIII手机的特殊App，房客就可以实现check-in和退房，并可使用手机的NFC功能打开房间门。

这样的开门方法足够聪明，它利用了房客的手机持有和安全协议，以识别机主是否拥有权限将门打开。你甚至不需要在你拿着手机的时候再额外地带上一把钥匙。就像你在使用一个低技术含量的房卡一样，一旦你在酒店超时了，它会自动取消你用手机访问酒店设施的授权。

#### 连接Wi-Fi

如果你想让家里(或办公室)的客人使用Wi-Fi而不需要给他们密码，你也许会用到InstaWifi。这个免费的安卓应用可以让你设置NFC标签，使手机或移动设备可以通过接触NFC设备来连接Wi-Fi。

#### 用NFC标签来分享Evernote笔记

Touchanote是一个免费应用，曾在笔记软件开发者竞赛上获奖。[链接](http://jingyan.baidu.com/article/f54ae2fccf2f8f1e92b849c9.html)分享了一个使用标签打开Evernote笔记的案例。Touchanote的盈利模式是通过售卖可写入内容的标签芯片（3加元1个）。

i.e.,可以利用手机向NFC标签中写入任意微内容并实现offline的分享（通过简单的触碰，比二维码便捷）

## 智能收纳箱--关于收纳物品的定位过程

### #1方案

> 1.  【录入】 通过APP端的interface将待收纳物品的基本信息录入，云端为用户维护录入数据，每项待收纳物品指定Unique ID
> 2.  【入箱】 APP端通过点击操作确定待放入物品，打开某一收纳箱，将待收纳物品放入，手机（NFC芯片）与收纳箱（NFC标签）进行一次接触，完成数据交换：手机APP端得到收纳箱ID，确认该物品放入指定箱内并update云端数据库；指定收纳箱得到收纳物品的ID。
> 3.  【查找】打开APP，浏览并确定待查找物品，数据库查询操作给出存放物品的收纳箱ID
> 4.  【出箱】用户打开查找给出ID的收纳箱，从其中取出物品，手机（NFC芯片）与收纳箱（NFC标签）进行一次接触，完成数据交换：手机APP端确认该物品拿出指定箱并update云端数据库；指定收纳箱删除该物品的ID。

### #2方案

> 1.  【录入】 通过APP端的interface将待收纳物品的基本信息录入，云端为用户维护录入数据，取出一个空NFC标签，通过手机与标签接触，将少量基本信息写入该空标签（同时，手机得到标签ID），将标签附着于待收纳物品表面
> 2.  【入箱】 打开收纳箱，将待收纳物品放入
> 3.  【数据更新】 所有收纳盒周期性“扫描”箱内物体，并通过无线连接技术（Wi-Fi or Bluetooth）向云端更新物体收纳情况信息
> 4.  【查找】 打开APP，浏览并确定待查找物品，数据库查询操作给出存放物品的收纳箱ID
> 5.  【出箱】 用户打开查找给出ID的收纳箱，从中取出物品，收纳箱重复【数据更新】操作

### 方案对比

<table>
<thead>
<tr>
  <th>方案</th>
  <th>#1方案</th>
  <th>#2方案</th>
</tr>
</thead>
<tbody>
<tr>
  <td>成本</td>
  <td>只需要给收纳箱定制NFC标签，较低</td>
  <td>需给每件收纳物体定制NFC标签，较高</td>
</tr>
<tr>
  <td>便捷性</td>
  <td>【入箱】【出箱】阶段需要通过接触操作确认</td>
  <td>需给物体附着NFC标签，【入箱】【出箱】无操作</td>
</tr>
<tr>
  <td>精确度</td>
  <td>依靠用户的确认操作，但高精度较易维持</td>
  <td>精度受到箱子放置密集程度的影响，难以保证高精度</td>
</tr>
<tr>
  <td>通用性</td>
  <td>可以在任意收纳箱上放置NFC标签完成，移植性好</td>
  <td>箱子需定制</td>
</tr>
<tr>
  <td>实现难度</td>
  <td>实现难度相对较低</td>
  <td>实现难度相对较高</td>
</tr>
<tr>
  <td>交互体验</td>
  <td>用户在放置物品过程中通过手机与收纳箱接触完成确认，较“cool”</td>
  <td>用户需要给物品附着标签，动作冗余较为繁杂</td>
</tr>
<tr>
  <td>建议</td>
  <td>推荐</td>
  <td>次选，可改进</td>
</tr>
</tbody>
</table>