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



