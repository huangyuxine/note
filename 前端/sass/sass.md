Sass（Syntactically Awesome Style Sheets）是一个强大的 CSS 预处理器，它扩展了 CSS 的功能，使得样式表的编写更具灵活性和可维护性。Sass 提供了许多高级功能，如变量、嵌套规则、混入（mixins）、继承等，这些功能使得 CSS 的编写更加高效和模块化。

## 基础知识

### 安装

```
npm install -g sass
```

### 变量

Sass 允许你使用变量来存储颜色、字体、尺寸等值，以便在整个样式表中重复使用。

**示例:**

```scss
// variables.scss
$primary-color: #3498db;
$font-stack: Helvetica, sans-serif;
$padding: 16px;

// styles.scss
@import 'variables';

body {
  font-family: $font-stack;
  padding: $padding;
  color: $primary-color;
}
```

### 嵌套规则

Sass 允许你嵌套 CSS 规则，使得样式表更具层次性和可读性。

**示例:**

```scss
nav {
  background-color: #333;
  
  ul {
    list-style: none;
    margin: 0;
    padding: 0;
    
    li {
      display: inline;
      margin-right: 10px;
      
      a {
        color: white;
        text-decoration: none;
      }
    }
  }
}
```

### 混入

允许你定义可重用的样式块，并在需要的地方调用它们。它们可以接收参数，使得样式更加灵活。

**示例:**

```scss
// mixins.scss
@mixin border-radius($radius) {
  border-radius: $radius;
}

// styles.scss
@import 'mixins';

.box {
  @include border-radius(10px);
  background: #f4f4f4;
}
```

### 继承

Sass 的继承功能允许一个选择器继承另一个选择器的样式，这样可以避免重复的代码。

**示例:**

```scss
// base.scss
%button-style {
  padding: 10px 20px;
  border: none;
  cursor: pointer;
}

// styles.scss
@import 'base';

.btn-primary {
  @extend %button-style;
  background-color: blue;
  color: white;
}

.btn-secondary {
  @extend %button-style;
  background-color: gray;
  color: black;
}
```

### 模块化

Sass 允许将样式拆分到多个文件中，并通过 `@import` 或新的 `@use` 语法将它们组合起来。推荐使用 `@use` 语法来避免全局命名空间污染。

**示例:**

```scss
// _colors.scss
$primary-color: #3498db;

// _typography.scss
$font-stack: Helvetica, sans-serif;

// main.scss
@use 'colors';
@use 'typography';

body {
  font-family: typography.$font-stack;
  color: colors.$primary-color;
}
```

### Sass 的编译

在使用 Sass 时，你通常需要将 `.scss` 文件编译成标准的 `.css` 文件。你可以使用 Sass 命令行工具、构建工具（如 Webpack、Gulp）或集成在前端框架中进行编译。

**使用命令行编译:**

```
sass src/styles.scss dist/styles.css
```