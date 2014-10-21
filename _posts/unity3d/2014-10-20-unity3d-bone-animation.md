---
layout: post
category : memory
tagline: "Supporting tagline"
tags : [unity3d]
---
{% include JB/setup %}

# unity3d骨骼动画
unity3d实现动画的方式有很多种，基本有序列帧动画，矢量动画。u3d技术深不可测，我自觉只掌握了u3d动画技术的1%，在此记录这1%的知识，以便空出脑容量学习其余99%。下面记录3dmax骨骼动画导入到u3d中的其中一种方法。

## 导出骨骼动画
3dmax导入模型，编辑骨骼。导出出时，采用选中模型和骨骼导出，去掉动画选项。

![1](/../../assets/images/unity3d-bone-animation/1.jpg)

然后导出骨骼动画，只选中骨骼，导出动画是选中动画选项。

## 导入到unity3d

### 骨骼动画

有了原料，现在开始导入动画到unity3d。在unity3d中创建FBX文件夹，把模型和动作放入该文件夹。

![2](/../../assets/images/unity3d-bone-animation/2.jpg)

看看刚才导出的模型和动画有什么区别。在模型girl中，没有动画信息，而在girl@dance中，没有模型信息，但是多了动画信息。

![3](/../../assets/images/unity3d-bone-animation/3.jpg)

选中girl，在属性面板中，修改Rig页的Animation Type为Humanoid，为什么要修改这个？因为xx我也不知道，但是不改就会让我接下来崩溃（此处花去5小时找到这个修改）。有时间咱们再来研究这个选项的区别吧。

![4](/../../assets/images/unity3d-bone-animation/4.jpg)

把girl做个prefab吧，这样才能挂component。

![5](/../../assets/images/unity3d-bone-animation/5.jpg)

在属性中有一个Animator，用来设置动画控制。

![6](/../../assets/images/unity3d-bone-animation/6.jpg)

新建一个Animator，打开Animator编辑器，可以看到就是一个状态机。状态机转换可以设置一些条件。

![7](/../../assets/images/unity3d-bone-animation/7.jpg)

Animator挂到girl上，接下来就是控制动画播放。下面的脚本使girl在肥idle状态下，就把girl设置成idle。后一个动作会在前一个动作做完之后再播放（注意这句话的重要性）。

	IEnumerator CharacterIdle()
	{
		yield return new WaitForSeconds(0.2f);
		while (true)
		{
			Animator animator = GetComponent<Animator>();
			AnimatorStateInfo info = animator.GetCurrentAnimatorStateInfo(0);
			if (!info.IsName("idle"))
			{
				animator.SetInteger("action", 0);
			}
			yield return new WaitForSeconds(0.2f);
		}
	}

只放一个动画太单调，再加两个动作。这个脚本在点击头发的时候播放动作1，点裙子的时候播放动画2（是不是有点猥琐的感觉？）。

	void Update () {
		if (Input.GetMouseButtonDown (0)) {
			Ray ray = Camera.main.ScreenPointToRay( Input.mousePosition );
			RaycastHit hit;
			if (Physics.Raycast (ray, out hit, 100)) {
				if( hit.collider.tag == "toufa" )
				{
					Animator animator = GetComponent<Animator>();
					animator.SetInteger("action", 1);
					NGUITools.PlaySound( HelloMusic );
				}else if( hit.collider.tag == "qunzi" ){
					Animator animator = GetComponent<Animator>();
					animator.SetInteger("action", 2);
					NGUITools.PlaySound( DanceMusic );
				}
			}
		}
	}

OK，骨骼动画就这么实现了，你以为这样就结束了吗？NO，NO，NO。来看一下如何让人物长高和变胖。

### 控制骨骼

想起前面提到的令人崩溃的Animation type了吗，为啥要选Humanoid？因为不选这个，骨骼控制就实现不了。

控制骨骼无非就是控制骨骼的尺寸，旋转，位置。我们只讨论控制尺寸。

小头娃娃和大头娃娃。看到没有，身体一样大的，头不一样大。

![8](/../../assets/images/unity3d-bone-animation/8.jpg)

![9](/../../assets/images/unity3d-bone-animation/9.jpg)

拉伸骨骼，和骨骼绑定的模型也会被拉伸，这样就达到了控制骨骼来改变模型大小的目的。

控制骨骼的代码如下，head就是头部绑定的骨头。

	private float _headSize = 0.5f;
	public void OnHeadSizeChanged(GameObject obj){
		UIScrollBar bar = obj.GetComponent<UIScrollBar> ();
		_headSize = bar.value;
		Transform head = transform.Find ("Bip001/Bip001 Pelvis/Bip001 Spine/Bip001 Spine1/Bip001 Neck/Bip001 Head");
		head.localScale = new Vector3 (0.75f + 0.5f * _headSize, 0.75f + 0.5f * _headSize, 0.75f + 0.5f * _headSize);
	}
	

就这么简单！（就这么简单花了不少时间，f**k）

## 感想
unity3d就是个深坑！