---
layout: post
category : memory
tagline: "Supporting tagline"
tags : [unity3d]
---
{% include JB/setup %}

# unity3d之Network

## Network 建立连接

### 服务端脚本

#### RbServer.cs

	using UnityEngine;
	using System.Collections;
	
	public class RbServer : MonoBehaviour {
	
		// Use this for initialization
		void Start () {
		
		}
		
		// Update is called once per frame
		void Update () {
		
		}
	
		public void OnStartServer(GameObject obj){
			Network.InitializeServer(32, 9999, false);
		}
	
		void OnServerInitialized(){
			Debug.Log ("Server start!");
		}
	
		void OnPlayerConnected(NetworkPlayer player){
			Debug.Log (player.ipAddress + ":" + player.port + " connected");
		}
	
		void OnPlayerDisconnected(NetworkPlayer player){
			Debug.Log (player + " disconnected");
			Network.RemoveRPCs (player);
			Network.DestroyPlayerObjects (player);
		}	
	}

### 客户端脚本

#### RbClient.cs

	using UnityEngine;
	using System.Collections;

	public class RbClient : MonoBehaviour {
	
		// Use this for initialization
		void Start () {
		
		}
		
		// Update is called once per frame
		void Update () {
		
		}
	
		public void OnConnect(GameObject obj){
			Network.Connect ("127.0.0.1", 9999);
		}
	
		void OnConnectedToServer(){
			Debug.Log ("connect to server");
		}
	
		void OnDisconnectedToServer(){
			Debug.Log ("connect to server " + "127.0.0.1" + ":" + 9999);
		}
	
		void OnDisconnectedFromServer(NetworkDisconnection info){
			if (Network.isServer) {
				Debug.Log ("server connection disconnected");	
			} else if(info == NetworkDisconnection.LostConnection){
				Debug.Log("lost connection to server");
			} else {
				Debug.Log("successfully disconnected from the server");
			}
		}
	}

### 创建一个简单的界面

用NGUI创建一个简单的界面，三个按钮，分别是server启动服务器，client连接服务器，公用的往对方发生一个hello消息。

![1](/../../assets/images/unity3d-network/1.jpg)

![2](/../../assets/images/unity3d-network/2.png)

将事件处理函数绑定到按钮。把两个脚本按钮都放到Panel对象上。这里要注意，Panel对象上要加一个NetworkView组件，否则无法成功调用RPC(此处花去30分钟寻找原因)。[参考别人的教程](http://blog.sina.com.cn/s/blog_4ef78af501017xq6.html)

![3](/../../assets/images/unity3d-network/3.jpg)

## 服务端与客户端交互

### RPC调用

#### RbRpc.cs

	using UnityEngine;
	using System.Collections;
	
	public class RbRpc : MonoBehaviour {
	
		// Use this for initialization
		void Start () {
		
		}
		
		// Update is called once per frame
		void Update () {
		
		}
	
		public void OnSayHello(GameObject obj){
			if (Network.isClient) {
				Debug.Log("I am client");
				networkView.RPC ("SayHello", RPCMode.Server, "client");	
			}
			else if(Network.isServer){
				Debug.Log("I am server");
				networkView.RPC ("SayHello", RPCMode.Server, "server");
			}
		}
		
		[RPC]
		public string SayHello(string name){
			Debug.Log (name + " say : hello");
			return name;
		}
	}

看上面那张图，把这个脚本也挂到Panel上面。

build程序，启动两个程序（其中一个用unity3d启动，要不然怎么看debug消息！），其中一个做server，另一个做client。看到u3d打印出信息了吗（什么？没有打印出调试信息？拜托，你把u3d放到电脑最上层看看。u3d的刷新有点蛋疼，虽然是为了节省资源，此处花去2小时寻找为什么没有连接上，特别鸣谢二傻子让我不耻下问）。好了，现在看到连接成功，来试一下发送个hello消息。恭喜可以开始联机对战游戏开发了！