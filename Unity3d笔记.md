---
title: Unity3d笔记
date: 2016-08-29 16:19:21
categories: 游戏Client
tags: Unity3D
---

1. `GameObject.CreatePrimitive()`:创建一个原始游戏对象，七参数可以设置为立方体、球体、圆柱体等系统默认的游戏对象。
2. `AddComponent()`:用于给该游戏对象添加一个组件。组件没有自身对应的删除方法，需要使用父类执行Object.Destroy()才能删除 
```CSharp
	GameObject obj = GameObject.Find("Cube");  
	obj.AddComponent("Test");           //给对象添加Test脚本
	Destroy(obj.GetComponent("Test"));  //删除对象的Test脚本
```
3. `renderer.material.color`:设置渲染材质的颜色或贴图
4. `trasform.position`:设置该游戏对象的位置
<!--more-->
5. 获取游戏物体的三种方案  
```CSharp
        - GameObject obj = GameObject.find("UI Root\player");
            - 该方法的参数为游戏对象在Hierarchy视图中的完整路径
        - GameObject obj = GameObject.FindWithTag("MyTag");
            - 该方法参数为给游戏对象添加的Tag
        - GameObject[] objs = GameObject.FindGameObjectsWithTag("MyTag");
            - 该方法能够获取所有标签为`MyTag`的对象  
```
6. 消息的发送  
```CSharp
	BroadcastMessage("arg1","arg2")    //向子类发送消息  
   	SednMessage("arg1","arg2")         //向自己发送消息  
   	SendMessageUpwards("arg1","arg2")  //向父类发送消息  
	//arg1表示接收消息,arg2表示发送的参数
```
7. `Instantiate()`克隆游戏对象.
8. 游戏对象位置相关  
	- `obj.transform.position()`    //对象的当前位置
	- `obj.transform.Rotate()`      //旋转对象
	- `obj.transform.RotateAround()`//围绕某点旋转
	- `Time.deltaTime`              //上一帧所消耗的时间
	- `Vector3.right`               //x轴方向
	- `Vector3.up`                  //y轴方向
	- `Vector3.forward`             //z轴方向
	- `transform.Translate(Vector3.forward)`//平移
	- `transform.localeScale = Vectors(scaleX,scaleY,scaleZ)`//缩放
9. 游戏脚本都要继承`MonoBehaviour`类。而且新建的类名应该和项目资源视图中该脚本的名称对应，否则就无法成功绑定至游戏对象。
10. Time类  
	- Time.time:从游戏开始后开始计时，表示机制目前共运行的游戏时间
	- Time.deltaTime:获取Update()方法中完成上一帧所消耗的时间
	- Time.fixedTime:FixedUpdate()方法中固定消耗的时间中和 
	- Time.fixedDeltaTime:固定更新上一帧所消耗的时间。
11. `WaitForSeconds(int argc)`可以让程序等待argc秒。返回值为IEnumerator，在需要等待的地方调用`yield return new WaitForSeconds(s)`，让主线程等待s秒后继续执行。调用改函数的函数应该将返回类型设置为`IEnumerator`。  
```CSharp
	public class test : MonoBehaviour
	{
		IEnumerator Start()
		{
			return Test();
		}
		
		IEnumerator Test()
		{
			yield return new WaitForSeconds(7);
			//等待2秒后返回

		}
	} 
```
12. `Random.Range(int start, int end)`获取随机数，获取范围是个闭合的类似`(start,end)`。即在不包含start和end的值随机。
13. Renderer(渲染器)的使用:  
```CSharp
	Renderer render = obj.GetComponent("Renderer");
	render.material.color = Color.green;   //组件颜色
	render.material.mainTexture = null;    //组件贴图
```
14. Mathf 数学工具函数  
```CSharp
	Mathf.Abs(int i);
	Mathf.Clamp(int num, int min, int max);
	Mathf.Lerp(float start, float end, Time.time);
	Mathf.Sin(5);
	Mathf.Cos(7);
	Mathf.Tan(3);
	Mathf.Max(33,77);
	Mathf.Min(33,99);
	Mathf.PI;
```