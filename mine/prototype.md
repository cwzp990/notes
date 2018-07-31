## 原型

### jQuery和zepto的原型使用

```
// html
<p>我是p标签1</p>
<p>我是p标签1</p>
<p>我是p标签1</p>

<div id="#div1">
  <p>我是div里的p标签</p>
</div>

//js
var $p = $('p');
$p.css('color', 'red');
alert($p.html())  // 只打印第一个

var $div1 = $('#div1');
$div1.css('color', 'blue');
alert($div1.html());

```

通过$传进不同的选择器，会实例出不同的对象，里面也有很多jq自定义的方法，如append等

```



```