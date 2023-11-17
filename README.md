# scss 学习笔记

## 1 scss 的输出格式

- nested 嵌套缩进的 css 代码
- expanded 展开的多行 css 代码
- compact 简洁格式的 css 代码
- compressed 压缩后的 css 代码

## 2 嵌套语法

- 选择器嵌套

  ```scss
  .container {
    width: 100vw;
    height: 100vh;
    .header {
      width: 100%;
      height: 60px;
      background-color: #1c1a1a;
      .logo {
        width: 40px;
        height: 40px;
      }
    }
  }
  ```

- 属性嵌套

  ```scss
  .content {
    font: {
      size: 20px;
      weight: bold;
      family: Arial, Helvetica, sans-serif;
    }
  }
  ```

- 父选择器

  ```scss
  .section {
    width: 200px;
    height: 150px;
    border: 1px solid #f1f1f1;
    background-color: #fff;
    &-title {
      display: flex;
      align-items: center;
      justify-content: center;
    }
    a {
      color: green;
      &:hover {
        color: red;
      }
    }
  }
  ```

## 2 占位符

scss 额外提供了一种特殊类型的选择器：占位符选择器，与常用的 id 与 class 选择器写法相似，只是 # 或 . 替换成了 %，占位符选择器必须通过 @extend 指令调用，当占位符选择器单独使用时（未通过 @extend 调用），不会编译到 css 文件中

```scss
.button%base {
  border: none;
  background: none;
  outline: none;
}

.button-primary {
  @extend %base;
  color: #fff;
  background-color: royalblue;
}

.button-danger {
  @extend %base;
  color: #fff;
  background-color: red;
}
```

## 3 scss 注释

- 非压缩模式下：
  - 单行注释不会被编译到 css 文件当中
  - 多行注释会被编译到 css 文件当中
- 压缩模式下：
  - 单行注释和多行注释都不会被编译到 css 文件当中

如果想要在压缩模式下，保留注释信息，则可以将 ! 作为多行注释的第一个字符，表示在压缩输出模式下保留这条注释并输出到 css 文件中，通常用于添加版权信息

```scss
// 这是一个单行注释
.container1 {
  width: 100%;
  height: 100%;
}

/*
  这是一个多行注释
  内容...
  内容...
*/
.container2 {
  width: 100%;
  height: 100%;
}

/*! 这是一个会在压缩模式下也保留的注释 */
.container3 {
  width: 100%;
  height: 100%;
}
```

## 4 变量

命名规则

- 必须以$开头，后面跟变量名称
- 变量名称不能以数字开头，可以包含数字、下划线、横线
- 写法同 css 的变量，变量名称和变量值使用:分隔
- 变量必须要先定义再使用
- 通过下划线和横线定义的同名变量为同一变量，下面的会覆盖上面的，建议统一使用一种命名风格

```scss
$redColor: red;
$green-color: green;
$blue_color: blue;
.test {
  color: $redColor;
}
```

<hr />

变量的作用域

- 全局变量，定义在最外层的，或者变量后面有 !global 修饰符的变量
- 定义在选择器内部的变量

```scss
$globalFontSize: 20px; // 全局变量，定义在了最外层
.container {
  $color: red !global; // 全局变量，使用了 !global
  $size: 10px; // 局部变量，定义在了选择器的内部
}
```

<hr />

变量的类型

- 数字 1/2/3/10px
- 字符串，有引号和无引号的字符串 "test1"/'test2'/test3
- 颜色 blue/#ccc/rgba(0,0,0,0.5)
- 布尔 true/false
- 空值 null
- 数组(list) 用空格或逗号作为分隔符 (1, 2, 3, "test", "test2")
- maps 相当于 js 当中的 object (key1: value1, key2: value2)

```scss
$num1: 100;
$num2: 10px;
$str1: test;
$str2: "test";
$bool: true;
$nullValue: null;
$list: (1, 2, 3, 4, 5);
$map: (
  color1: red,
  color2: green,
);

.section {
  width: #{$num1}px;
  height: #{$num1}px;
  font-size: $num2;
  content: $str2;
  border: length($list) + px solid #ccc;
  @if $bool {
    background-color: map-get($map, "color1");
  } @else {
    background-color: map-get($map, "color2");
  }
}
```

<hr />

变量的默认值
当我们接手别人的项目，不清楚某个变量是否定义时，可以使用 !default 来进行兜底操作

```scss
// 如果$test定义了，值为#333
// 如果$test未定义，值为#666
$test: #333;
$test: #666 !default;
.footer {
  color: $test;
}
```

## 5 导入 @import

在 css 当中，我们可以通过 @import 语句来导入一个 css 文件，在 scss 当中，同样存在 @import 语句。scss 拓展了 css 中@import 的功能，允许导入 scss 文件和 sass 文件，被导入的文件将合并编译到一个 css 文件中，另外被导入的文件中所包含的变量或混入指令都可以在导入的文件中使用

以下几种方式都将作为普通的 css 语句，不会导入任何的 scss 文件

- 文件拓展名为 css
- 文件名是一个网络链接
- 文件名是 url()
- @import 包含 media query

```scss
// 这里导入publi公共scss文件，会发现css目录中也编译出了一个public.css文件，并且内容是空的
// 我们只是想导入public.scss，并不想编译这个文件，可以将public.scss重命名为_public.scss
// 即以下划线开头的scss文件，将不会被监听和编译
// 注意：_public.scss和public.scss将会被认为是一个文件
@import "./public.scss";

.header {
  color: $baseColor;
}
```

## 6 混合 @mixin

混合指令提供了一种封装重复样式的方式，可以大大减少代码量。混合指令内可以编写所有的 css 代码和大部分的 scss 代码，还可以通过参数来引入变量，输出多样化的样式

混合指令的定义和使用

```scss
@mixin block {
  width: 90%;
  padding: 24px;
  margin: 0px 5% 24px;
  background-color: #fff;
  border-radius: 8px;
}
.section {
  @include block;
}
```

<hr />

混合指令-参数和默认参数

```scss
// 混合指令-带有参数
@mixin common-padding-one($top, $right, $bottom, $left) {
  padding: $top, $right, $bottom, $left;
}
.test-common-padding-one-1 {
  @include common-padding-one(0, 10px, 0, 10px); // 没有默认参数，使用时需要传递全部参数
}
.test-common-padding-one-2 {
  @include common-padding-one($top: 0, $right: 10px, $bottom: 0, $left: 10px);
}

// 混合指令-带有默认参数
@mixin common-padding-two($top: 0, $right: 0, $bottom: 0, $left: 0) {
  padding: $top, $right, $bottom, $left;
}
.test-common-padding-two-1 {
  @include common-padding-two(
    $right: 10px,
    $left: 10px
  ); // 关键词参数，可以直接指明传递给哪一个参数，其他的参数则使用默认值
}
.test-common-padding-two-2 {
  @include common-padding-two($top: 10px, $bottom: 10px);
}
```

<hr />

混合指令-剩余参数，类似于 js 当中的剩余参数，可以接收多个参数

```scss
@mixin linear-gradient($direction, $gradient...) {
  background: linear-gradient($direction, $gradient);
}
.container {
  @include linear-gradient(to right, red, green, blue, brown);
}
```

<hr />

混合指令导入内容-可以在 @include 调用混合指令时，传递代码到混合指令内，再输出

```scss
@mixin apply-to-ie6-only {
  * html {
    @content;
  }
}
@include apply-to-ie6-only {
  #logo {
    background-image: url("https://img1.baidu.com/it/u=3659556470,999557894&fm=253");
  }
}
```

## 7 继承 @extend

scss 当中复用公共样式时，可以使用继承和混合两种方式

```scss
/*
  使用@extend可以实现css样式的继承
  @extend可以继承一个css样式，也可以继承一个占位符
  下面的编写方式.alert类也会被保留，但是实际上我们并不需要.alert类
  我们可以通过继承占位符样式来去除多余的样式类
*/
.alert {
  width: 100%;
  padding: 10px;
  box-sizing: border-box;
  border: 1px solid;
  font-size: 14px;
}
.alert-success {
  @extend .alert;
  background-color: #e1f3d8;
  border-color: #67c23a;
}
.alert-danger {
  @extend .alert;
  background-color: #fde2e2;
  border-color: #f56c6c;
}

// 继承占位符样式来去除多余的样式类
%common {
  font-size: 14px;
}
.test1 {
  @extend %common;
  color: red;
}
.test2 {
  @extend %common;
  color: green;
}

// 使用混合来复用公共代码，混合比较灵活，可以传递参数，但是混合编译之后的代码体积会比继承大
@mixin flex {
  display: flex;
  align-items: center;
  justify-content: center;
}
.container1 {
  @include flex;
}
.container2 {
  @include flex;
}
```

## 8 运算符

代码示例参考 `scss/8_运算符.scss` 文件

- 等号操作符，支持所有的数据类型
  - == 等于
  - != 不等于
- 关系运行符，只能操作整数
  - < 小于
  - \> 大于
  - <= 小于等于
  - \>= 大于等于
- 逻辑运算符
  - and 与
  - or 或
  - not 非
- 数字操作符
  - \+ 加
  - \- 减
  - \* 乘
  - / 除
  - % 取模

注意，因为/是 css 当中的分隔符，所以在 scss 当中进行除法运算时，需要注意写法，以下三种情况中，/将会被视为 scss 的除法运算符号

- 如果值或者值的一部分是变量或者函数的返回值
- 如果值被圆括号包裹
- 如果值是算数表达式的一部分

```scss
$width: 100px;
$list: 100, 1000, 10000;
.test {
  font: 16px/30px Arial, Helvetica, sans-serif;
  width: ($width / 2); // 使用了变量
  height: (100px / 2); // 使用了括号
  z-index: nth($list, 2) / 10; // 使用了函数
  margin-bottom: 5px + 8px / 2px; // 使用了表达式
  background-size: #{$width} / 20px;
}
```

如果需要使用变量，又不想让 / 进行除法运算，而且是将 / 编译到 css 文件当中，只需要用#{}插值语句包裹变量即可

## 9 插值语句

插值语句，使用范围：选择器、属性名、属性值、注释等

```scss
$author: "张三";
$className: "success";
$color: green;
$attr: "background";

/* 
  @author: #{$author}
*/
button.#{$className} {
  #{$attr}-color: #{$color};
}
```

## 10 scss 常见函数

[查阅 scss 函数文档](https://sass-lang.com/documentation/modules/)

代码示例参考 `scss/10_函数.scss` 文件

color 颜色函数

- `lighten($color, $amount)` 调亮颜色
- `darken($color, $amount)` 调暗颜色
- `opacity($color)` 返回一个颜色的透明度
- `opacify($color, $amount)` 修改一个颜色的透明度

string 字符串函数

- `quote($string)` 给内容添加引号
- `unquote($string)` 去除内容的引号
- `str-length($string)` 计算内容的长度
- `insert($string, $insert, $index)` 在字符串的指定位置插入内容

math 数值函数

- `percentage($number)` 将无单位的数值转换为百分比
- `round($number)` 对数字进行四舍五入
- `floor($number)` 向下取整
- `ceil($number)` 向上取整
- `abs($number)` 绝对值
- `min()` 获取几个数字中的最小值
- `max()` 获取几个数字中的最大值
- `random($limit)` 如果不传递$limit，则随机返回一个 0-1 之间的数字，如果传递$limit，则随机返回一个 0-$limit 之间的数字

list 数组函数

- `length($list)` 返回数组的长度
- `nth($list, $n)` 返回指定的数组元素
- `index($list, $value)` 返回指定元素在列表中的位置，位置从 1 开始
- `join($list1, $list2, $separator)` 将两个列表连接到一起
- `append($list, $val, $separator)` 在列表的末尾添加一个值

map 函数

- `map-get($map, $key)` 根据键值获取 map 中对应的值
- `map-merge($map1, $map2)` 将两个 map 合并成一个新的 map
- `map-has-key($map, $key)` 判断 map 当中是否存在指定的 key
- `map-keys($map)` 映射 map 中的全部键
- `map-values($map)` 映射 map 中的全部值

selector 选择器函数

- `selector-append($selectors: ...)` 将第二个（也可以有多个）添加到第一个选择器的后面
- `selector-unify(selector1, selector2)` 将两组选择器合成一个复合选择器，如两个选择器无法合成，则返回 null 值

自检函数

- `variable-exists($name)` 判断某个变量是否存在
- `mixin-exists($name)` 判断某个混合是否存在

## 11 @if 语句

@if 语句可以用来控制代码的执行

```scss
$type: monster;
p {
  @if $type == ocean {
    color: blue;
  } @else if $type == matador {
    color: red;
  } @else if $type == monster {
    color: green;
  } @else {
    color: black;
  }
}
```

## 12 @for 语句

@for 语句类似于 js 当中的 for 循环，可以在限制的范围内进行循环，@for 语句有两种语法格式，分别为 through 和 to

- `@for $var from <start> through <end>` 包含开头和结尾
- `@for $var from <start> to <end>` 包含开头，不包含结尾

```scss
// 1 2 3 4 5
// .item-1 ~ .item-5
@for $i from 1 through 5 {
  .item-#{$i} {
    width: 10px * $i;
  }
}

// 1 2 3 4
// .section-1 ~ .section-4
@for $i from 1 to 5 {
  .section-#{$i} {
    width: 10px * $i;
  }
}
```

## 13 @each 语句

@each 语句用于遍历 list 或者 map

- 遍历单个 list
  ```scss
  @each <var> in <list> ;
  ```
- 遍历多个 list

  ```scss
  @each <var1>, <var2> in <list1>, <list2> ;
  ```

- 遍历 map
  ```scss
  @each <key>, <value> in (key1: value1, key2: value1) ;
  ```

```scss
// 遍历单个list
@each $name in (dog, pig) {
  .#{$name}-icon-1 {
    background-image: url("./icon/#{$name}.png");
  }
}

// 遍历多个list
$list1: (dog, red);
$list2: (pig, green);
@each $name, $color in $list1, $list2 {
  .#{$name}-icon-2 {
    background-image: url("./icon/#{$name}.png");
    color: $color;
  }
}

// 遍历map
@each $header, $size in (h1: 20px, h2: 18px, h3: 16px) {
  .#{$header} {
    font-size: $size;
  }
}
```
