## h5 新增语义化

![新的语义化](D:\Jxiang\After reading\移动web前端高效开发实战\img\1.jpeg)

``` html
<body>
    <header></header> 
    <nav></nav>
    <div>
        <article>
        	<section></section>
        </article>
        <aside></aside>
    </div>
    <footer></footer>
</body>
```

- Header 表示介绍内容的容器或者一组导航链接
- Nav 标签的内容主要用于导航
- Article 表示页面中的主体内容。
- Section 用来标记页面上重要的部分
- Aside 表示和页面主要内容相关，但不是页面的一部分，经常表示一个相关链接
- Footer 和 Header 相对，表示文档或者章节的页脚



## h5 新元素和特殊属性 contenteditable

- progress 元素表示进度条

  ```html
  <progress value="30" max="100"></progress>
  ```

  

- Meter 元素表示标尺

  - Meter 元素包含3个属性：max：最大值，默认为1； min：最小值，默认为0； value： 标尺的值

  ```html
  <meter value="3" min="0" max="10">3/10</meter><br>
  <meter value="0.6">60%</meter>
  ```

  

- contenteditable，通过该属性，可以让一个普通元素可编辑。相当于一个简单的编辑器``

```html
<p contenteditable="true">这里的内容是可以编辑的</p>
```



## 调用摄像头拍照

>  `html5`的`getUserMdia API`提供了访问用户媒体设备的能力，基于该特性，开发者可以在不依赖任何浏览器插件的条件下访问视频和音频等设备。

- 1. `getUserMedia API`
     
     - `getUserMedia API` 起初的版本是 `navigator.getUserMedia`, 目前以从最新Web标准中废除
  
     - 最新的标准为 `navigator.mediaDevices.getUserMedia`, 但浏览器支持情况不如旧版`API`普及
  
  ```js
  // 部分浏览器需要使用特定前缀（webkit），例如在Firefox中，方法为 navigator.mozGetUserMedia
  if (navigator.mediaDevices.getUserMedia || navigator.getUserMedia || navigator.webkitGetUserMedia || navigator.mozGetUserMedia) {
      // 调用用户媒体设备
  } else {
      alert('您的浏览器不支持访问用户媒体设备！')
}
  ```
  
  - 旧版 `getUserMedia`语法: `getUserMedia(constraints, successCallback, errorCallback)`
  
  - 新版 `getUserMedia`语法：`getUserMedia(constraints).then(successCallback).catch(errorCallback)` 
  
    - 参数 `constraints` 指定请求的媒体类型，主要包含 `vedia` 和 `audio`，例如请求不带任何参数的视频和音频，代码如下
  
    `{ video: true, audio: true }`
  
    ​	- 可指定视频分辨率，代码如下
  
    `{ video: {width: 640, height: 360} }`
  
    ​    - 移动设备上，可指定使用前置摄像头，代码如下
  
    `{ video: {facingMode: 'user'} }`
  
    ​    - 调用后置摄像头，代码如下
  
    `{ video: { facingMode: { exact: 'enviroment' } } }`
  
    - 成功回调函数 `successCallback` 的参数 `stream`, 为 `MediaStream` 对象，表示媒体内容的数据流, 可以通过 `URL.createObjectURL` 转换后设置 `Vedio` 或 `Audio` 元素的 `src` 属性来使用, 部分较新的浏览器可以直接设置 `srcObject`属性来使用。
    - 失败回调函数 `errorCallback` 的参数 `error`, 其中 `name` 属性的值参考一下表格
    
    |        错误值        |     错误名称     |
    | :------------------: | :--------------: |
    |      AbortError      |     中止错误     |
    |   NotAllowedError    |     拒绝错误     |
    |    NotFoundError     |    找不到错误    |
    |   NotReadableError   |   无法读取错误   |
    | OverConstrainedError | 无法满足要求错误 |
    |    SecurityError     |     安全错误     |
    |      TypeError       |     类型错误     |
  
  

```js
navigator.mediaDevices.getUserMedia({ video: true})
    .then(function(stream) {
    	// 成功获取媒体流
	})
    .catch(function(error) {
    	// console.log('获取用户媒体失败', + error.name)
	})
```



- 2. 调用摄像头拍照实例

  ```html
  <!-- video 用于显示媒体设备的是视频流，自动播放 -->
  <video id="video" autopla style="width: 480px; height: 320px;"></video>
  <div>
      <!-- 拍照按钮 -->
      <button id="capture">
          拍照
      </button>
  </div>
  <!-- 描绘video截图 -->
  <canvas id="canvas" width="480" height="320"></canvas>
  ```

  ```js
  // 以下方法在本地非localhost无法运行，是因为 https 的原因。浏览器出于安全着想
  //访问用户媒体设备的兼容方法
  function getUserMedia(constraints, success, error) {
      if (navigator.mediaDevices.getUserMedia) {
          //最新的标准API
          navigator.mediaDevices.getUserMedia(constraints)
              .then(success)
              .catch(error);
      } else if (navigator.webkitGetUserMedia) {
          //webkit核心浏览器
          navigator.webkitGetUserMedia(constraints, success, error)
      } else if (navigator.mozGetUserMedia) {
          //firfox浏览器
          navigator.mozGetUserMedia(constraints, success, error);
      } else if (navigator.getUserMedia) {
          //旧版API
          navigator.getUserMedia(constraints, success, error);
      }
  }
  
  let video = document.getElementById('video');
  let canvas = document.getElementById('canvas');
  let context = canvas.getContext('2d');
  
  function success(stream) {
      //兼容webkit核心浏览器
      let CompatibleURL = window.URL || window.webkitURL;
      //将视频流设置为video元素的源
      console.log(stream);
  
      //video.src = CompatibleURL.createObjectURL(stream); // 浏览器已取消, 用下面的取代
      video.srcObject = stream;
      video.play();
  }
  
  function error(error) {
      console.log(`访问用户媒体设备失败${error.name}, ${error.message}`);
  }
  
  if (navigator.mediaDevices.getUserMedia || navigator.getUserMedia || navigator.webkitGetUserMedia || navigator.mozGetUserMedia) {
      //调用用户媒体设备, 访问摄像头
      getUserMedia({
          video: {
              width: 480,
              height: 320
          }
      }, success, error);
  } else {
      alert('不支持访问用户媒体');
  }
  
  document.getElementById('capture').addEventListener('click', function() {
      context.drawImage(video, 0, 0, 480, 320);
  })
  ```



## 手机上实现摇一摇

- 方向事件和移动事件

  - 方向事件 `deviceorientation`

    - `deviceorientation` 事件是在设备方向发生变化时触发, 使用方法如下

    `window.addEventListener('deviceorientation', orientationHandler, true)`

    - 回调函数 `orientationHandler` 在注册后，会被定时调用，并收到一个 `DeviceOrientationEvent`类型参数，通过该参数获取设备的方向信息

    

| 属性名       | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| absolute     | 如果方向数据跟地球坐标系和设备坐标系有差异，则为true，如果方向数据由设备本身的坐标系提供，则为false |
| alpha（z轴） | 设备在Alpha方向上旋转的角度，范围为0 ~ 360°                  |
| beta（x轴）  | 设备在Beta方向上旋转的角度，范围为-180 ~ 180°                |
| gamma（y轴） | 设备在Gamma方向旋转的角度，范围为-90 ~ 90°                   |

- 移动事件 `devicemotion`

  - `devicemotion`事件在设备位置发生变化时触发，使用方法如下：
    - `window.addEventListener('devicemotion', motionHandler, false)`
    - 回调函数 `motionHandler`在注册之后，会被定时调用，并会收到一个 `DeviceMotionEvent`类型参数，通过该参数可以访问设备的方向和位置信息，

  

| 属性名                       | 说明                                                  |
| ---------------------------- | ----------------------------------------------------- |
| acceleration                 | 设备在x、y、z三个轴的方向上移动的距离，已抵消重力加速 |
| accelerationIncludingGravity | 设备在x、y、z三个轴的方向上移动的距离，包含重力加速   |
| rotationRate                 | 设备在Alpha、Beta、Gamma三个方向旋转的角度            |
| interval                     | 从设备获取数据的频率，单位是毫秒                      |



> 摇一摇实例

- 摇一摇这个动作是指手机在一定时间内移动了一定的距离，也可以近似理解为手机以不低于一定的速度移动。
- 实现的方法是在监听 `devicemotion` 事件后，判断设备在 `x、y、z` 三个方向上移动的距离与前一次移动的距离差，并除以两次事件触发的事件差，即为设备移动的速度。
- 将这个速度与预先设定的速度比较，如果大于预先设定值，则认为设备发生摇动。
- 这个预先设定的速度可以进行多次测试之后，取起平均值



```html
<div>用力摇一摇你的手机</div>
```

```js
var SHAKE_SPEED_THRESHOLD = 300; // 摇动速度阈值
var lastTime = 0; // 上次变化的事件
var x = y = z = lastX = lastY = lastZ = 0; // 位置变量初始化
function motionHandler(evt) {
    // 取得包含重力加速度的位置信息
    var acceleration = evt.accelerationIncludingGravity;
    // alert(acceleration)
    var curTime = Date.now(); // 取得当前时间
    if ((curTime - lastTime) > 120) { // 判断
        // alert(123)
        var diffTime = curTime - lastTime; // 两次变化时间差
        lastTime = curTime; // 保存此次变化的时间
        x = acceleration.x;
        y = acceleration.y;
        z = acceleration.z;
        // 计算摇动的速度
        var speed = Math.abs(x + y + z - lastX - lastY - lastZ) / diffTime * 1000;

        if (speed > SHAKE_SPEED_THRESHOLD) { // 判断是否大于预设速度
            alert('你摇动了手机');
        }
        lastX = x; // 保存此次变化的位置X
        lastY = y; // 保存此次变化的位置y
        lastZ = z; // 保存此次变化的位置z
    }
}

if (window.DeviceMotionEvent) {
    alert('请开始你的表演')
    window.addEventListener('devicemotion', motionHandler, false);
} else {
    alert('您的设备不支持位置感应');
}
```



## 利用IndexDB实现便签管理

> `IndexedDB`是一个事务型数据库系统，同时也是一个基于`Javascript`的面向对象数据库系统。
>
> `IndexedDB`为开发者再本地储存信息提供了另一种可能性。与`Localstorage`和`sessionStorage`不同的是，`IndexedDB`可以存储大量结构化的数据，并且使用基于索引的高效`API`检索。

```html
<!-- 创建一个便签容器 -->
<div class="notes">
	<!-- 添加按钮 -->
    <div class="add">
        <p class="ic_add">+</p>
        <p>添加便签</p>
    </div>
</div>
<!-- 为了简化代码，基于jQuery开发 -->
<script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.js"></script>
<!-- 操作indexedDB的帮助类 -->
<script src="indexddb.js"></script>
<script>
	// 预先定义每一个便签的HTML代码
    var divstr = `<div class="note"><a class="close">X</a><textarea></textarea></div>`;
    // 实例化一个便签数据库、数据表
    var db = new LocalDb('db1', 'notes');
    // 打开数据库
    db.open(function () {
        // 页面初始化时，获取所有已有便签
        db.getAll(function (data) {
            var div = $(divstr);
            div.data('id', data.id);
            div.find('textarea').val(data.content);
            // 将便签插入添加按钮前边
            div.insertBefore(add);
        });
    });
    
    // 为添加按钮注册单击事件
    var add = $('.add').on('click', function () {
        var div = $(divstr);
        div.insertBefore(add);
        // 添加一条空数据到数据库
        db.set({content: ''}, function (id) {
            // 将数据库生成的自增id赋值到便签上
            div.data('id', id);
        });
    });
    
    // 监听所有便签编辑域的焦点事件
    $('.notes').on('blur', 'textarea', function () {
        var div = $(this).parenet();
        // 获取该便签的id和内容
        var data = { id: div.data('id'), content: $(this).val() };
        // 写入数据库
        db.set(data);
    })
  	// 监听所有关闭按钮的单击事件
    .on('click', '.close', function () {
        if(confirm('确定删除此便签吗？')) {
    		var div = $(this).parent();
            // 删除这条便签数据
            db.remove(div.data('id'));
            // 删除便签DOM元素
            div.remove();
        }
   	})
</script>
```

> 为了便于维护，`IndexedDB`操作的逻辑单独封装

```js
// 声明一个数据库操作的构造函数
function LocalDB(dbName, tableName) {
    this.dbName = dbName;
    this.tableName = tableName;
    this.db = null;
}
// 在原型链上注册open方法， 完成打开数据库的操作
LocalDB.prototype.open = function (callback) {
    var _this = this;
    // 打开数据库的动作
    var request = window.indexedDB.open(_this.dbName);
    // 打开成功后的回调
    request.onsuccess = function (event) {
        // 获取打开结果； 数据库实例
        _this.db = request.result;
        // 如果调用方有回调函数，就执行回调函数
        callback && callback();
    };
    
    // 第一次创建数据库时触发该事件
    request.onupgradeeneeded = function (event) {
        // 获取数据库实例
        var db = request.result;
        // 检查是否存在指定的表
        if (!db.objectStoreNames.contains(_this.tableName)) {
            // 如果不存在，则创建，并指定一个自增的id作为查询数据
            db.createObjectStore(_this.tableName, {
                keyPath: 'id',
                autoIncrement: true
            });
        }
    };
}

// 获取数据表的实例
LocalDB.prototype.getStore = function () {
    var transaction = this.db.transaction(this.tableName, 'readwrite');
    var objStore = transaction.objectStore(this.tableName);
    return objStore;
}

// 保留一条数据：支持添加和修改
LocalDB.prototype.set = function (data, callback) {
    var objStore = this.getStore();
    var request = data.id ? objStore.put(data) : objStore.add(data);
    request.onsuccess = function (event) {
        callback && callback(event.target.result);
    };
}

// 获取一条数据
LocalDB.prototype.get = function (id, callback) {
    var objStore = this.getStore();
    var request = objStore.get(id);
    request.onsuccess = function (event) {
        callback && callback(event.target.result);
    }
}

// 获取表中所有的数据
LocalDB.prototype.getAll = function (callback) {
    var objStore = this.getStore();
    // 打开数据游标
    var request = objStore.openCursor();
    request.onsuccess = function (event) {
        var cursor = event.target.result;
        if (cursor) {
            // 如果游标存在，执行回调并传入当前数据行
            callback && callback(cursor.value);
            // 继续下一行数据
            cursor.continue();
        }
    }
}

// 删除一条数据
LocalDB.prototype.remove = function (id) {
    var objStore = this.getStore();
    objStore.delete(id);
}

/**
	以上代码包含如下常用的数据操作
	 - 打开数据库： 如果指定数据不存在，系统会自动创建
	 - 创建数据表： 在首次打开数据库时检测表是否存在，不存在则创建
	 - 保存一条数据： 通过数据表对象的 put 和 add 方法实现
	 - 获取一条数据： 利用索引获取指定id的数据行
	 - 删除一条数据： 可以直接通过数据表对象的delete方法删除一条数据
**/
```

- 注意： `IndexedDB`所有`API`操作均为异步模式，需要通过回调函数来获取结果。



## flex布局

- Flex容器属性

| 属性名称        | 说明                                                         | 可选值                                                       |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| flex-direction  | 决定主轴的方向（项目的排列方向）                             | row、row-reverse、column、column-reverse                     |
| flex-wrap       | 默认情况下，项目都排在轴线上，如果超出一行，该属性定义如何换行 | nowrap、wrap、wrap-reverse                                   |
| justify-content | 定义项目在主轴上的对齐方式                                   | flex-start、flex-end、center、space-between、space-around    |
| align-items     | 定义项目在交叉轴上的对齐方式                                 | flex-start、flex-end、center、baseline、stretch              |
| align-content   | 定义了多根轴线的对齐方式，如果项目只有一根轴线，该属性不起作用 | flex-start、flex-end、center、space-between、space-around、stretch |

- Flex项目包含的属性

| 属性名称    | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| order       | 定义项目的排列顺序、值越小、排列越靠前，默认值为0            |
| flex-grow   | 定义项目的放大比例，默认值0                                  |
| flex-shrink | 定义项目的缩小比例，默认为1                                  |
| flex-basic  | 定义了在分配剩余空间之前，项目章句的主轴空间，浏览器根据这个属性值，计算主轴的剩余空间，默认值“auto”，即项目的实际大小 |
| align-slef  | 设置单个项目的对齐方式，可覆盖Flex容器设置的align-items属性。可选值: auto, flex-start, flex-end, center, baseline, stretch |

​	



## 背景（backgrounds）

- `background-color`: 规定要使用的背景颜色
- `backgruond-position`: 规定背景图像的位置
  - `padding-box | border-box | content-box`
- `background-size`: 规定背景图片的尺寸
  - `length`: 设置背景图像的高度和宽度。第一个值宽度，第二个高度。如果只设置一个值。则第二个值为 `auto`
  - `percentage`: 以父元素的百分比来设置背景图像的宽度和高度。第一个值宽度。第二个高度。如果只设置一个值。则第二个值为 `auto`
  - `cover`：把背景图像扩展至足够大，以使背景图像完全覆盖背景区域。背景图像的某些部分也许无法显示在背景定位区域中
  - `contain` 把图像扩展至最大尺寸，以使其宽度和高度完全适应内容区域。
- `background-repeat`: 规定如何重复背景图像
- `background-origin`: 规定背景图片的定位区域
- `background-clip`: 规定背景的绘制区域
  - `border-box | padding-box | content-box`
- `background-attachment`: 规定背景图像是否固定或者随着页面的其余部分滚动。
- `background-image`: 规定要使用的背景图像 

> background的语法规则如下：

`background: #000 url("图片路径") no-repeat left top`





## Less使用

- 变量

> Less的变量通过"@"符号作为标记

```css
@primaryColor: #58bc58;
h1 { color: @primaryColor; }
.title { color: @primaryColor; }

/* 生成的css代码如下： */
h1 { color: #58bc58; }
.title { color: #58bc58; }
```

> Less允许插值变量

```css
@mySelector: title;
.@{mySelector} {
    color: #58bc58;
    font-size: bold;
    line-height: 40px;
}

/* 生成的css代码如下： */
.title {
    color: #58bc58;
    font-size: bold;
    line-height: 40px;
}

/* Less中的变量只能定义一次， 所以可以被认为是编程语言中的常量 */
```



- 嵌套

```css
// 用嵌套展示清晰的逻辑关系
#header {
    display: inline;
    float: left;
    h1 {
        font-size: 24px;
        font-weight: bold;
        a { color: #58bc58; }
    }
}

/* "&" 运算符表示父选择器 */
/* 应用伪类 */
a {
    color: #ccc;
    &:hover { color: #f00; }
}

/* 生成的css代码如下：*/
a { color: #ccc; }
a:hover { color: #foo; }

/* 生成重复的类名 */
.button {
    &-ok { background: green; }
    &-warn { background: yellow; }
    &-danger { background: red; }
}

/* 生成的css代码如下：*/
.button-ok { background: green; }
.button-warn { background: yellow; }
.button-danger { background: red; }
```



- 作用域

> Less的作用域主要体现在局部变量和全局变量之上， 即查找变量的顺序是先在局部定义中，如果找不到，则会向上一级，直到全局

```css
@primaryColor: #58bc58;
#header {
    @primaryColor: #333;
    h1 { color: @primaryColor; } // color值为#333
}
#footer { color: @primaryColor; } // color值为#58bc58
```



- 混合

> ​	Less中可以将任意的"类"选择器和"id"选择器混合到另一个选择器中，而被嵌入的这个选择器也可以被视为一个变量。被混入的部分在Less中称为混合集，这个做法可以减少重复的属性书写，也可以视为Less提供的一种抽象方法
>
> ​	选择器作为混合集是Less灵活且接近CSS语法的体现，即混合集并非特殊的存在。但有些情况下开发者并不想将混合集的内容输出到最终的CSS的样式中。Less提供了一种解决方案，在混合集的后面加上一对括号。这种混合集可以被视为一个函数声明，同时这种混合集可以接受参数，提供更好的复用功能



```css
/* 定义一个规则，并且不设置默认参数 */
.borderRadius(@radius) {
    -moz-border-radius: @radius; // 兼容firefox
    -webkit-border-radius: @radius; // 兼容chrome
	border-radius: @radius; 
}
/* 应用到元素中 */
#header { .borderRadius(10px); } // 将10px传给变量@radius
.btn { .borderRadius(3px); }

/* 生成的css代码如下：*/
#header {
    -moz-border-radius: 10px;
    -webkit-border-radius: 10px;
	border-radius: 10px; 
}
.btn {
    -moz-border-radius: 3px;
    -webkit-border-radius: 3px;
	border-radius: 3px; 
}
```



- 约束与循环

```css
.generate-column(4);
.generate-coloumn(@n, @i:1) when(@i <= @n) { // n, i 是mixin的两个参数，i <=n 是条件
    .column-@(i) {						   // 用i作为插值变量
        width: (@i * 100% / @n);				// 运算生成width的宽度
    }
.generate-column(@n, (@i + 1));				// 将i + 1递归调用自身
}

/* 生成的css代码如下：*/
.column-1 {width: 25%;}
.column-1 {width: 50%;}
.column-1 {width: 75%;}
.column-1 {width: 100%;}
```



- 导入

> ​	导入功能在原生CSS就有相应的实现，但在实际开发过程中并不推荐开发者使用。原生CSS通过"@import"关键字实现导入功能存在的两大弊端，首先，"@import"导入规则必须于先于除了"@charset"外的其他CSS规则执行。其次，导入的文件需要等待引用其父文件下载解析才被执行，如此，丢失了CSS并行下载的能力，加大了资源加载的开销
>
> ​	Less提供的导入方法是在编码阶段被引入的，在最终代码中合并成单个CSS文件。除此之外，还提供了导入选项来更灵活地引入第三方文件



## 面向对象

- 原型和原型链

> 在javaScript中每个对象都有一个指向其原型对象的内部引用，而这个原型对象又通过引用指向其原型，直到某个对象的原型为null，这种一级一级的链结构就被称为原型链

> 首先，原型：`__proto__`属性是对象所独有， `prototype`属性是函数所独有。当我们想访问一个对象上某个属性时，如果在对象内部找不到，它会往`__proto__`属性所指向的那个对象（父对象）找，一直找，知道`__proto__`属性的终点`null`，就会返回 `undefinded`。通过`__proto__`属性将对象链接起来的这条链路就时原型链

- 创造对象和生成原型链

  - （1）使用字面量创造对象

  `var o = {a: 1};`

  - (2) 使用构造器创造对象

  ```js
  function Person (name) {
      this.name = name || 'Jxiang';
  };
  var p = new Person('小张');
  ```

  - （3）使用Object.create创造对象

    > 该方法是ES5新增，可以通过一个对象原型创造一个新的对象，新对象原型即为使用create方法传入的第一个参数。

    `Object.create(prototype, descriptors)`

- ECMAScript6的 Class 和 Extends

```js
class People {				// 定义一个类People
    constructor (name) {	// constructor函数，必须存在，接受实例化参数
        this.name = name;
    }
    getName () {			// 类的属性
        console.log(this.name)；
    }
}
var p = new People('Jxiang')； // 实例化一个People类
p.getName()					 // 调用实例getName方法，输出Jxiang
```

```js
// class 中可以进行静态声明， 添加static关键字即可
class People {
    static sayHello () {
        console.log('hello world');
    }
}
People.sayHello() // 不需要实例化，直接用类调用静态方法
```

```js
// Extends 关键字配合 Class实现继承
class Student extends People {
    constructor (name, grade) {			// 声明constructor
     	super(name); 				   // 继承父类的this对象
        this.grade = grade;
    }
    getGrade () {					   // Student类的属性 
        console.log(this,grade);
    }
}
var s = new Student('Jxiang', 18);		// 实例化Student类
s.getName();						  // 调用继承的属性，输出 'Jxiang'
s.getGrade();						  // 调用Student类的属性，输出18
```



## 仿原生相册

- 在 `Header`标签添加 `Viewport`

`<meta name="viewport" content="width=device-width, initial-scale=1">`

- 在`Body`元素中添加小相片列表

```html
<div class="gallery">
    <div class="item">
        <img src="images/1.jpg">
    </div>
    <div class="item">
        <img src="images/2.jpg">
    </div>
</div>
```

- 添加相册样式

```css
.gallery {
    width: 100px;				  /* 设置相册的狂赌为屏幕宽度 */
    display: flex;				  /* 相册采用flex布局 */
    flex-flow: row wrap;		   /* 相册每一项为横向排列，并且换行 */
}
.gallery .item { flex: 1; }       	/* 每一项平均排列 */
.gallery .item img { width: 33vw; } /* 图片宽度始值为1 / 3 屏幕宽度 */

/* 添加预览模式下的效果 */
.gallery.preview .item {
    display: flex;    			    /* 对子项设置为flex布局 */  
    margin: auto;				   /* 设置margin为auto实现图片居中显示 */
}
.gallery.preview .item img {
    max-width: 100vw;				/* 设置预览图片的最大宽度为屏幕宽度 */ 
    max-height: 100vh;				/* 设置预览图片的最大高度为屏幕高度 */ 
    width: initial;					/* 初始化图片宽度，覆盖之前设置的宽度 */
}
/* 禁止浏览器默认的触摸行为 */
.gallery.preview {
    touch-action: none;
}
```

- 监听相册`Click`事件，用户点击相片时，将相册切换到预览模式。

```js
var $gallery = document.querySelector('.gallery');
var itemsLength = document.querySelectorAll('.item').length;
$gallery.addEventListener('click', function (e) {
    // 监听单击事件，切换相册的css class来实现预览和普通模式的切换
    var classList = $galler.classList,
        css_preview = 'preview';
    if (classList.contains(css_preview)) {
        classList.remove(css_preview);
        // 在非预览模式下，相册的宽度为100vw
        $gallery.style.width = 100 + 'vw';
    } else {
        classList.add(css_preview);
        // 在预览模式下，所有的图片横着排成一排，相册的总宽度为每一个项目长度总和
        $gallery.style.width = 100 * itemsLength + 'vw';
    }
})
```

- 通过监听touchstrat、touchmove和touchend事件来实现滑动手势功能，在touchstart事件中，记录当前手指的位置

```js
$gallery.addEventListener('touchstart', function (e) {
    // 触摸开始时，记住当前手指的位置
    startOffsetX = e.changeTouches[0].pageX;
    // 手指滑动的时候，禁止动画
    $gallery.classList.remove('animation');
});
```

- 在 `touchmove`事件中，使图片随着手指拖曳移动

```js
$gallery.addEventListener('touchmove', function (e) {
    isTouchstart = true;
    // 计算手指的水平移动量
    var dx = e.changeTouches[0].pageX - startOffsetX;
    // 调用move方法，设置gallery元素的transform,移动图片
    move(currentTranX + dx);
})
```

- 图片位置移动采用css3的transform属性实现

```js
function (dx) {
    $gallery.style.transform = "translate(" + dx + "px, 0)";
}
```

- 在`touchend`事件中，判断手指移动的范围是否超过图片宽度的一半，如果达到一半，则切换图片，否则还原图片的位置

```js
$gallery.addEventListener('touchend', function (e) {
    if (isTouchstart) {
        // 在移动图片的时候，需要动画，动画采用css3的transition实现
        $gallery.classList.add('animation');
        var dx = e.changeTouches[0].pageX - startOffsetX; // 计算偏移量
        // 如果偏移量超出gallery宽度的一半
        if (Math.abs(dx) > width / 2) {
            // 处理临界值
            if (currentTranX <= 0 && currentTranX > -width * itemsLength) {
                if (dx > 0) {						// 如果手指向右滑动
                    if (currentTranX < 0) {			 // 如果图片不是显示第一张
                        currentTranX = currentTranX + width;
                    }
                } else if (currentTranX > -width * (itemsLength - 1)) {
                    // 如果手指向右滑动，并且当前图片不是显示之后一张
                    currentTranX = currentTranX - width;
                }
            }
        }
        // 如果为超出图片的宽度一半，拖曳停止，将图片的位置还原
        // 如果超出了图片宽度的一半，将切换到上一张或者下一张
        move(currentTranX);
    }
    isTouchstart = false;
})
```



## 用Canvas制作刮刮卡

- 原理： 在中奖或者未中奖两张图片中随机出现一张，然后在图片上方用Canvas绘制一个灰色蒙层，同时监听鼠标或者手势移动来绘制一个透明图形，这样就能看到蒙层下方真实的图片，从而实现刮刮卡的效果

```html
<!DOCTYPE html>
<html lang="zh">
<head>
	<meta charset="UTF-8">
    /* 设置视口宽度、初始缩放 */
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<meta http-equiv="X-UA-Compatible" content="ie=edge">
	<title>刮刮卡</title>
    <style type="text/css">
    	/* 设置容器的大小和位置 */
        .container {
            width: 320px;
            margin: 10px auto 20px auto;
            min-height: 300px;
        }
    </style>
</head>
<body>
	<div class="container">
        <canvas></canvas>
    </div>
</body>
</html>
```



```js
// 为了提升用户体验，在拖动时禁止选择文本行为，通过禁用鼠标选择事件实现
var bodyStyle = document.body.style;
bodyStyle.mozUserSelect = 'none';
bodyStyle.webkitUserSelect = 'none';
```

```js
var img = new Image();							// 实例化一个图片类
var canvas = document.querySelector('canvas');	  // 拿到canvas的DOM对象
canvas.style.backgroundColor = 'transparent';	  // canvas的背景为透明
canvas.style.position = 'absolute';				 // canvas的定位方式为绝对定位
var imgs = ['bg-01.jpg', 'bg-02.jpg'];			 // 中奖和未中奖两个图片
var num = Math.floor(Math.random()*2);			 // 随机生成0或1
img.src = imgs[num];						    // 图片的实例对象设置图像url 
```

```js
// 进入主体部分，等待图片加载完成后，定义初始化的属性和事件函数，蒙层通过layer方法绘制。当按下鼠标或者单机手势时，获取当前坐标信息，并绘制透明小圆点
img.addEventListener('load', function (e) {
    var ctx;
    var w = img.width,				// 获取图片宽度
        h = img.height;				// 获取图片高度
    var offsetX = canvas.offsetLeft,	 // 获取canvas相对于左边界的偏移
        offsetY = canvas.offsetTop;	 // 获取canvas相对于上边界的偏移
    var mousedown = false;			// 防止手势操作画出手机边界
    function layer (ctx) {		 	// 绘制蒙层
        ctx.fillStyle = 'gray';		// 蒙层颜色
        ctx.fillRect(0, 0, w, h); 	// 蒙层的位置和大小
    }
    
    function eventDown (e) {
        e.preventDefault();			// 鼠标或者手势按下时触发的事件回调
        mousedown = true;
    }
    function eventUp (e) {
        e.preventDefault();			// 鼠标或手势松开时触发的事件回调
        mousedown = false;
    }
    function eventMove (e) {
        e.preventDefault();		// 鼠标或手势移动时触发的事件回调
        if (mousedown) {
            // 拿到TouchList对象中最后一个事件对象
            if (e.changedTouches) {
                e = e.changedTouches[e.changedTouches.length-1];
            }
            // 获得当前鼠标或者手势的坐标
            var x = (e.clientX + document.body.scrollLeft || e.pageX) - offsetX || 0,
                y = (e.clientY + document.body.scrollTop || e.pageY) - offsetY || 0;
            ctx.beginPath();					// 创建一个新的路径
            ctx.arc(x, y, 20, 0, Math.PI * 2);	  // 绘制弧线
            ctx.fill();							// 填充路径
        }
    }
    
    canvas.width = w;
    canvas.height = h;
    canvas.style.backgroundImage = 'url(' + img.src + ')';
    canvas.style.backgroundSize = 'cover';
    ctx = canvas.getContext('2d');
    ctx.fillStyle = 'transparent';
    ctx.fillRect(0, 0, w, h);
    layer(ctx);											 // 绘制蒙层
    ctx.globalCompositeOperation = 'destination-out';		// 路径与原图像重叠部分透明
    canvas.addEventListener('touchstart', eventDown);
    canvas.addEventListener('touchend', eventUp);
    canvas.addEventListener('touchmove', eventMove);
    canvas.addEventListener('mousedown', eventDown);
    canvas.addEventListener('mouseup', eventUp);
    canvas.addEventListener('mousemove', eventMove);
})；
```

