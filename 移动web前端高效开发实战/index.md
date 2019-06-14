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

  

  

