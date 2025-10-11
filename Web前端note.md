# CSS
## 选择器语法

- `*`: 选择所有元素（通用选择器）
- 标签选择器
    - `p`: 选择所有标签为 `<p>` 的元素
- 类选择器
    - `.content-box`: 选择所有类名（class）为 `content-box` 的元素
- ID 选择器
    - `#unique-paragraph`: 选择所有 ID 为 `unique-paragraph` 的元素
- 后代选择器
    - `div p`: 选择所有 `<div>` 元素中的 `<p>` 元素
- 子选择器
    - `div > p`: 选择所有直接子元素为 `<p>` 的 `<div>` 元素
- **多个选择器可以组合使用**
    - 比如 `p.content-box` 选择所有标签为 `<p>` 且类名（class）为 `content-box` 的元素
- **选择器可以有多个，用逗号分开**
    - 比如 `p, .content-box` 选择所有标签为 `<p>` 或类名（class）为 `content-box` 的元素
- 更多选择器
    - 属性选择器
        - `[attr="value"]`: 选择所有 `attr` 属性为 `value` 的元素
    - 伪类选择器
        - `:hover`: 选择鼠标悬停的元素
        - `:active`: 选择正在被点击的元素
        - `:focus`: 选择正在被选中的元素
        - `:first-child`: 选择第一个子元素
        - `:last-child`: 选择最后一个子元素
        - `:nth-child(n)`: 选择第 n 个子元素
    - 相邻兄弟选择器
        - `p + p`: 选择所有紧接在 `<p>` 元素之后的 `<p>` 元素
    - 通用兄弟选择器
        - `p ~ p`: 选择所有在 `<p>` 元素之后的所有 `<p>` 元素


## 常用 CSS 排版属性

常用的 CSS 排版属性：

- 字体与排版
    - **`font-family`**: 设置文本的字体。可以指定多个字体，浏览器会按照顺序查找并使用第一个可用的字体 (fallback)。
        - **示例**: `font-family: Arial, sans-serif;`
        - 字体可以是系统字体，含有空格的字体名称需要用引号包裹。
        - **最佳实践**：使用 [网页安全字体](https://developer.mozilla.org/zh-CN/docs/Learn_web_development/Core/Text_styling/Fundamentals#%E7%BD%91%E9%A1%B5%E5%AE%89%E5%85%A8%E5%AD%97%E4%BD%93) 或者使用 [在线字体服务](https://developer.mozilla.org/zh-CN/docs/Learn_web_development/Core/Text_styling/Web_fonts#%E4%BD%BF%E7%94%A8%E5%9C%A8%E7%BA%BF%E5%AD%97%E4%BD%93%E6%9C%8D%E5%8A%A1)
    - **`font-style`**:设置斜体。italic
    - **`font-size`**: 设置文本的字号。可以使用像素（px）、em、rem 或百分比等单位。
        - **示例**: `font-size: 16px;` 或 `font-size: 1.2em;`
        - `em` 最初表示的是字体中大写 `M` 的宽度及所用的尺寸
        - 使用 `em` 作为单位时，文本的大小会根据父元素的字体大小而变化
        - **最佳实践**：使用 `rem` 作为单位，`rem` 表示相对根元素（`<html>`）的字体大小
    - **`font-weight`**: 设置文本的粗细。可以使用关键字（如 `normal`, `bold`）或数字（100-900）。
        - **示例**: `font-weight: bold;` 或 `font-weight: 700;`
        - **最佳实践**：使用 `bold` 等作为关键字，使用数字时，大部分字体并不支持多数区间和可变字体，常见的为 `x00`。
    - **`line-height`**: 设置文本的行高。可以是数字（相对于字号的倍数）、长度单位或百分比。
        - **示例**: `line-height: 1.5;` 或 `line-height: 24px;`
        - **最佳实践**：使用 `1.5` 作为行高，这是一个比较合理的行高，可以提高可读性。
    - **`text-align`**: 设置文本的水平对齐方式。常用值有 `left`, `right`, `center`, `justify`。
        - **示例**: `text-align: center;`
    - **`text-decoration`**: 设置文本的装饰线（如`underline`下划线、`overline`上划线、`line-through`删除线）。
        - **示例**: `text-decoration: underline;`
    - **`letter-spacing`**: 设置字符之间的间距。
        - **示例**: `letter-spacing: 0.5px;`
    - **`word-spacing`**: 设置单词之间的间距。
        - **示例**: `word-spacing: 2px;`
    - **`text-transform`**: 控制文本的大小写转换。常用值有 `uppercase`（大写）、`lowercase`（小写）、`capitalize`（首字母大写）。
        - **示例**: `text-transform: uppercase;`
    - 
- 颜色与边框
    - **`color`**: 设置文本的颜色。可以使用颜色名称、十六进制值（hex）、RGB 或 RGBA 值。
        - **示例**: `color: #333;` 或 `color: rgb(51, 51, 51);`
    - **`background-color`**: 设置元素的背景颜色。
        - **示例**: `background-color: #f0f0f0;`
    - **`border`**: 设置元素的边框。
        - **示例**: `border: 1px solid #ccc;`
        - 其实 `border` 是一个复合属性，可以设置 `border-width`, `border-style`, `border-color` 三个属性。
    - **`border-radius`**: 设置元素的圆角。
        - **示例**: `border-radius: 10px;`, `border-radius: 10px 20px 30px 40px;` （顺时针）
        - 可以使用 `100%` 变成圆形/椭圆。
- button
	- **`transition`**：颜色过渡动画
		- **示例**：transition: color 0.3s ease; /* 颜色过渡动画 */
```
		a {

            color: #3498db; /* 蓝色链接 */

            text-decoration: none; /* 移除下划线 */

            transition: color 0.3s ease; /* 颜色过渡动画 */

        }

        a:hover {

            color: #2980b9; /* 悬停时变深 */

            text-decoration: underline; /* 悬停时出现下划线 */

        }
        
        
        
        
        
```

				transition: background-color 0.3s ease, box-shadow 0.3s ease;

## 显示属性 (Display Property)

`display` 属性是 CSS 布局最基本的属性之一，它定义了元素如何被显示。

- **`block`（块级元素）**: 独占一行，宽度默认填充父容器，可以设置宽度、高度、内边距和外边距。常见的块级元素有 `<div>`, `<p>`, `<h1>`。
- **`inline`（行内元素）**: 不独占一行，宽度由内容决定，不能设置宽度和高度，垂直方向的内边距和外边距不影响布局。常见的行内元素有 `<span>`, `<a>`, `<strong>`。
- **`inline-block`（行内块级元素）**: 结合了行内元素和块级元素的特性。它像行内元素一样排列，但可以设置宽度、高度、内边距和外边距。
- **`none`**: 元素将完全从文档流中移除，不占据任何空间。

## 定位 (Positioning)

`position` 属性允许您对元素进行精确控制，使其脱离正常的文档流，或相对于其他元素进行定位。

- **`static`（静态定位）**: 默认值。元素按照正常的文档流进行布局。`top`, `right`, `bottom`, `left` 属性无效。
- **`relative`（相对定位）**: 元素相对于其正常位置进行定位，但仍占据其在文档流中的原始空间。`top`, `right`, `bottom`, `left` 属性会使元素相对于自身位置移动。
- **`absolute`（绝对定位）**: 元素脱离正常文档流，不占据任何空间。它相对于 _最近的已定位父元素_ （非 `static`）进行定位。如果没有已定位的父元素，则相对于 `<body>` 进行定位。
- **`fixed`（固定定位）**: 元素脱离正常文档流，不占据任何空间。它相对于 _浏览器视口/ViewPort_ 进行定位，并且在滚动时保持固定位置。
- **`sticky`（粘性定位）**: 元素根据用户的滚动位置在 `relative` 和 `fixed` 之间切换。当它在视口中时，行为类似于 `relative`；当它滚动到特定阈值时，则变为 `fixed`。


# Js
## null vs undefined

- `undefined` 表示“声明但未分配值”
- `null` 表示“没有值”
    - **最佳实践：** 使用 `null` 来清空一个变量。明确地说变量现在是空的。

## 输出

- 我们通常输出到控制台，便于快速调试！
    - `console.log()`
    - `console.error()`
    - `console.warn()`
    - `console.info()`
    - `console.debug()`
- `console.log()` 是最常用的，而且可以接受多个参数，几乎什么都能输出
    - `console.log("Hello", "world!");`
    - `console.log("The answer is", 42);`
    - `console.log("The answer is %d", 42);`
    - ``console.log(`The answer is ${41 + 1}`);`` (template literals / 格式化字符串)
- 使用 `alert` 弹窗
    - `alert("Hello, world!");`



## 对象

- 对象 / Object 是属性的集合
- 每个属性都有一个名称，描述其含义；以及一个值，描述该对象属性的值。
- 语法：用大括号括起来，冒号分隔名称和值，逗号分隔值对。允许你将相关数据存储在一个变量中。
- 可以将它们视为 JavaScript 中相当于 Python 字典的部分。
- 那么，我们可以用它做什么呢？

```
const person = {  name: "John",  age: 30,};
```


### 访问属性、解构

- 如果你知道属性名称，有两种方法可以访问对象属性：

```
const myCar = {  make: "Ford",  model: "Mustang",  year: 2005,  color: "red"}; console.log(myCar.model); // "Mustang"console.log(myCar["color"]); // "red"
```

- 或者你可以通过解构同时获取多个

```
const { model, color } = myCar;console.log(model, color); // "Mustang" "red"
```


### 对象引用和拷贝

- 对象变量是**引用**
    - 它们指向数据实际存储的位置
- Javascript 中，`===` 比较的是**引用**，而不是值，即检查两个数据是否存储在同一位置
    - Python 仍然会比较值

### 浅拷贝和深拷贝

- 浅拷贝：只拷贝对象的第一层
- 深拷贝：拷贝对象的所有层

```
// Shallow copylet arr = [1, 2, 3];let copyArr = arr;// 修改 copyArr 也会修改 arr
```

```
// Deep copylet arr = [1, 2, 3];let copyArr = [...arr];// 修改 copyArr 不会修改 arr
```


> 补充python函数小知识：
> 1.add= lambda x, y: x + y
> result=add(5,3)
> 当你需要一个简单的函数，但只在一个地方使用一次，不值得用 `def` 正式定义时。

## 函数
### 定义函数

```
// Javascriptconst 
celsiusToFahrenheit = (tempCelsius) => { const tempFahrenheit = tempCelsius * 9/5 + 32; return tempFahrenheit;};
```


### `map`

```
const arr = [1, 2, 3, 4, 5];const arr2 = arr.map((x) => x * 2); // [2, 4, 6, 8, 10]
```

### 函数链

- Javascript 中有许多高阶函数，它们接受函数作为参数，并返回一个新函数。
    - Eg. `map`, `filter`, `reduce`
    - 甚至在网络请求中，我们也可以用 `fetch(...).then(...).catch(...).finally(...)` 来链式调用。
- 接受一个函数作为参数，对数组中的每个元素应用该函数，并返回一个新数组。
- 不会改变原数组，而是返回一个新数组。
- 可以链式调用。

```
const arr = [1, 2, 3, 4, 5];const arr2 = arr.map((x) => x * 2).filter((x) => x > 5); // [6, 8, 10]
```

```
// reduceconst sum = arr.reduce((acc, x) => acc + x, 0); // 15
```
### 回调函数的实际运用

#### `addEventListener`

- 接受两个参数：事件类型和回调函数。
- 回调函数会在事件发生时被调用。

```
// 按钮在点击时会调用回调函数const button = document.querySelector("button");button.addEventListener("click", () => {  console.log("clicked");});
```

```
// 窗口在按下按键时会调用回调函数window.addEventListener("keydown", (event) => {  console.log(event.key);});
```

More at [MDN - `addEventListener`](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener)




# 一些补充：
**`<textarea>` 是一个允许用户输入多行文本的输入框**，类似于记事本的功能。

与 `<input type="text">`（单行文本框）的区别：

- `<input type="text">`：只能输入单行文本
    
- `<textarea>`：可以输入多行文本，会自动换行

`box-shadow: <水平偏移> <垂直偏移> <模糊半径> <颜色>;`

- **水平偏移** `0` → 阴影在左右方向不偏移，正数向右，负数向左
    
- **垂直偏移** `1px` → 阴影在上下方向偏移 1px，正数向下，负数向上
    
- **模糊半径** `2px` → 阴影模糊的程度，数值越大越柔和
    
- **颜色** `rgba(0,0,0,0.1)` → 半透明黑色（透明度 0.1，非常浅）



# d3
## assignment1
1. csv文件导入：`d3.csv('pgy2022.csv').then(data => {}`
2. 数据处理：将字符串转换为数字：
```
data.forEach(d => {
d.globalsale =+d.globalsale
d.year = +d.year;
 });
```

3. 提取单个名称下的所有数据成为一个数组：`const years = Array.from(new Set(data.map(d => d.year))).sort();`
4. 定义坐标轴和比例尺：
```
const xScale = d3.scaleLinear();
const yScale = d3.scaleBand();
yScale.domain(data.map(yValue)).range([0, innerHeight]).padding(0.1);\\离散到连续一定要padding
xScale.domain([0, d3.max(data, xValue)]).range([0, innerWidth]);
```
> 这里要注意的是坐标轴不要在render函数里面定义

```
xAxis = d3.axisBottom(xScale);

            yAxis = d3.axisLeft(yScale);

            xAxisGroup = mainGroup.append('g').call(xAxis);

            yAxisGroup = mainGroup.append('g').call(yAxis);

            xAxisGroup.attr('transform', `translate(${0}, ${innerHeight})`);
```
maingroup.append('g')就是把xy坐标轴看成一个组。、
加入坐标轴标题
```
xAxisGroup.append('text').attr('class', 'axisTitle').text('Global Sale')

                .attr('x', innerWidth / 2).attr('y', 60);
yAxisGroup.append('text').attr('class', 'axisTitle').text('Platform')

                .attr('transform', 'rotate(-90)').attr('x', -innerHeight / 2).attr('y', -60);

d3.selectAll('.axisTitle').attr('text-anchor', "middle").attr('fill', 'black').attr('font-size', '2em');

```
xy定义成一个叫做axistitle的类方便select之后统一操作。
5. 根据年份遍历每个类别的数据：
6. 动画更新：
- 先定义一个transition：
```
 const transition = d3.transition()

                .duration(1000)

                .ease(d3.easeLinear);
                
               
```

- 使用enter(),enter代表还未显示到图上，但是存在于数据里的，这时候，要到这些状态就要使用transition
```
let enter =

                maingroup.selectAll('rect')

                    .data(data, d => d.platform)

                    .enter().append('rect')

                    .attr('height', yScale.bandwidth())

                    .attr('x', 0).attr('y', d => yScale(yValue(d)))

                    .attr('fill', d => colorscale(yValue(d)))
```

```
 maingroup.selectAll('rect').merge(enter).transition(transition)

                .attr('y', d => yScale(yValue(d)))

                .attr('width', d => xScale(xValue(d)))

                .attr('height', yScale.bandwidth());
```

- 更新完柱子、标注数据之后还要根据比例尺变化更新坐标轴：
```
xAxis = d3.axisBottom(xScale);

            yAxis = d3.axisLeft(yScale);

            xAxisGroup.transition(transition).call(xAxis);

            yAxisGroup.transition(transition).call(yAxis);

            
```



