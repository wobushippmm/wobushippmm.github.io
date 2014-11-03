---
layout: post
category : memory
tagline: "Supporting tagline"
tags : [unity3d]
---
{% include JB/setup %}

# unity3d动态访问field，property，class

## 获得字段
this.GetType().GetField(fieldName)

## 获得字段类型
this.GetType().GetField(fieldName).FieldType

## 获得字段值
this.GetType().GetField(fieldName).GetValue(this)

## 赋值
this.GetField(fieldName).SetValue(this, value)

## 获得当前方法命名空间
System.Reflection.MethodBase.GetCurrentMethod().DeclaringType.Namespace

## 获得当前方法类全名
System.Reflection.MethodBase.GetCurrentMethod().DeclaringType.FullName

## 获得当前方法名
System.Reflection.MethodBase.GetCurrentMethod().name