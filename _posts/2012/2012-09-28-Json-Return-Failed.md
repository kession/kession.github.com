---
layout: post
title: xselect
categories:
- javascript
tags:
- javascript
- json
- spring
---

## 异步请求返回JSON为空常见原因
***
**问题描述:**在进行异步请求的时候，有时会遇到程序完成返回，结果的JSON串为空的情况。首先确保程序Debug能return ModelAndView，一般原因有以下两种：
### 1. Float字段转换出错导致
 Float类型数据在返回时可能会造成ModelAndView转换为JSON串时失败，有一个对象转换失败则整个JSON串返回为空。可以新建一Bean专门用于返回，将BO取得的对象拷贝过来。
 
### 2.  Controller接受多个对象导致
Controller用于接受的参数可为多个参数，但多个参数只能为基本数据类型；或者接受单一对象作为参数。多个对象作为参数传递时，可改为多个基本数据类型传递，手动new对象处理。
