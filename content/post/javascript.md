+++
date = "2015-01-15T12:59:49+08:00"
description = "javascript notes"
title = "javascript"

+++

# 原型链

![javascript](/image/prototype.jpg)

图片来自：[kzangv](https://github.com/kzangv)


### 图片说明

------

#### 总共三类对象(蓝色大框)

1.**实例对象**（通过new XX() 所得到的实例），跟原型链相关的只有 `__proto__` 属性，指向其对应的原型对象 `*.prototype` 。

<br />

2.**构造函数对象** 分原生和自定义两类。跟原型链相关的有 `__proto__` 属性，除此之外还有 `prototype` 属性。`__proto__` 属性都是指向 `Function.prototype` 这个原型对象的。`prototype` 则是指向对应的原型对象。

<br />

3.**原型对象** 除了一样拥有 `__proto__` 外，也拥有独有的属性 `constructor` 。它的`__proto__` 指向的都是 `Object.prototype` ，除了 `Object.prototype` 本身，它自己是指向 `null` 。而 `constructor` 属性指向它们对应的构造函数对象。

<br />

# arguments 参数

它是对象，不是数组，使用 `Array.prototype.slice.call(arguments)` 转为数组
