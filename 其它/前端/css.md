# CSS

定义页面样式。浏览器在构造完成页面的 DOM 树后，会解析 css 代码以及引入的 css 文件，将定义的样式表规则添加到对应的 DOM 树节点上。在绘制并渲染为用户可见的页面时使用。



---

## 选择器

```html
<div>
  <h1>title</h1>
  <p class="px" id="p1">first paragraph</p>
  <p class="px" id="p2">second paragraph</p>
  <a type="button" href="./next.html">next</a>
</div>

<style>
div { letter-spacing:0.5em; }               <!--简单选择器--> 
.px { font-size:20px; }                     <!--类选择器--> 
#p1 { color:brown; }                        <!--ID选择器--> 
[type="button"] { display:block; }          <!--属性选择器--> 
div h1 { font-weight:bold; }                
</style>
```

.important.warning {background:silver;}  （无空格隔开）同时是两个类的元素才触发样式（同时）

.important .warning {background:silver;}  （空格隔开）important 类中的 warning 类元素才触发样式（嵌套）


## 定位方式

1. 静态定位(static)：默认情况，元素从上到下依次放入 html 页面。
2. 相对定位(relative)：根据其正常位置偏移。
3. 绝对定位(absolute)：根据首个非静态父元素位置偏移。
4. 固定定位(fixed)：相对于浏览器窗口偏移，即使滚动窗口也不发生改变。

- `position:relative;` 设置元素为相对定位
- `position:absolute;` 设置元素为绝对定位
- `position:fixed;` 设置元素为固定定位

## 元素框

元素从外到内依次为：**外边距(margin) > 边框(border) > 内边距(padding) > 内容(content)**

**外边距**

1. 默认透明。
2. 上下两元素外边距取最大值：外边距重叠。
3. 可以取负值：相邻两元素重叠。

## 元素放置

1. **块级元素**
   
  独占一行，可任意规定宽高和内外边距大小。
  如 分隔符(div)、文本(h/p)、表格(table)、表单(form)等。 

2. **内联元素** 
   
  和其他内联元素分享一行，宽高随内容自动变化，不可设置上下边距大小。
  如 超链接(a)、图片(img)、输入框(input)等。

- `display:block` 设置为块级元素
- `display:inline` 设置为内联元素
- `display:none` 不摆放该元素

## 文本样式

`letter-spacing:0.5em;` 字符间距
`word-spacing:0.5em;` 单词间距（只对英文起作用）

## 表格样式

#### 边框

**table有外边距，但有无内边距视情况而定：**

`border-collapse:separate;`

【默认情况】表格内单元格(td)分散，表格内边距(padding)有效。

`border-collapse:collapse;`

【常用情况】表格内单元格(td)紧挨，且单元格和表格边框紧密贴合：表格内边距(padding)无效。

`border-space:0;`

单元格分散情况下调整单元格间距。设为0时可达到单元格紧挨效果。

【不绘制单元格框线，且需要设置表格内边距时可使用。】

**tr和td/th作为内部元素无外边框。**

#### 局部元素

tr：对 table 的指定行采用不同样式

`table tr:nth-child(odd){ background: #eeeff2; }`  //奇数行元素
`table tr:nth-child(even){ background: #dfe1e7; }`  //偶数行元素

td：对 table 的指定列采取不同样式

`tr td:nth-child(-n+3){ text-align:center; }`    //第3列以前的全部元素
`tr td:nth-child(n+4){ text-align:center; }`    //第4列以后的全部元素

### 特殊技巧

放在页面中央

  left:50%;
  top:50%;
  transform: translate(-50%,-50%);
