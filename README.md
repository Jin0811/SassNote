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
