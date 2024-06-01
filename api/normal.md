

# 常用控制脚本API
# 完整的API文档[查看](https://yooge.github.io/robot-docs/)

## 1. 启动app

```js
launchApp("浏览器");
```

## 1. 点击

### click全局函数[推荐] 
#### 无论元素是否有clickable属性，都会强制点击到对应的坐标上

```js
click("按钮1");
click("赞", 0);  //点击第0个赞
```
点击文字为“按钮1”的界面元素(按钮，文字等)

```js
click("*入购物车");  //点击包含这个文字的元素

click(desc('购物车').findOne());	
click(desc('购物车'),  0);	
```
点击文字包含"入购物车"的界面元素(按钮，文字等)

```js
click(300,500); //点击坐标(300,500)


click('购物车', 10,10);  //右下角偏移 10,10
click('购物车', -10,10); //左下角
click('购物车', 10,-10); //右上角
click('购物车', -10,-10); //左上角 
click(desc('购物车'));	 //新
click(desc('购物车'), 2);	 //新
click(desc('购物车').findOne());	 //
click(desc('购物车').findOne(), -10,-10);	 //购物车的左上角 10,10点击一下 ，以购物车的左上角为参考
click(desc('购物车').findOne(), 10,10);	 //购物车的右下角 10,10点击一下 ，以购物车的 右下角为参考
click(desc('购物车'), 10,10);
text('购物车').findOne().click(); //点击购物车，此方法点击的是元素本身(如果元素只是普通文本，则会失效)
click(text('购物车').findOne()); //(推荐)点击购物车，此方法点击的是屏幕，如果自己不可点击，会把事件传递给父控件
```

#### 点击进入页面，然后再回来

* 适合多页面跳来跳去的交互场景 *

```

click(desc('购物车'), function(){
	;;//进去页面了，干点啥
	//处理完毕后，会回到原页面
	//如图期待的页面没出现 请用 return 'click_fail'; 
});

//点击粉丝列表
click('粉丝', () => { 
	    //打开了粉丝列表页面
		var pp = desc('粉丝列表').findOne();
		var fanslist = listChildren(pp);
		//在粉丝列表页面上进行一系列操作
		dosomthing(fanslist);
	});
```

## 2. 输入
向本界面中的输入框输入文字

```js
setText("你好");
setText(0, "你好"); //第几个输入框
```

## 3. 查找控件
```js
//1. 查找界面上是否有文字为“消息”的控件，返回数组
text("消息"); 
text("消息").find(); 
//2. 界面上是否有控件包含“入购物车”这几个字，返回数组
text("*入购物车").find(); 
desc("*个赞")

//3. 查找并返回一个
text("消息").findOne(); 
//4. 查找并返回一个，超时5秒
text("消息").findOne(5000);
// 是否存在
text("消息").exists();

//5. 等待界面出现“”这个字的控件，等5秒(默认20秒)
text("消息").waitFor(5000); 
waitFor("消息");
waitFor("消息", 5000);

//6. 等待这个文字消失, 默认超时20秒
waitGone("消息");
waitGone("消息", 5000);
//7. 
textEx  //是text的临时补充版
textEx("#") //查找输入框
textEx("#139101") //查找输入框，里面内容为139101
textEx("#*请输入") //查找输入框，里面内容 包含 请输入

//8.
desc("赞").findOne(); //控件的Content-Description属性
descContains("评论数").findOne(); 

//9
className('RecyclerView');//控件的类
classNameContains('RecyclerView');//类


//10.
//遍历过滤器
var uc = className("TextView").filter(function(w){
    return w.text().length == 10;
});

//11.
//对象在屏幕内，比如查找抖音的点赞按钮时，会有两个在屏幕外。就需要这个函数来锁定
inScreen(desc('赞'));
inScreenOne(desc('赞'));
//或者 用扩展搜索
desc('赞').visibleToUser(true);  //控件是否可见。

//10 其他扩展搜索条件:
/*
cls('classname')
bounds(0,0, 600, 900) 控件在屏幕上的范围。
indexInParent(2) 控件在父控件的位置。
editable(true) 控件是否可编辑。
visibleToUser(true) 控件是否可见。
drawingOrder(2) 控件在父控件的绘制顺序。
clickable(true) 控件是否可点击。
longClickable(true) 控件是否可长按。
checkable(true) 控件是否可勾选。
checked(true) 控件是否可已勾选。
scrollable(true) 控件是否可滑动。
selected(true) 控件是否已选择。
enabled(true)
*/
```
### 3.0 控件的[属性]
`** 控件有很多属性，请打印一下获取的对象看看`

### 3.1 控件的[方法]
```js
var box = desc('body').findOne();
box.click(); //点击控件
box.longClick();
box.text();  //获取控件的文本
box.setText('xxx'); //设置文本
box.bounds();  //获取控件的坐标 top, left, right, bottom
box.paste();; //粘贴 ， setClip("你好");
box.children(); //子元素的数组
listChildren(box); //新增，同上， 增加了 textAll属性，所有的子元素的文本
box.child(i); //第3个子元素
box.parent(); //父控件
box.findOne(desc("赞")); //查找子元素
box.find(desc("赞")).get(1); //查找子元素，获取第2个
box.find(desc("赞")).empty(); //是否为空
```


### 3.2 页面是否匹配某些内容

根据页面是否匹配某些内容，来判断页面功能
```js
//函数
matchContents(matchkeys);

//查找当前页面是否同时包含 这4个项(主界面)
matchContents("消息 & 通讯录 & 工作台 & *待办");
//查找当前页面是否有如下情况(搜索手机号页面)
// A. 手机号 与 输入框  B. 出现文字‘搜索’ 与输入框
matchContents("手机号 & # | *搜索 & #");
//添加好友页面
matchContents("*好友中添加 & 已发邀请 | *好友中添加 & 添加");
```

### 3.3 复杂的查找/等待分支
 在流程中，可能出现任意个不同界面的时候，需要进行分支切换
 
```js
//等待出现两个控件中的一个， 返回 0或1，失败返回-1；超时5秒（默认20秒）
//这个功能 主要用在一个动作后，可能出现两个窗口中的一个。
var i = either("*加为好友", "用户不存在");
var i = either("*加为好友", "用户不存在", 5000);

```
```js
//1. 等待出现多个控件中的一个， 返回 0,1,2,3，失败返回-1； 超时5秒（默认20秒）
//这个功能 主要用在比如一个点击动作后，可能出现多个窗口中的一个。
var i = appear(["*加为好友", "用户不存在", "*无权限"], 5000);

//2. 或者，返回key, 失败则返回-1
var key = appear({"ok":"*加为好友", "unexist":"用户不存在", "denial": "*无权限"}, 5000);
```



### 3.3.4  逆向搜索
//逆向搜索，从我开始,向其他节点搜索
```js
var mynode = text('回复').findOne();
var targetSelector = className('androidx.recyclerview.widget.RecyclerView')
var obj = lookup(mynode, targetSelector);
```


### 3.3.5  图片查找
 ```js
//查找尺寸的图片
var ckRect= {w1:100}  //宽度大于100的图片
var ckRect = {w2:200} //宽度小于200的图片
var ckRect = {w1:100, w2:200} //宽度在100-200之间的图片
var ckRect = {h1:100, h2:500} //高度在100-500之间的图片
//或者数组 ckRect = [w1,w2,h1,h2] 

//查找所有符合尺寸的图片
var images = findPictures(parentObj, ckRect);

//查找尺寸的图片，取第一个
var img = findPicture(parentObj, ckRect);

//查找大控件
bigSize(classname);//d大于半个屏幕大小的类
bigSize(classname, 500);
```

### 3.3.6 按尺寸查找 
 ```js
findsize(list, chRect)  
//list控件数组， ckRect参考上面找图的参数

//过滤器
var uc = className("ImageView").filter(function(w){
	var b = w.bounds();
    return b.width() > 10; //宽度大于10的图片
});

```


### 3.3.6 其他 
```js 
//寻祖
parent(obj, 2) //祖父 
parent(obj, 3) //祖父的父亲

//查找本控件及所有子控件，并返回文本及控件
loopElementsText(obj, false);
loopElementsText(obj, true); //只返回文本
alltext(obj) //同上,只返回文本(压在一起了)

listChildren(obj)//返回数组，枚举每个子控件(及内部所有文本)
```


## 4. 窗口操作（设计的不好）

1. 启动app
```js
launchApp("浏览器");
```
2. 获取当前的包名
//如 com.tencent.mm
```js
currentPackage();
```

3. 等待窗口出现
```js
waitForActivity("*.launch.WwMainActivity", 4000);
//建议多使用，等待内容出现的函数 waitFor
```
4. (从深层堆栈中回退)一直点击返回键，让页面一直返回，直至遇到某窗口(# 有点费劲， 推荐使用click的回调功能)
```js
//参数：窗口类名/或数组，包名，超时时间，忽略其他窗口的存在；
//返回: 该类名（有点复杂，可以放弃，请参考4.2）
back2activity("*.launch.WwMainActivity");
back2activity("*.launch.WwMainActivity","com.tencent.wework");
back2activity("*.launch.WwMainActivity","com.tencent.wework", 12000);
back2activity("*.launch.WwMainActivity","com.tencent.wework", 12000, ["*.xxActivity","*.yyyActivity"]);
//也可以是数组，返回对应的类名
back2activity(["*.launch.WwMainActivity", "*.LaunchSplashActivity"]);
```
4.2 从深层堆栈中回退) 一直点击返回键，让页面一直返回，直至遇到某窗口
```js
//自动点击返回键，回调页面包含xx内容的窗口
//matchList可以为字符串，或数组， 回到包含该内容的页面，参考 matchContents函数
back2activityEx(matchList, timeout)

back2activityEx("消息 & 通讯录", 5000);
back2activityEx(["消息 & 通讯录", "已发邀请 | 添加"], 5000);
```

5. 等待窗口消失
```js
activityWaitGone("*.launch.WwMainActivity", 5000);
```
推荐使用waitGone('文字')


## 5. 按键
```js
home(); //桌面
back(); //返回
recents(); //任务列表
notifications(); //拉出通知栏
```

## 6. 其他

```js
sleep(1000);//暂停1秒
//
console.log("xxxxx"); //会同时打印到电脑,手机
console.show();  //打开悬浮日志窗口(日志同时会往这里输出)
//---
//用新的控制台
console.useNew(); //使用新的模式!!(使下面的这些功能)，老控制台则用这个访问console_old
console.useNew('patchs/log_layout.xml'); //指定layout
console.log2ide("xxxx"); //仅仅显示在电脑里
console.log2app("xxxx");  //仅仅显示在手机上
console.title('修改标题内容');
console.toast('修改标题内容');
toast('修改标题内容');

console.msg('覆盖日志区域内容');//内容可使用HTML，可作为自定义统计页
console.log('日志');
console.log('<b>日志</b>'); //内容可使用HTML
console.log('<font color=red>红色文字</font>'); //内容可使用HTML
console.info('蓝色提示');
console.warn('黄色警告');
console.verbose('低级日志');
console.error('红色警告');

console.error.color = '#DD0099';//设置颜色
console.error.color = 'red';//设置颜色
console.info.color = 'blue';
console.warn.color = 'yellow';
console.log.color = 'white';
console.verbose.color = 'gray';
 
console.resize(400, 400);  //设置日志浮窗 宽度，高度
console.resize(-1, 400);  //宽度为全屏
console.setPosition(0, 502);  //设置位置

console.window //浮窗对象 

//可以控制layout中的节点,比如:
console.window.resize(400, 400)
console.window.title.setText('hello') //设置文字
console.window.myid.setTextSize(20) //设置字体大小（请在自定义layout中加入这个myid节点）

console.setText(console.window.body, '<b>粗体文字</b>'); //添加html文字
console.addText(console.window.myid, '<font color=red>红色文字</font>'); //添加html文字

//设置日志本地存储
console.logfile ; 
console.setLogfile('/sdcard/1.txt');
console.removeLogfile();//删除log内容
console.getLog();//获取log内容

```



## 7. 手指操作
 

```js
//滑动
swipeUp() //上滑
swipeUp(800)  //800px

swipeTo('张三') //滑到某个内容
swipeTo(desc('张三')) 
swipeTo({key:'张三'}) //滑到某个内容
swipeTo({key,timeout,length,to}) //滑到某个内容

//
scrollTo('添加');
scrollUp(1)
scrollDown(0)
scrollForward()
scrollLeft()
scrollRight()

longClick(text[, i])
click(text[, i])


swipe(200, 1500, 200, 400, 500); //500毫秒
swipe(x1, y1, x2, y2, 500); //500毫秒
//连续滑动
gesture(1000, [0, 0], [500, 500], [500, 1000])
//多指连续滑动
gestures([delay1, duration1, [x1, y1], [x2, y2], ...], [delay2, duration2, [x3, y3], [x4, y4], ...], ...)

gestures([0, 500, [800, 300], [500, 1000]],
         [0, 500, [300, 1500], [500, 1000]]);
```


## 8. 截图, 找图 (原API，没有新增，老手请请忽略)
```js

//请求
requestScreenCapture(true);
//1,截图
var img = captureScreen(); 

var color = images.pixel(img, 100, 100);
//
toast(colors.toString(color));


//2,找到红色
while(true){
    var img = captureScreen();
    var point = findColor(img, "#ff0000");
    if(point){
        toast("找到红色，坐标为(" + point.x + ", " + point.y + ")");
    }
}

//3.
//指定找色区域(400, 500),宽为300,长为200的区域，色临界值 默认4 (范围:0-255) 
var point = findColor(img, "#00ff00", {
     region: [400, 500, 300, 200],
     threshold: 4
 });
 
 //4, 根据颜色找坐标
 var p = findColorEquals(captureScreen(), "#f64d30");
 
 //5. 找图片
 var img = images.read("/sdcard/大图.png");
 var template = images.read("/sdcard/小图.png");
 var p = findImage(img, template);
 if(p){
     toast("找到啦:" + p);
 }else{
     toast("没找到");
 }
 img.recycle();
 template.recycle();
 
 //在一定范围内找
 var p = findImage(captureScreen(), template, {
     region: [0, 50],
     threshold: 0.8 //相似度,默认值0.9
 });
 
 //
 var j=findImage(img, template, {
     region: [x, y, width, height],
     threshold: 0.8 //相似度
 })
 
 //找100个给给我
 var result = images.matchTemplate(img, template, {
     region: [x, y, width, height],
	 threshold:0.8,
	 max: 100
 });
 //result.matches 
```
.
.


## 9. 与安卓其他APP交互
### 发送广播
```js
 var it = {
	action: "APP_EVENT",
	extras: {
		'mydata': 'xxxxxxxx'  
	}
 };
app.sendBroadcast(it);
```
### 接收广播
```js
//找作者要补丁
app.broadcast.receiver('APP_EVENT', function(act, data, it, ctx) {
		console.log('接收数据: ') ;
		console.log(data.getString('mydata')) ;
})
```
## 参考[js与java+Android交互](java.html)的章节
