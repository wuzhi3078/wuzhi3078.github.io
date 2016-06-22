---
title: Unity 序列化
layout: post
date: 2016-06-21 10:17:34
tags: [unity]
categories: [Unity, 序列化]
---
一般我们在游戏项目都需要进行“编辑工具——文件——加载解析文件”工作。编辑工具到文件的过程，是把逻辑类的数据保存起来，序列化。从文件解析出来，反序列化。
通常，我们有Json或Xml格式使用，方便且解耦人员。Unity有自己的序列化文件。

<!--more-->

# ScriptableObject
使用的情况如一下代码情况，把一些布局数据写到文件中：
```C#
[MenuItem ("Tools/Zuma/LevelConfig2Json", false, 1001)]
public static void GameLevelLayout () {
    string assetPath = @"Assets/Resources/map.asset";

    if (Selection.activeGameObject == null) 
        throw new UnityException ("must select Map");

    Transform map = Selection.activeGameObject.transform;

    MapConfig info = AssetDatabase.LoadAssetAtPath (assetPath, typeof (MapConfig)) as MapConfig;
    bool isExist = (info != null); 

    if (info == null) {
        Debug.Log ("AssetDatabase not exist MapConfig");
        info = ScriptableObject.CreateInstance <MapConfig>() as MapConfig;			
    }

    info.MapInfo = new Vector3 [map.childCount];
    for (int i =0, L = map.childCount; i < L; ++i) {
        Transform t = map.GetChild (i);
        info.MapInfo [i] = t.localPosition;
    }
    
    if ( !isExist ) {
        AssetDatabase.CreateAsset (info, assetPath);
        return;
    }

    // 这里很重要，如果没有告诉unity已经被改变。它只写入内存，没有写入磁盘
    EditorUtility.SetDirty (info);
    AssetDatabase.SaveAssets();
}
```
# MonoBehaviour
这个我们最常用，每次把GameObject拖成Prefab时它被序列化到.prefab文件。在Scene它就序列化到.unity场景文件。

# 编辑数据
我们设计了两个类
* MonoScript : MonoBehaviour
* SelfScriptable : ScriptableObject
```
using UnityEngine;
using System.Collections;
public class MonoScript : MonoBehaviour {
	public Vector3 [] mPosition;
}
```
```
using UnityEngine;
using System.Collections;
public class SelfScriptable : ScriptableObject {
	public Vector3 [] mPosition;
}
```
ScriptableObject 需要用代码生成asset文件。
```C#
SelfScriptable self = SelfScriptable.CreateInstance <SelfScriptable> ();
		UnityEditor.AssetDatabase.CreateAsset (self, "Assets/Game/Resources/SelfScriptable.asset");
```
MonoBehaviour 需要挂载到GameObject，然后生成Prefab，在Prefab身上是可编辑的。
# 使用数据
* 测试 MonoBehaviour  
测试情景，我们用另外一个 MonoBehaviour, 声明一个
```C#
public MonoScript mRef;
```
如图:  
![][1]
* 测试ScriptableObject  
同样的声明  
```C#
public SelfScriptable mRef;
```
![][2]
也是可以拖放到篇public变量中的。
用代码测试
```
public SelfScriptable mRef;
void Start () {
    foreach (Vector3 e in mRef.mPosition)
        Debug.Log (e);
}
```
两种情况都可以打印出数据。
# 非public引用情况下
我们知道，Unity在非pulic引用下，可以有Resource.Load与WWW等加载的方式。我们尝试Resource.Load的情况
加入文件已经放到Resources目录  
![][3] 
* 测试MonoScript
```
GameObject g = Resources.Load ("MonoScript", typeof(GameObject)) as GameObject;
MonoScript mRef = g.GetComponent <MonoScript> () as MonoScript;
foreach (Vector3 e in mRef.mPosition)
    Debug.Log (e);
```
这种情况下，是有数据输出的。但是，加载了资源如果我们使用完毕，是需要释放的
```
Resources.UnloadAsset (g);
g = null;
Resources.UnloadUnusedAssets();
```
使用以上代码会出现：
- UnloadAsset may only be used on individual assets and can not be used on GameObject's / Components or AssetBundles
```
GameObject.Destroy (g);
```
这里又出现:
- Destroying assets is not permitted to avoid data loss.
If you really want to remove an asset use DestroyImmediate (theObject, true);  
>使用 DestroyImmediate 时，在Editor情景下都会把Prefab Kill掉。  

*测试 SelfScriptable*  
```
SelfScriptable mRef = Resources.Load("SelfScriptable", typeof(SelfScriptable)) as SelfScriptable;
foreach (Vector3 e in mRef.mPosition)
	Debug.Log (e);

Resources.UnloadAsset (mRef); mRef = null;
Resources.UnloadUnusedAssets();
```
发现并未问题。



[1]:UnitySerialize/0.png
[2]:UnitySerialize/1.png
[3]:UnitySerialize/2.png