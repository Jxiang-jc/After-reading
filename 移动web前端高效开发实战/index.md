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

  - 