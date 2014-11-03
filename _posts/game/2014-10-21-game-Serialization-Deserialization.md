---
layout: post
category : memory
tagline: "Supporting tagline"
tags : [server]
---
{% include JB/setup %}

# 序列化和反序列化

序列化可以使服务端客户端的交互更方便传递参数。把对象，复杂的数据结构转换成二进制流传输，然后在接收端重新转换成对象。

## amf3

## protobuf

## json

### LitJson

ListJson的c#版本使用起来非常方便，定义一个class，直接可以序列化和反序列化到这个对象。

	// serialize 
    public static string Encode<T>(T data)
    {
        return JsonMapper.ToJson(data);
    }

	// deserialize
    public static T Decode<T>(string sJson)
    {
        return JsonMapper.ToObject<T>(sJson);
    }


## xml

## byte
