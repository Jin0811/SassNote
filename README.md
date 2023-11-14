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

## 5 导入

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
