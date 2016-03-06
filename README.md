# 1 Activity的启动模式

|特性|standard|singleTop|singleTask|singleInstance|
|---|---|---|---|---|
|与发出intent的Activity|在同一个task|在同一个task|如果加入了taskAffinity属性，则新开启一个task，否则在原来的task中|独占一个task|
|是否允许多个实例|每次都生成新的实例|如果发出intent的Activity所在的task的顶部存在目标Activity的实例，则直接使用该实例，调用`OnNewIntent`，否则生成新的实例|只允许一个实例存在，重复启动会调用`onNewIntent`方法|只有一个实例|
|是否允许存在于多个task中|允许|允许|只存在于一个task中|只存在于一个task中|

> singleTask只是让系统判定为“可以单task启动”，而不是直接开启新的task启动，需要加上taskAffinity属性，值不同于当前的taskAffinity（一般默认的taskAffinity的value为包名）

> taskAffinity属性设置为相同，可以让不同应用的Activity跑在同一个task中。

> 按下多任务键，就能看到task的数量。

> 同一个task中，按下返回键起弹栈的作用。

>  为一个activity的taskAffinity设置一个空字符串，表明这个activity不属于任何task

# 2 通过Intent传递一些二进制数据的方法有哪些?

- 使用Serializable接口实现序列化，这是Java常用的方法。

- 实现Parcelable接口，这里Android的部分类比如Bitmap类就已经实现了，同时Parcelable在Android AIDL中交换数据也很常见的。

- [Android Studio一键生成Parcelable代码的插件](https://github.com/mcharmas/android-parcelable-intellij-plugin)

# 3 Android应用的入口点

`AndroidManifest,xml`文件，可以查看Application标签下的`Activity`，`Service`，`BroadCaseReceiver`，`ContentProvider`。

一般的，带有以下代码的Activity是应用从桌面启动的入口Activity
```xml
<intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
</intent-filter>
```

# 4 Android都有哪些XML解析器

- 结论

> dom方式解析xml，比较简单，并可以访问兄弟元素，但是需要将整个xml文档加载到内存中，对于android设备来说，不推荐使用dom的方式解析xml。

> sax和pull都是基于事件驱动的xml解析器，在解析xml时并不会加载整个的xml文档，占用内存较少，因此在android开发中建议使用sax或者pull来解析xml文档。

sax和pull都是基于事件驱动的xml解析器，在解析xml时并不会加载整个的xml文档，占用内存较少，因此在android开发中建议使用sax或者pull来解析xml文档。

- 引用

[Android系列--DOM、SAX、Pull解析XML](http://www.cnblogs.com/xiaoluo501395377/p/3444744.html)

# 5 SQLite支持事务吗? 添加删除如何提高性能?

- 不支持事务。

- 可以通过调用原生的经过优化的sql语句来提高性能。

# 6 如何安全退出已调用多个Activity的Application

> 可以考虑一个建立一个manager类，将所有启动的Activity加入集合中（在BaseActivity的onCreate中执行加入的方法），集合可以是list，vector，退出的时候使用manager类遍历该集合退出，需要注意同步的问题。

# 7 Android的数据存储方式

- SharePrefernce

    简单的键值对存储，

```Java
SharedPreferences sp = SharedPreferences sp = getApplicationContext().getSharedPreferences("fileName", MODE_PRIVATE);
SharedPreferences.Editor editor = sp.edit();
```
> 使用editor操作，可以存储
- `String`，
- `Set<String>`，
- `int`,
- `long`,
- `float`,
- `boolean`
> 等数据类型，进行操作后需要调用commit方法（类似于数据库事务操作）。

> 存储文件在`/data/data/pcakageName/shared_prefs/`中，以xml的格式进行存储。

- sqlite存储

 一个小型的关系型数据库，常见的Android上ORM框架有
    - [LitePal](https://github.com/LitePalFramework/LitePal)
    - [GreenDao](https://github.com/greenrobot/greenDAO)
    - [ActiveAndroid](https://github.com/pardom/ActiveAndroid)


- 文件存储，也就是常说的Cache

    在Android系统中，存在着两个Cache目录，
    - 一个是internal存储中，也就是`/data/data/packageName/cache`目录中。
    - 一个是external存储中，也就是`/storage/sdcard0/Android/data/packageName`中。
