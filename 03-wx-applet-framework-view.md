#微信小程序框架--视图层

##五.视图层
框架的视图层由 WXML 与 WXSS 编写，由组件来进行展示。

将逻辑层的数据反应成视图，同时将视图层的事件发送给逻辑层。

WXML(WeiXin Markup language)用于描述页面的结构。

WXSS(WeiXin Style Sheet)用于描述页面的样式。

组件(Component)是视图的基本组成单元。



### WXML

WXML（WeiXin Markup Language）是框架设计的一套标签语言，结合[基础组件](../../component/index.md?t=1474644091286)、[事件系统](event.html?t=1474644091286)，可以构建出页面的结构。

用以下一些简单的例子来看看 WXML 具有什么能力：

#### [数据绑定](data.html?t=1474644091286)
```xml
<!--wxml-->
<text> {{message}} </view>
```

```javascript
// page.js
Page({
  data: {
    message: 'Hello MINA!'
  }
})
```

### [列表渲染](list.html?t=1474644091286)

```xml
<!--wxml-->
<view wx:for="{{array}}"> {{item}} </view>
```

```javascript
// page.js
Page({
  data: {
    array: [1, 2, 3, 4, 5]
  }
})
```
### [条件渲染](conditional.html?t=1474644091286)
```xml
<!--wxml-->
<view wx:if="{{view == 'WEBVIEW'}}"> WEBVIEW </view>
<view wx:elif="{{view == 'APP'}}"> APP </view>
<view wx:else="{{view == 'MINA'}}"> MINA </view>
```

```javascript
// page.js
Page({
  data: {
    view: 'MINA'
  }
})
```
### [模板](template.html?t=1474644091286)
```xml
<!--wxml-->
<template name="staffName">
  <view>
    FirstName: {{firstName}}, LastName: {{lastName}}
  </view>
</template>

<template is="staffName" data="...staffA"></template>
<template is="staffName" data="...staffB"></template>
<template is="staffName" data="...staffC"></template>
```

```javascript
// page.js
Page({
  data: {
    staffA: {firstName: 'Hulk', lastName: 'Hu'},
    staffB: {firstName: 'Shang', lastName: 'You'},
    staffC: {firstName: 'Gideon', lastName: 'Lin'}
  }
})
```
### [事件](event.html?t=1474644091286)
```xml
<view bindtap="add"> {{count}} </view>
```

```javascript
Page({
  data: {
    count: 1
  },
  add: function(e) {
    this.setData({
      data: this.data.count + 1
    })
  }
})
```


##六 数据绑定

WXML 中的动态数据均来自对应 Page 的 data。

### 简单绑定

数据绑定使用 Mustache 语法（双大括号）将变量包起来，可以作用于：

#### 内容
```xml
<view> {{ message }} </view>
```
```javascript
Page({
  data: {
    message: 'Hello MINA!'
  }
})

```

 ### 组件属性(需要在双引号之内)
```xml
<view id="item-{{id}}"> </view>
```
```javascript
Page({
  data: {
    id: 0
  }
})

```

### 控制属性(需要在双引号之内)
```xml
<view wx:if="{{condition}}"> </view>
```
```javascript
Page({
  data: {
    condition: true
  }
})
```

### 运算

 可以在 `{{}}` 内进行简单的运算，支持的有如下几种方式：

#### 三元运算

```xml
<view hidden="{{flag ? true : false}}"> Hidden </view>
```


#### 算数运算
```xml
<view> {{a + b}} + {{c}} + d </view>
```
```javascript
Page({
  data: {
    a: 1,
    b: 2,
    c: 3
  }
})
```
view中的内容为 `3 + 3 + d`。

#### 逻辑判断
```xml
<view wx:if="{{length > 5}}"> </view>
```
#### 字符串运算
```xml
<view>{{"hello" + name}}</view>
```
```javascript
Page({
  data:{
    name: 'MINA'
  }
})
```
###组合

也可以在 Mustache 内直接进行组合，构成新的对象或者数组。

#### 数组

```xml
<view wx:for="{{[zero, 1, 2, 3, 4]}}"> {{item}} </view>
```
```javascript
Page({
  data: {
    zero: 0
  }
})
```
最终组合成数组`[0, 1, 2, 3, 4]`。

### 对象
```xml
<template is="objectCombine" data="{{for: a, bar: b}}"></template>
```
```javascript
Page({
  data: {
    a: 1,
    b: 2
  }
})
```
最终组合成的对象是 `{for: 1, bar: 2}`

也可以用扩展运算符 `...` 来将一个对象展开
```xml
<template is="objectCombine" data="{{...obj1, ...obj2, e: 5}}"></template>

```
```javascript
Page({
  data: {
    obj1: {
      a: 1,
      b: 2
    },
    obj2: {
      c: 3,
      d: 4
    }
  }
})
```
最终组合成的对象是 `{a: 1, b: 2, c: 3, d: 4, e: 5}`。

如果对象的 key 和 value 相同，也可以间接地表达。

```xml
<template is="objectCombine" data="{{foo, bar}}"></template>

```
```javascript
Page({
  data: {
    foo: 'my-foo',
    bar: 'my-bar'
  }
})
```

最终组合成的对象是 `{foo: 'my-foo', bar:'my-bar'}`。

 **注意：**上述方式可以随意组合，但是如有存在变量名相同的情况，后边的会覆盖前面，如：
 
```xml
<template is="objectCombine" data="{{...obj1, ...obj2, a, c: 6}}"></template>

```
```javascript
Page({
  data: {
    obj1: {
      a: 1,
      b: 2
    },
    obj2: {
      b: 3,
      c: 4
    },
    a: 5
  }
})
```
最终组合成的对象是 `{a: 5, b: 3, c: 6}`。


##七 条件渲染

#### wx:if

在框架中，我们用 `wx:if="{{condition}}"` 来判断是否需要渲染该代码块：
```xml
<view wx:if="{{condition}}"> True </view>
```
 
也可以用 `wx:elif` 和 `wx:else` 来添加一个 else 块：
```xml
<view wx:if="{{length > 5}}"> 1 </view>
<view wx:elif="{{length > 2}}"> 2 </view>
<view wx:else> 3 </view>
```

### block wx:if

因为 `wx:if` 是一个控制属性，需要将它添加到一个标签上。但是如果我们想一次性判断多个组件标签，我们可以使用一个 `block` 标签将多个组件包装起来，并在上边使用 `wx:if` 控制属性。
  
```xml
<block wx:if="{{true}}">
  <view> view1 </view>
  <view> view2 </view>
</block>
``` 

**注意：** `block` 并不是一个组件，它仅仅是一个包装元素，不会在页面中做任何渲染，只接受控制属性。

### `wx:if` vs `hidden`

因为 `wx:if` 之中的模板也可能包含数据绑定，所有当 `wx:if` 的条件值切换时，框架有一个局部渲染的过程，因为它会确保条件块在切换时销毁或重新渲染。

同时 `wx:if` 也是**惰性的**，如果在初始渲染条件为 `false`，框架什么也不做，在条件第一次变成真的时候才开始局部渲染。

相比之下，`hidden` 就简单的多，组件始终会被渲染，只是简单的控制显示与隐藏。

一般来说，`wx:if` 有更高的切换消耗而 `hidden` 有更高的初始渲染消耗。因此，如果需要频繁切换的情景下，用 `hidden` 更好，如果在运行时条件不大可能改变则 `wx:if` 较好。


## 八 列表渲染

### wx:for

在组件上使用`wx:for`控制属性绑定一个数组，即可使用数组中各项的数据重复渲染该组件。

默认数组的当前项的下标变量名默认为`index`，数组当前项的变量名默认为`item`

```xml
<view wx:for="{{items}}">
  {{index}}: {{item.message}}
</view>

```
```javascript
Page({
  items: [{
    message: 'foo',
  },{
    message: 'bar'
  }]
})
```

使用`wx:for-item`可以指定数组当前元素的变量名

使用`wx:for-index`可以指定数组当前下标的变量名：
```xml
<view wx:for="{{array}}" wx:for-index="idx" wx:for-item="itemName">
  {{idx}}: {{itemName.message}}
</view>
```

`wx:for`也可以嵌套，下边是一个九九乘法表
```xml
<view wx:for="{{[1, 2, 3, 4, 5, 6, 7, 8, 9]}}" wx:for-item="i">
  <view wx:for="{{[1, 2, 3, 4, 5, 6, 7, 8, 9]}}" wx:for-item="j">
    <view wx:if="{{i <= j}}">
      {{i}} * {{j}} = {{i * j}}
    </view>
  </view>
</view>
```

### block wx:for

 类似`block wx:if`，也可以将`wx:for`用在`block`标签上，以渲染一个包含多节点的结构块。例如：
```xml
<block wx:for="{{[1, 2, 3]}}">
  <view> {{index}}: </view>
  <view> {{item}} </view>
</block>
```


##九 模板

WXML提供模板（template），可以在模板中定义代码片段，然后在不同的地方调用。

### 定义模板

使用name属性，作为模板的名字。然后在`template`内定义代码片段，如：

```xml
<!--
  index: int
  msg: string
  time: string
-->
<template name="msgItem">
  <view>
    <text> {{index}}: {{msg}} </text>
    <text> Time: {{time}} </text>
  </view>
</template>
```

### 使用模板

使用 is 属性，声明需要的使用的模板，然后将模板所需要的 data 传入，如：
```xml
<template is="msgItem" data="{{...item}}"/>
```
```javascript
Page({
  data: {
    item: {
      index: 0,
      msg: 'this is a template',
      time: '2016-09-15'
    }
  }
})

```

is 属性可以使用 Mustache 语法，来动态决定具体需要渲染哪个模板：

```xml
<template name="odd">
  <view> odd </view>
</template>
<template name="even">
  <view> even </view>
</template>

<block wx:for="{{[1, 2, 3, 4, 5]}}">
    <template is="{{item % 2 == 0 ? 'even' : 'odd'}}"/>
</block>
```

### 模板的作用域

模板拥有自己的作用域，只能使用data传入的数据。


 

##十. 事件

### 什么是事件

*   事件是视图层到逻辑层的通讯方式。

*   事件可以将用户的行为反馈到逻辑层进行处理。

*   事件可以绑定在组件上，当达到触发事件，就会执行逻辑层中对应的事件处理函数。

*   事件对象可以携带额外信息，如id, dataset, touches。

### 事件的使用方式

*   在组件中绑定一个事件处理函数。

如`bindtap`，当用户点击该组件的时候会在该页面对应的Page中找到相应的事件处理函数。

```xml
<view id="tapTest" data-hi="MINA" bindtap="tapName"> Click me! </view>
```

*   在相应的Page定义中写上相应的事件处理函数，参数是event。
```javascript
Page({
  tapName: function(event) {
    console.log(event)
  }
})
```
*   可以看到log出来的信息大致如下：
```json
{
"type": "tap",
"timeStamp": 1252,
"target": {
  "id": "tapTest",
  "offsetLeft": 0,
  "offsetTop": 0,
  "dataset": {
   "hi": "MINA"
  }
},
"currentTarget": {
  "id": "tapTest",
  "offsetLeft": 0,
  "offsetTop": 0,
  "dataset": {
    "hi": "MINA"
  }
},
"touches": [{
  "pageX": 30,
  "pageY": 12,
  "clientX": 30,
  "clientY": 12,
  "screenX": 112,
  "screenY": 151
}],
"detail": {
  "x": 30,
  "y": 12
}
}
```

### 事件详解

#### 事件分类

事件分为冒泡事件和非冒泡事件：

1.  冒泡事件：当一个组件上的事件被触发后，该事件会向父节点传递。
2.  非冒泡事件：当一个组件上的事件被触发后，该事件不会向父节点传递。

    WXML的冒泡事件列表：
    <table>
    <thead>
    <tr><th>类型</th>
    <th>触发条件</th>
    </tr></thead><tbody>
    <tr><td>touchstart</td>
    <td>手指触摸</td>
    </tr><tr><td>touchmove</td>
    <td>手指触摸后移动</td>
    </tr><tr><td>touchcancel</td>
    <td>手指触摸动作被打断，如来电提醒，弹窗</td>
    </tr><tr><td>touchend</td>
    <td>手指触摸动作结束</td>
    </tr><tr><td>tap</td>
    <td>手指触摸后离开</td>
    </tr><tr><td>longtap</td>
    <td>手指触摸后，超过350ms再离开</td>
    </tr></tbody></table>

    **注：除上表之外的其他组件自定义事件都是非冒泡事件，如[`form`](../../component/form.md?t=1474644091088)的`submit`事件，[`input`](../../component/input.md?t=1474644091088)的`input`事件，[`scroll-view`](../../component/scroll-view.md?t=1474644091088)的`scroll`事件，(详见各个[组件](../../component/index.md?t=1474644091088))**

    ### 事件绑定

    事件绑定的写法同组件的属性，以 key、value 的形式。

*   key 以`bind`或`catch`开头，然后跟上事件的类型，如`bindtap`, `catchtouchstart`
*   value 是一个字符串，需要在对应的 Page 中定义同名的函数。不然当触发事件的时候会报错。

    `bind`事件绑定不会阻止冒泡事件向上冒泡，`catch`事件绑定可以阻止冒泡事件向上冒泡。

    如在下边这个例子中，点击 inner view 会先后触发`handleTap1`和`handleTap2`(因为tap事件会冒泡到 middle view，而 middle view 阻止了 tap 事件冒泡，不再向父节点传递)，点击 middle view 会触发`handleTap2`，点击 outter view 会触发`handleTap1`。
 
```xml
 <view id="outter" bindtap="handleTap1">
  outer view
  <view id="middle" catchtap="handleTap2">
    middle view
    <view id="inner" bindtap="handleTap3">
      inner view
    </view>
  </view>
</view>
```

#### 事件对象


如无特殊说明，当组件触发事件时，逻辑层绑定该事件的处理函数会收到一个事件对象。
**事件对象的属性列表：**
<table>
<thead>
<tr><th>属性</th>
<th>类型</th>
<th>说明</th>
</tr></thead><tbody>
<tr><td>[type](#type)</td>
<td>String</td>
<td>事件类型</td>
</tr><tr><td>[timeStamp](#timeStamp)</td>
<td>Integer</td>
<td>事件生成时的时间戳</td>
</tr><tr><td>[target](#target)</td>
<td>Object</td>
<td>触发事件的组件的一些属性值集合</td>
</tr><tr><td>[currentTarget](#currenttarget)</td>
<td>Object</td>
<td>当前组件的一些属性值集合</td>
</tr><tr><td>[touches](#touches)</td>
<td>Array</td>
<td>触摸事件，触摸点信息的数组</td>
</tr><tr><td>[detail](#detail)</td>
<td>Object</td>
<td>额外的信息</td>
</tr></tbody></table>
### type
#### 通用事件类型
### timeStamp
该页面打开到触发事件所经过的毫秒数。
### target
触发事件的源组件。
<table>
<thead>
<tr><th>属性</th>
<th>说明</th>
</tr></thead><tbody>
<tr><td>id</td>
<td>事件源组件的id</td>
</tr><tr><td>[dataset](#dataset)</td>
<td>事件源组件上由`data-`开头的自定义属性组成的集合</td>
</tr><tr><td>offsetLeft, offsetTop</td>
<td>事件源组件的坐标系统中偏移量</td>
</tr></tbody></table>
### currentTarget
事件绑定的当前组件。
<table>
<thead>
<tr><th>属性</th>
<th>说明</th>
</tr></thead><tbody>
<tr><td>id</td>
<td>当前组件的 id</td>
</tr><tr><td>[dataset](#dataset)</td>
<td>当前组件上由`data-`开头的自定义属性组成的集合</td>
</tr><tr><td>offsetLeft, offsetTop</td>
<td>当前组件的坐标系统中偏移量</td>
</tr></tbody></table>
**说明: target 和 currentTarget 可以参考上例中，点击 inner view 时，`handleTap3`收到的事件对象 target 和 currentTarget 都是 inner，而`handleTap2`收到的事件对象 target 就是 
inner，currentTarget 就是 middle**
##### dataset
在组件中可以定义数据，这些数据将会通过事件传递给 SERVICE。
书写方式：
以`data-`开头，多个单词由连字符`-`链接，不能有大写(大写会自动转成小写)如`data-element-type`，最终在 event.target.dataset 中会将连字符转成驼峰`elementType`。
**示例：**

```xml
<view data-alpha-beta="1" data-alphaBeta="2" bindtap="bindViewTap"> DataSet Test </view>
```

```javascript
Page({
  bindViewTap:function(event){
    event.target.dataset.alphaBeta == 1 // - 会转为驼峰写法
    event.target.dataset.alphabeta == 2 // 大写会转为小写
  }
})
```

### touches

touches是一个触摸点的数组，每个触摸点包括以下属性：
<table>
<thead>
<tr><th>属性</th>
<th>说明</th>
</tr></thead><tbody>
<tr><td>pageX,pageY</td>
<td>距离文档左上角的距离，文档的左上角为原点 ，横向为X轴，纵向为Y轴</td>
</tr><tr><td>clientX,clientY</td>
<td>距离页面可显示区域（屏幕除去导航条）左上角距离，横向为X轴，纵向为Y轴</td>
</tr><tr><td>screenX,screenY</td>
<td>距离屏幕左上角的距离，屏幕左上角为原点，横向为X轴，纵向为Y轴</td>
</tr></tbody></table>

### detail

特殊事件所携带的数据，如表单组件的提交事件会携带用户的输入，媒体的错误事件会携带错误信息，详见[组件](wxml/wxml-component.md?t=1474644091088)定义中各个事件的定义。



##十一 引用

WXML 提供两种文件引用方式`import`和`include`。

### import

`import`可以在该文件中使用目标文件定义的`template`，如：

在 item.wxml 中定义了一个叫`item`的`template`：

```xml
<!-- item.wxml -->
<template name="item">
  <text>{{text}}</text>
</template>
```

在 index.wxml 中引用了 item.wxml，就可以使用`item`模板：
```xml
<import src="item.wxml"/>
<template is="item" data="{{text: 'forbar'}}"/>
```

### import 的作用域

import 有作用域的概念，即只会 import 目标文件中定义的 template，而不会 import 目标文件 import 的 template。

**如：C import B，B import A，在C中可以使用B定义的`template`，在B中可以使用A定义的`template`，但是C不能使用A定义的`template`**。

```xml
<!-- A.wxml -->
<template name="A">
  <text> A template </text>
</template>
```
```xml
<!-- B.wxml -->
<import src="a.wxml"/>
<template name="B">
  <text> B template </text>
</template>
```

```xml
<!-- C.wxml -->
<import src="b.wxml"/>
<template is="A"/>  <!-- Error! Can not use tempalte when not import A. -->
<template is="B"/>
```

### include

`include`可以将目标文件出了`&lt;template/&gt;`的整个代码引入，相当于是拷贝到`include`位置，如：
```xml
<!-- index.wxml -->
<include src="header.wxml"/>
<view> body </view>
<include src="footer.wxml"/>
```
```xml
<!-- header.wxml -->
<view> header </view>
```

```xml
<!-- footer.wxml -->
<view> footer </view>
```



## 十二 WXSS

WXSS(WeiXin Style Sheets)是一套样式语言，用于描述 WXML 的组件样式。

WXSS 用来决定 WXML 的组件应该怎么显示。

为了适应广大的前端开发者，我们的 WXSS 具有 CSS 大部分特性。
同时为了更适合开发微信小程序，我们对 CSS 进行了扩充以及修改。

与 CSS 相比我们扩展的特性有：

*   尺寸单位

*   样式导入

### 尺寸单位

*   rpx（responsive pixel）: 可以根据屏幕宽度进行自适应。规定屏幕宽为750rpx。如在 iPhone6 上，屏幕宽度为375px，共有750个物理像素，则750rpx = 375px = 750物理像素，1rpx = 0.5px = 1物理像素。
<table>
<thead>
<tr><th>设备</th>
<th>rpx换算px (屏幕宽度/750)</th>
<th>px换算rpx (750/屏幕宽度)</th>
</tr></thead><tbody>
<tr><td>iPhone5</td>
<td>1rpx = 0.42px</td>
<td>1px = 2.34px</td>
</tr><tr><td>iPhone6</td>
<td>1rpx = 0.5px</td>
<td>1px = 2rpx</td>
</tr><tr><td>iPhone6s</td>
<td>1rpx = 0.552px</td>
<td>1px = 1.81rpx</td>
</tr></tbody></table>

*   rem（root em）: 规定屏幕宽度为20rem；1rem = (750/20)rpx 。

**建议：** 开发微信小程序时设计师可以用 iPhone6 作为视觉稿的标准。
**注意：** 在较小的屏幕上不可避免的会有一些毛刺，请在开发时尽量避免这种情况。

### 样式导入

使用`@import`语句可以导入外联样式表，`@import`后跟需要导入的外联样式表的相对路径，用`;`表示语句结束。

**示例代码：**

```css
/** common.wxss **/
.small-p {
  padding:5px;
}
```

```css
/** app.wxss **/
import "common.wxss";
.middle-p {
  padding:15px;
}
```

### 内联样式

框架组件上支持使用 style、class 属性来控制组件的样式。

*   style：静态的样式统一写到 class 中。style 接收动态的样式，在运行时会进行解析，请尽量避免将静态的样式写进 style 中，以免影响渲染速度。

```xml
<view style="color:{{color}};" />
```

*   class：用于指定样式规则，其属性值是样式规则中类选择器名(样式类名)的集合，样式类名不需要带上`.`，样式类名之间用空格分隔。
```xml
<view class="normal_view" />
```

### 选择器

目前支持的选择器有：
<table>
<thead>
<tr><th>选择器</th>
<th>样例</th>
<th>样例描述</th>
</tr></thead><tbody>
<tr><td>.class</td>
<td>`.intro`</td>
<td>选择所有拥有 class="intro" 的组件</td>
</tr><tr><td>#id</td>
<td>`#firstname`</td>
<td>选择拥有 id="firstname" 的组件</td>
</tr><tr><td>element</td>
<td>`view`</td>
<td>选择所有 view 组件</td>
</tr><tr><td>element, element</td>
<td>`view` `checkbox`</td>
<td>选择所有文档的 view 组件和所有的 checkbox 组件</td>
</tr><tr><td>::after</td>
<td>`view::after`</td>
<td>在 view 组件后边插入内容</td>
</tr><tr><td>::before</td>
<td>`view::before`</td>
<td>在 view 组件前边插入内容</td>
</tr></tbody></table>

### 全局样式与局部样式

定义在 app.wxss 中的样式为全局样式，作用于每一个页面。在 page 的 wxss 文件中定义的样式为局部样式，只作用在对应的页面，并会覆盖 app.wxss 中相同的选择器。


组件参考文档 https://mp.weixin.qq.com/debug/wxadoc/dev/component/?t=1474644084689

API参考文档 https://mp.weixin.qq.com/debug/wxadoc/dev/api/?t=1474644087418