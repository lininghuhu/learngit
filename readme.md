# 2017IFE任务汇总:
[汇总](https://psilocine.github.io/learngit/)
(全做完了哦~ FAUX!

## 下面是自己的一些坑和笔记
> 走过的一些坑,作此文档用来激励自己,也希望读者(你)能与我共勉.&nbsp;&nbsp;&nbsp; -PsiloLau

### 2017年11月17日
插入数组的不同方法性能对比
1. 向数组中插入元素, 能有几种方法?
```JavaScript
var arr = [1,2,3,4];
// 数组结尾添加元素
1. arr.push(n)最简单;     // 返回arr长度, 并且arr改变
2. arr[arr.length] = n;  // 返回n, arr改变
3. arr2 = arr.concat(n); // arr不变
从效率来说, 第3种是最吃性能的, 也就是最慢的; 而通常最先想到的push方法, 也不是最优的解决方法, 
综上考虑采取第2种方法效率性能能快上不少, 在不同浏览器上都能快50%以上(safari浏览器除外)

// 数组头部添加元素
1. arr.unshift(n); // 原数组改变
2. [n].concat(arr); // 返回新数组, arr不变
令人意外的是unshift()方法比concat平均慢了一倍不止(safari浏览器除外)

// 数组中间添加元素
1. arr.splice(arr.length/2, 0, n); // arr改变, 返回空数组 arr = [1,2,n,3,4];
当元素是奇数时, arr = [1,2,3]; 上述语句执行后, arr => [1,n,2,3];
用splice是最优方案, 无脑用即可
```

### 2017年11月15日
1. 打乱数组排序的技巧.
```JavaScript
var arr = [5, 345, -127, 1222, 866, -10, 400];
arr = arr.sort(function() {
  return Math.random() -  0.5
});
// 顺序打乱
```

### 2017年11月13日
今天讨论js单线程的机制
1. 任务队列task queue. js是单线程的, 先执行一行一行的代码, 这属于同步任务, 后去任务队列运行里面的任务, 属异步操作, 只有主线程即同步任务空了, 才会去执行任务队列. 
2. macrotask microtask. microtask: promise, Object.observe等; macrotask: setTimeout setInterval等. 值得注意的是整个script代码也是macrotask
3. 具体的流程是, 先执行macrotask(整个script代码), 同步代码运行完后有microtask就先执行, 没有microtask执行下一个macrotask, 依次列推.

### 2017年11月2日
1. arguments并不是真正的数组, 它是一个实参对象. 每个实参对象都包含以数字为索引的一组元素以及length属性, 但它毕竟不是真正的数组, 可以理解成它是一个对象, 只是碰巧具有以数字为索引的属性. 在严格模式下不能给arguments赋值, 和使用arguments作为变量名.
2. caller, callee属性. ECMAScript标准规范规定callee属性指代当前正在执行的函数, caller是非标准的, 指代调用当前正在执行的函数的函数. 通过caller可以访问调用栈. callee在递归调用非常有用.
3. 宁愿程序在传入非法值时报错, 也不愿非法值导致程序在执行时报错.
4. 闭包. 作用域在函数定义时决定的.
```JavaScript
var scope = 'global scope';
function checkscope() {
  var scope = 'local scope';
  function f() { return scope; }
  return f;
}
checkscope()();   // 'local scope'
```

### 2017年10月31日
1. toString()问题. 让我们来探索下3.toString(), 3..toString(), 3...toString()的结果.
```javascript
3.toString();   // error
3..toString();  // '3'
3...toString(); // error

var a = 3;
a.toString();   // '3'
a..toString();  // error

// 上面看完是不是晕了, 其实在js中, (3.3), (3.), (.3)都是合法的数字, 3.toString()里的 . 到底是 (3.)还是方法调用? 结果是(3.),
// 因为编译从左到右, 所以 3. .toString()才是唯一不报错的那个. 当然我们可以加括号
(3).toString(); // '3'
```

### 2017年10月30日
1. string中slice, substr, substring方法区别. slice两个参数(start, end), 返回开始和结束区域(不包含结束), 负数加上字符串总长度; substr两个参数(start, length), 返回开始index到长度长度, 第二个参数(length)不支持负数; subtring两个参数(start, end), 返回开始和结束区域(不包括结束index, 如果第二个参数比第一个参数小, 会调转位置), 负数直接置零.
```JavaScript
var str = 'hello world';
str.slice(-3);     // rld
str.substr(-3);    // rld
str.substring(-3); // hello world

str.slice(4, 7);     // 'o w'
str.substr(4, 7);    // 'o world' 第二个参数7, 从索引4开始, 返回7个长度
str.substring(4, 7); // 'o w'

str.slice(3, -4);     // 'lo w'
str.substr(3, -4);    // '', 长度-4返回空字符串
str.substring(3, -4); // 'hel' -4置零, 变成str.substring(0, 3);
```
 
### 2017年10月28日
1. 数组方法 reduce(), reduceRight(). 两个参数, 第一个是执行化简操作的函数, 第二个(可选)的参数是一个传递给函数的初始值, 当没有指定初始值时将使用数组的第一个元素作为其初始值. reduceRight()工作原理和reduce()一致, 只是顺序是从右到左.
```JavaScript
var a = [1,2,3,4,5]
var sum = a.reduce(function(x,y){ return x+y;}, 0);      // 数组求和 
var product = a.reduce(function(x,y){ return x*y; }, 1); // 数组求积
var max = a.reduce(function(x,y){ return (x>y)?x:y; });  // 求最大值, 初始值是1
```
2. 函数表达式也可以包含名称, 而且函数表达式适合用来定义只会用一次的函数.
```JavaScript
// 包含名称在递归的时候很有用
var f = function factorial(x) { if(x<=1) return 1; return factorial(x-1); }

// 函数表达式有时定义后立即调用
var tensquared = (function(x){ return x*x;}(10));
```
3. 函数调用4种, 作为函数, 作为方法, 作为构造函数, 通过call()apply().


### 2017年10月22日
1. es6: 用let命令声明，不会发生变量提升。如果区块中存在let和const命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。
2. 字符串的扩展. 可以用反引号`标识, 
```javascript
`There are <b>${basket.count}</b> items`
```
省去+连接符的繁琐, 变量直接在${}里填写即可.
3. 数值的扩展. 新增二进制和八进制的写法. 0b11 == 3, 0o11 == 9; 提供Number.isFinite(), Number.isNaN().
4. 函数的扩展. 箭头函数有几个使用注意点
```javascript
（1）函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。其实箭头函数内部没有自己的this，导致内部的this就是外层代码块的this。

（2）不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。

（3）不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。

// ES6
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}

// ES5
function foo() {
  var _this = this;

  setTimeout(function () {
    console.log('id:', _this.id);
  }, 100);
}
```
5. 数组的扩展. Array.from方法用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象; Array.of方法用于将一组值，转换为数组。
```javascript
let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
};
let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']

Array.from('hello') // ['h', 'e', 'l', 'l', 'o']

let namesSet = new Set(['a', 'b']) // Set { "a", "b" }
Array.from(namesSet) // ['a', 'b']

Array.of(3, 11, 8) // [3,11,8]
Array.of(3) // [3]
Array.of(3).length // 1
```
6. 对象的扩展. 属性的简洁表示法
```JavaScript
// 变量缩写
function f(x, y) {
  return {x, y};
}
// 等同于
function f(x, y) {
  return {x: x, y: y};
}

// 方法缩写
const o = {
  method() {
    return "Hello!";
  }
};
// 等同于
const o = {
  method: function() {
    return "Hello!";
  }
};
```

### 2017年10月18日
1. parentNode, parentElement区别. parentNode是W3C标准, parentElement只在IE中可用. 经测试后者也能在高级浏览器使用. parentNode返回元素的DOM树中的父元素. nodeType可以是多样的. 后者返回必须是元素节点, 否则返回null. 
```javascript
eg: document.body.parentNode.parentNode; // => #Document nodeType为9(Document); 
document.body.parentElement.parentElement; // => null 因为nodeType !== 1(Element) 所以返回null;
```
### 2017年10月16日
1. Array.join(), Array.concat(), Array.slice(), toString(), toLocaleString(), 都不会修改原数组.
2. Array.reverse(), Array.sort(), Array.splice(), push()pop() shift()unshift()会修改原数组.
3. es5数组方法. forEach()修改原数组; map(), filter()不会修改原数组.
4. 在空数组上调用时, every()返回true, some()返回false.

### 2017年10月15日
1. 使用负数或非整数来索引数组, 数值转换为字符串, 字符串作为属性名来用; 使用非负整数的字符串, 它会被当做数组索引; 使用浮点数和一个整数箱等时的情况下, 也会被当成数组索引.
```JavaScript
var arr = [];
// 负数非整数
arr[-1.23] = 1; // 创建"-1.23"的属性

// 非负整数字符串
arr["5"] = 5; // arr[5] => 5

// 浮点数
arr[1.000] // 和a[1]等价
```
2. 稀疏数组, 就是length大于元素实际的个数. 当省略数组直接量中的值时, 所得的数组是稀疏数组.
3. 设置length属性为一个小于当前长度的非负整数n时, 数组中那些索引值大于n的元素都将被删除.
```JavaScript
a = [1, 2, 3];
a.length = 0;
a // []
a.length = 5;
a // [undefined x 5]
```
4. 数组元素添加删除. delete并不影响数组长度, 可是会致使数组变为稀疏数组.
```javascript
a = [1, 2, 3];
delete a[1];
1 in a; // false: 数组索引1并未在数组中定义
a.length; // => 3
```
5. pop(), shift()方法会删除数组元素并返回被删的值. 注意的是pop()方法会使length-1, shift()方法将所有元素的索引降1, 这是和delete不同的地方.


### 2017年10月13日
1. 属性特性. 数据属性的4个特性: value, writable, enumerable, configurable. 存储器4个特性: get, set, enumearble, configurable. 可以用Object.defineProperty(对象, "属性", {4个特性})来定义一个对象的属性. 不写的话默认false或undefined
```javascript
var o = {};
// 可配置性configurable是true就可以修改4个特性, 但是要注意的是, 修改configurable为false后再也不能修改了
Object.defindProperty(o, 'x', {value:1, writable:true, configurable:true}); // enumerable没写默认false.

// x属性存在但不可枚举
o.x; // => 1
Object.keys(o) // => []

// 对属性x做修改, 让它只读
Object.defindProperty(o, 'x', {writable: false});
o.x = 2; // 操作失败但不报错, 严格模式下抛出TypeError.
o.x; // => 1

// 修改可配置性
Object.defindProperty(o, 'x', {configurable: false});
Object.defindProperty(o, 'x', {configurable: true}); // error: Uncaught TypeError: Cannot redefine property: x

// 存储器
Object.defineProperty(o,"b",{
  set:function(newValue){
    console.log("你要赋值给我,我的新值是"+newValue)
  },
  get:function(){
    console.log("你取我的值")
      return 2;
  }
})
o.b = 1; // 控制台输出"你要赋值给我,我的新值是1"
o.b; // 控制台输出'你取我的值', 返回2

// 对多个属性同时定义可用Object.defindProperties(o, {
  x: {value:1},
  b: {set: function()...}
})
```
2. [,,]是两个元素而不是三个, 原因是数组直接量的语法允许有可选的结尾的逗号.


### 2017年10月12日
1. continue在while语句和for语句里作用不一样. while语句的increment自增自减语句和执行语句在一起, continue跳过也会跳过而陷入死循环. for语句的continue跳出当前执行语句, 然后再进行increment自增自减.
2. 严格模式下, 禁止使用with语句. 调用的函数(不是方法)中的this不是全局对象而是undefined. 
3. delete运算符是断开属性和宿主对象的联系, 而不会去操作属性中的属性. a={p:{x:1}}; b=a.p; delete a.p; b.x的值依然为1; 其二delete只能删除自有属性, 不能删除继承属性. 

### 2017年10月11日
1. 不考虑兼容性的情况下, 觉得滚动条太丑, 可以用css3自定义滚动条样式. -webkit-scrollbar, 纯css实现滚动条, 并且支持鼠标滑轮, 鼠标点击等操作.

### 2017年10月10日
1. switch语句里的case和表达式匹配是用全等'==='来衡量, 换句话说, 表达式和acse的匹配并不会做任何类型转换.
2. for语句. for(initialize; test; increment) statement; 循环中三个表达式中的任何一个都可以忽略, 但是两个分号必不可少. for(;;).
3. break语句带不带标签, 它的控制权都无法越过函数的边界. 对于一条带标签的函数定义语句来说, 不能从函数内部通过这个标签来跳转到函数外部.


### 2017年10月7日
1. 当有运算符副作用时, i=i+1和++i就不等价了. eg: data[i++] += 2; data[i++] = data[i++] + 2;
2. eval()直接调用时, 会在调用它的上下文作用域内执行; 其他的间接调用则使用全局对象作为其上下文作用域, 并且无法读写局部变量和函数.
```JavaScript
var geval = eval;
var x = 'global',
    y = 'global';
function f() {
  var x = 'local';
  eval('x += "changed";');
  return x;
}
function g() {
  var y = 'local';
  geval('y += "changed";');
  return y;
}

console.log(f(), x); // 直接调用, 改变局部变量. 输出'localchanged global'
console.log(g(), y); // 间接调用, 改变全局变量. 输出'local globalchanged'
```
3. typeof(i) 等价 typeof i;
4. delete运算符不能删除内置核心和客户端属性, 不能删除用户var出来的变量和用户通过function定义的函数;
```javascript
var a = 1;
b = 1;
delete a; // false 删除失败返回false, 严格模式下还会返回类型错误.
delete b; // true
a; // 1
window.a; // 1
b; // undefined
window.b; // undefined
```
5. 逗号运算符返回右操作数. eg: i=0,j=1,k=2; 返回2;

### 2017年10月6日
1. lval. left-value缩写, 左值指表达式只能出现在赋值运算符的左侧.
2. 三元运算符顺序, 从右至左. eg: q = a ? b : c ? d : e ? f : g; // => q = a ? b : (c ? d : (e ? f : g))
3. y = x + z. 如果表达式x中的一个变量自增1, 这个变量在表达式z中使用, 那么实际上是先计算出了x的值在计算z的值. eg: b = (a++) + a. 假设a的值是1, b = 3; 
4. 字符串比较是区分大小写的. 所有大写的ASCII字母都小于小写的ASCII字母 'Zoo' < 'asd' // => true
5. 加号运算发更偏爱字符串, 有一个是字符串的话, 则进行字符串比较; 比较运算符更偏爱数字, 只有当两个操作数是字符串时, 才会进行字符串比较.
```javascript
var date = new Date(),
    fun  = function(){return 1;},
    arr  = [1, 2, 3],
    reg  = /\d+/g,
    date2,
    fun2,
    arr2,
    reg2;
    
date2 = date + 1; // typeof date2 => String 下面同
fun2 = fun + 1; // String
arr2 = arr + 1; // String
reg2 = reg + 1; // String

date2 = date - 1; // Number
fun2 = fun - 1; // Number
arr2 = arr - 1; // Number
reg2 = reg - 1; // Number
----------------------
'11' < 2 // 11 < 2 false
'one' > 3 // NaN > 3 false 只要有Nan就是false
```

### 2017年10月3日
1. 包装对象. 
```JavaScript
var s = 'test';
s.len = 4;
var t = s.len // => undefined
// 第二行代码创建一个临时字符串对象, 并给其len属性赋值为4, 随即销毁这个对象. 
// 这种存取字符串, 数字, 布尔值的属性时创建的临时对象称作包装对象.
// 用来区分字符串值字符串对象, 数字数值对象, 布尔值布尔对象.
// 可以通过String() Number() Boolean()构造函数来显式创建包装对象.
```
2. 原始值: 任何方法都无法更改. 原始值的比较是值的比较. 对象的比较并非值的比较, 即使两个对象包含同样的属性相同的值也不会相等.
3. 非空非数字的字符串, 类型转换成Number类型都是NaN. eg: 'n' 'one'
4. 一些隐式类型转换. 
```JavaScript
x + '' // 等价于String(x)
+x // 等价于Number(x) 之前提到的一元运算符
!!x // 等价于Boolean(x)
```
5. 除了常见的toFixed(), 还有toExponential()使用指数计数法将数字转换为指数形式的字符串, 小数点后的位数由参数指定, 即总的有效数字 = 参数 + 1; toPrecision()根据指定的有效数字位数将数字转换成字符串, 如果有效数字的位数少于数字整数部分的位数, 则转换成指数形式. 三种方法都会适当地进行四舍五入或填充0.
```JavaScript
var n = 123456.789
n.toFixed(5) // "123456.78900"
n.toExponential(1) // "1.2e+5"
n.toExponential(3) // "1.235e+5"
n.toPrecision(4) // "1.235e+5"
n.toPrecision(7) // "123456.8"
n.toPrecision(10) // "123456.7890"
```
6. 对象转换为原始值. 第一个是toString(), 第二个是valueOf(). 
```JavaScript
对象到字符串的转换:
1. 如果对象具有toString(), 则调用这个方法, 如果它返回一个原始值, JavaScript将对这个值转换为字符串, 
   并返回这个字符串结果.
2. 如果对象没有toString(), 或者这个方法并不会烦一个原始值, 那么JavaScript会调用valueOf()方法. 如
   果存在这个方法, 则调用它, 如果返回值是原始值, JavaScript将这个值转换为字符串, 并返回这个字符串结果.
3. 否则, JavaScript会抛出一个类型错误异常
对象到数字的转换: 将上述1, 2点反转一下.
```

### 2017年10月2日
1. 出于可移植性和易于书写的考虑, 一般只是用字母和数字来定义标识符(变量名), 但是js是允许标识符出现Unicode字符的, 意味着可以使用非英语语言或数字符号来书写标识符. eg: var π = 3.14;
2. js字符串都是固定不变的, replace() toUpperCase()等方法都是返回新数组.
3. 涉及++和--运算符时, 如果想将其作为后缀表达式, 必须和变量在同一行.
```JavaScript
x
++
y
// js解释器将解析为x; ++y;
x
++;
y
// 报错 SyntaxError

```

### 2017年9月29日
1. vscode插件修改个人配置, 到C盘下所在用户文件夹下找到.vscode/extensions/插件所在文件夹, 找到相关文件修改, 重启vscode即可. 比如fileheader, 只需要修改package.json里的Author.
2. 犀牛书的直接量和高程里的字面量是一个东西.

### 2017年9月26日
1. visibility属性, 默认visible, 设为hidden元素不可见. 与opacity的区别: 都占据原本的空间, 区别是opacity会响应用户交互, visibility不会. 如绑定事件click, 点击opacity的元素还是会起作用.

### 2017年9月25日
1. Math.min()比Math.max()大. Math.min() > Math.max() // true
2. 正则表达式字面一致也不相等
3. 函数名称不可改变
4. 写出最简单的去重方式
```JavaScript
// es6的new Set()方法, 不过一般面试官肯定不会只满意这个
let arr = [0, 1, 2, 1, 0];
console.log(new Set(arr)); // => Set {0, 1, 2}

// es5 filter过滤器
arr.filter(function(elem, index, Array) {
  return index === Array.indexOf(elem)
})
```

### 2017年9月16日
1. 父元素透明不影响子元素. 高级浏览器下用opacity后, 子元素会一起变透明, 显然是不行的, 这时候可以用rbga来实现; ie6, 7 ,8, 9浏览器下用专属的filter:Alpha(opacity=x), 再将子元素设为相对定位, 可以让子元素不透明. 除此之外还可以用两个div来重叠, 设置z-index即可.
```html
<div>
  <p>text</p>
</div>
```
2. 父元素模糊不影响子元素. 通过伪类实现, ie下也可以通过方法支持伪类从而实现.
```css
.test {  
  width:420px;
  height:420px;
}
/* 亲测after也可用 */
.test::before{ 
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0px;
  left: 0px;
  content: "";
  
  -webkit-filter: blur(10px);
  -moz-filter: blur(10px);
   -ms-filter: blur(10px);    
       filter: blur(10px);
  z-index: -1; /* z-index负值使子元素可见 */
  background:url("") no-repeat;
  background-size: cover;
  overflow:hidden;  /* 边缘是否模糊, hidden为不模糊 */
}
```

### 2017年9月15日
1. 前序遍历: 先遍历根结点, 然后遍历左子树, 最后遍历右子树. 中序遍历: 先遍历左子树, 然后遍历根结点, 最后遍历右子树. 后序遍历: 先遍历左子树, 然后遍历右子树,最后遍历根节点.

### 2017年9月14日
1. 解耦HTML/JavaScript. HTML和JavaScript过于紧密耦合, 出现错误的时候就要判断错误是在HTML还是在JavaScript部分, 第一种情况是HTML中用script元素包含内联代码; 第二种情况就是JavaScript中用innerHTML包含html代码. 行为和数据需要保持分离. HTML和JavaScript解耦可以在调试过程中节省时间, 更加容易确定错误的来源和减轻维护的额难度.
2. 解耦CSS/JavaScript. 在JavaScript中常常会用到element.style.color = 'red';这样来更改某些样式. 实际上让CSS和JavaScript完全解耦是不可能的, 不过我们可以将大部分样式信息留在CSS中, 通过动态定义类来最小程度上减轻耦合紧密度, elment.className = 'text-color';
3. 解耦应用逻辑/事件处理程序. 将应用逻辑validateValue从事件处理程序汇总分离出来有几个好处, 第一不依赖事件处理, 只接受一个值, 后续如果有事件引发同样的逻辑, 可以调用它; validateValue更容易被触发, 在事件处理程序中如果发生错误, 你需要判断两边, 但分离后你手动传值就能判断出应用逻辑是否有错. 几条原则: 勿将event对象传给其他方法, 只传来自event对象中所需的数据; 任何可以在应用层面的动作都应该可以在不执行任何事件处理程序的情况下运行; 任何事件处理程序都应该处理事件, 然后将处理转交给应用逻辑.
```JavaScript
    function handleKeyPress (event) {
      event = EventUtil.getEvent(event);
      if (event.keyCode == 13) {
        var target = EventUtil.getTarget(event);
        var value = 5 * parseInt(target.value);
        if (value > 10) {
          document.getElementById('error-msg').style.display = 'block';
        }
      }
    }
    ----------------------------------
    function validateValue (value) {
        value = 5 * parseInt(value);
        if (value > 10) {
          document.getElementById('error-msg').style.display = 'block';
        }
    }
    function handleKeyPress (event) {
      event = EventUtil.getEvent(event);
      if (event.keyCode == 13) {
        var targe = EventUtil.getTarget(event);
        validateValue(target.value);
      }
    }
```

### 2017年9月13日
1. 离线检测 navigator.onLine 值为true表示设备能上网, 正常工作; false表示设备离线, 执行离线状态时的任务. 实际上HTML5还定义了online offline两个事件, 对象都是window. 在加载页面后用navigator.onLine获得初始状态, 然后靠这两个事件来检测应用是否变化.
2. 数据存储 HTTP Cookie, 也叫cookie. 一些限制: cookie绑定在特定的域名下, 无法被其他域访问; cookie总个数有限制, 如ie6最多20个; cookie容量不能超4KB. cookie由名称,值,域,路径,失效时间,安全标志构成. 服务器将上述发给浏览器, 而浏览器只能发键值给服务器,且都需要URL编码.
3. Web storage存储机制 sessionStorage和globalStorage(被localStorage取代). 出现的原因是克服cookie的一些限制. 二者区别: session针对回话的小段数据存储, 即数据值保持到浏览器关闭. local除非用户通过js清除或清除浏览器缓存, 会一直存在. Web storage容量能达到5MB.

### 2017年9月12日
1. 函数返回值return和值之间不能换行, 不然返回undefined. 2017年10月2日补充, break, continue也同理.

### 2017年9月11日
1. with try-catch eval可以改变作用域链.
2. Boolean([]); // => true,  Number([]); // => 0,  Number({}); // => NaN,  Number(false); // => 0  
   [] == false // => true,  {} == false // NaN == 0 => false.
3. canvas绘制矢量图, svg绘制位图.
4. 1. A:IE6.0的div的内嵌div可以把父级的高度撑大, 而FireFox不可以, 要自己设置高度;
   2. 当设置为三列布局时, IE6.0的float宽度不能达到100％, 而FireFox可以. 当设置为两列布局时, 两种浏览器都可以;
   3. FF非float的div前面有同一父级的float的div, 此div若有背景图, 要使用clear: both, 才能显示背景图, 而IE6.0中不用使用clear: both;
   4. 在[text-decoration:underline]的属性下, IE6.0显示的下划线会比FireFox低一点. 在FireFox中, 部分笔画会在下划线的下面1个象素左右.
5. 非块级元素无法设宽高; float会把浮动元素变成块级元素; 绝对定位脱离文档流. span height继承div 所以高为200px 宽度auto由内容决定, i脱离文档流, span宽度为0.

        <div style=”width:400px;height:200px;”>
            <span style=”float:left;width:auto;height:100%;”>
                <i style=”position:absolute;float:left;width:100px;height:50px;”>hello</i>
            </span>
        </div>
  6. BFC: 满足以下一项即可成为BFC
  ```javascript
  float: left | right
  position: fixed | absolute
  display: inline-block | table-cell | table-caption | flex | inline-flex
  overflow: hidden | scroll | auto
  ```
    用BFC来做什么:1.外边距折叠; 2.容器无高度包含浮动元素; 3.阻止文字环绕

### 2017年9月10日
1. Flash提供了ExternalInterface接口与JavaScript通信, ExternalInterface有两个方法：call和addCallback. call让Flash调用js里的方法, addCallback是用来注册flash函数让js调用.
2. a:link {} a:visited {} a:hover {} a:active {}  (固定顺序: LoVe HAte 记忆口诀 爱与恨).
3. json字符串必须用双引号. stringify()把一个javascript对象序列化为json字符串, parse()把json字符串转为JavaScript对象. stringify还能传两个可选参数, 第一个是过滤器, 可以是数组,函数. stringify不传空格数是不包含空格字符和缩进的, 所以第二个参数是空格数(数值 最大为10), 可以传非数值. parse()有一个可选参数, 是一个函数(还原函数).
4. 跨域解决方案 CORS, ie8用XDomainRequest对象支持, 其他浏览器用XHR对象支持. 图像ping和jsonp也能解决跨域通信.
5. 作用域安全的构造函数(可能漏写new, 保证函数健壮), 惰性载入函数(不必每次执行if语句).
6. console.log(1+ +"2"+"2"); // 32   +"2" + => 一元运算符, 1+ +"2" 相当于1+2.

### 2017年9月9日
```JavaScript
function Foo(){
  var i=0;
  return function(){
    document.write(i++);
  }
}
var f1=Foo();
f1();
f1();
```
1. 上述题目答案是 0 1 别被var i = 0给迷惑, 这里考点是闭包的性质. 这里可以看成var f1 = function(){document.write(i++);}; 闭包可以通过作用域链读取上层变量, 另一个重要的性质就是会把这些变量的值保存在内存中, 所以f1()后 i = 1 保存在内存中.
2. console.log('Value is ' + (val != '0') ? 'define' : 'undefine'); 输出define, 前面的'Value is'是障眼法, 实际上考察三目运算符和符号优先级, 加号+优先级比? :高, 所以无论val有无定义, ('Value is' + (...))一定是true.
3. try-catch-finally, 如果代码包含finally语句, 无论是try还是catch的return语句都会被忽略. 因此有finally就不用写catch了, ie7之前需要写catch, finally才会执行. => 2017年10月12日更新, 当try块内某处发生异常, 调用catch内的代码逻辑. finally块放置清理代码. 不论try是否抛出异常, finally总会执行.

### 2017年9月1日
1. html5事件 contenxtmenu可以自定义右键菜单. 通过事件绑定的方式定义范围, 然后右键就可以触发写好的菜单(需要阻止默认行为和绝对定位用client来定位), 这里需要注意的是ie和高级浏览器的阻止默认行为写法不一样. ie: event.returnValue = false; 高级浏览器: event.preventDefault();

### 2017年8月31日
1. 事件委托可以解决多个事件监听内存多性能差的问题, 原理是利用了事件冒泡, 和event的target配合, 只添加一个事件处理程序, 用switch方法处理子节点的所有事件.

### 2017年8月30日
1. clientX, clientY pageX, pageY screenX, screenY区别
2. mouseenter, mouseleave mouseover, mouseout区别
3. click dblclick触发原理, mousedown mouseup click, mousedown mouseup click mousedown mouseup click dblclick.  mousedown, mouseup被取消, click失效
4. typeof function(){} => 'function'; typeof (function(){})() => 'undefined'  没有return都是undefined; typeof (function(){return 1})() => 'number'

### 2017年8月29日
addEventListener和attachEvent的一些细节
1. 给页面中最后一代监听addEvnetListener时, true和false都只会在事件冒泡阶段执行. 
2. 解除监听的时候需要和添加监听时候的参数相同
```javascript
var btn = document.getElementById('myBtn');
btn.addEventListener('click', function() {
  alert('hi');
}, false);

// 解除无效, 此时的 function(){} 并不是上述那一个.
btn.removeEventListener('click', function() {
  alert('hi');
}, false); 
```
解决的方法就是给函数一个变量, 传入变量即可.  
3.addEventListener执行顺序是从上往下, ie的attachEvent则相反. ie和高级浏览器取消冒泡和取消默认行为也不一样, 需要做兼容.

### 2017年8月28日 15:28:51
1. css选择器的问题关于.的连接, 如 li.open.menu 是找到li元素同时有open和menu的类. li.open#menu, 找到 id是menuclass是open的li, .open.menu是找到同时具有这两个类名的任意标签

### 2017年8月27日 19:23:49
1. 函数表达式和函数声明
```javascript
var getName = function () { console.log('hsbds'); };
function getName () { console.log('hssm'); }

getName();
```
输出结果为hsbds, 函数表达式在作用域里会变量提升, 而函数声明会函数提升, 理解了这一点就好办了, 可以想象函数声明的任何函数, 都是在作用域的最上方, 因此会被函数表达式覆盖.

### 2017年8月24日 13:06:05
1. 操作节点的四种方法 appendChild(), insertBefore(), replaceChild(), removeChild().
  最常用的appendChild(), 一个参数, 用于在父节点的类数组childNodes列表末尾添加一个节点. 添加完成后, childNodes的最后一个节点关系指针得到相应更新. 更新完成后, appendChild()返回新增节点.
```javascript
var returnedNode = someNode.appendChild(newNode);
alert(returnedNode == newNode);  // true
alert(newNode == someNode.lastChild);  // true
```
  如果要把节点放在childNodes的特定位置, 使用insertBefore(), 两个参数, 第一个是需要插入的节点, 第二个是插入节点的位置. 第二个参数如果是null, 和appendChild()效果一致.
```javascript
var returnedNode = someNode.insertBefore(newNode, null);
alert(returnedNode == someNode.lastChild);  // true

var returnedNode = someNode.insertBefore(newNode, someNode.firstChild);
alert(returnedNode == newNode);  // true
alert(newNode == someNode.firstChild); // true

var returnedNode = someNode.insertBefore(newNode, someNode.lastChild);
alert(returnedNode == newNode);  // true
alert(newNode == someNode.childNode[someNode.childNode.length-2]); // true  newNode位于倒二
```  

replaceChild(), 替换节点, 两个参数, 第二个参数是要移除的节点.
```javascript
// 替换第一个子节点 替换成newNode
var returnedNode = someNode.replaceChild(newNode, someNode.firstChild);
```

removeChild(), 移除节点, 一个参数.
```javascript
// 移除第一个子节点
var returnedNode = someNode.removeChild(someNode.firstChild);
```

还有cloneNode(), 可传参布尔值true, cloneNode(true)表示深拷贝, 反之为浅. 还要有上述的添加节点方法, 不然拷贝完的副本是没有父节点的, 不在Dom树上.

### 2017年8月23日 23:44:38
1. scss写伪类的时候会有bug
```javascript
  a {
    :hover {
      ...
    }
  }
```
输出的css是
```javascript
  a {
    ...
  }

  a :hover {  // => 相当于 a *:hover
    ...
  }
```
解决的方法是在:hover前面加上&即可.

### 2017年8月22日 21:27:25
1. encodeURI()和encodeURIComponent()的区别, 二者都可以将传递的字符串进行编码, 前者对(：;/?:@&=+$,#)不会进行转义, 重点就是这个斜杠/和:. 比如一段 http://github.com/p silo 前者输出http://github.com/p%20silo, 后者输出http%3A%2F%2Fgithub.com/p%20silo
2. 上述说到url地址, 针对url各个部分, BOM的location也要相当注意. http://github.com:80/psilocine#Home?name=psilo&password=123
    1. location.hash => 锚部分 #Home
    2. location.host => 主机名和端口 github.com:80
    3. location.hostname => 主机名 github.com
    4. location.href => 完整URL http://github.com:80/psilocine#Home
    5. location.pathname => 路径名 /psilocine
    6. location.port => 端口号 80
    7. location.protocol => 协议 http:
    8. location.search => 返回查询部分 ?name=psilo&password=123

### 2017年8月18日 13:53:23
1. 还是md文档的坑, 这次关于代码块如何显示, 为何我们的代码块有时候显示有时候不显示?
	 有下列方法可以抢救:
	 1. 在要显示的代码块前后在加上两行, 然后每行加上三个`, 注意这里不是单引号 是esc下面的那个, 有很多网站都说是单引号, 一度让我很头疼. 这种方法可以显示代码的行数. 还可以在第一行的```后面写上语言, 比如javascript.
	 2. 在代码块前面缩进4个空格, 这里(有时候)有个坑, 也是我最气的, 就是代码前一行必须要空一行, 就可以了. 当然你也可以在上一行末尾加上2个空格, md也会将其显示为空一行.
	 3. 最无脑的, 就是找下在线md编辑器, 在里面尝试下, 直到可以了后再发布, 省得发布修改发布.
    
### 2017年8月16日 22:02:50
1. md文档中的链接, [psilo的github](http://github.com/)psilocine)如果链接里面有括号, 这种方法不适用, 会提前终止.  
   这时候就要选择其他的方法:
   1. 第一, 可以用转义, 左右括号在href里的表示分别是%28 %29.
   2. 用[psilo的github][id], 然后在文档的任意一行写上id的定义即可, 注意要加上中括号.
   3. 用[psilo的github]: 地址 然后同样在文档任意一行写上[psilo的github][]就行了.

### 2017年8月12日 15:48:39
1. 要知道DOMContentLoaded和window.onload的区别
2. 了解从输入url到得到html的详细过程、html css js渲染过程等
3. 横向nav li有间距. 这是因为用了inline-block后, html的文本节点也就是空格也会算进去,解决方法大概分以下几种:
    1. 用float left代替display inline-block;
    2. li标签之间不要有文本节点, (压缩后没此问题;
    3. 给ul一个font-size:0, (缺点是要在li再设font-size;

### 2017年8月9日 17:46:53
1. 原型的5个重要规则.
    1. 所有的引用类型(对象,数组,函数)都具有对象特征, 即可自由扩展属性.
    2. 所有的引用类型都有一个__proto__属性, 属性值是一个普通的对象.
    3. 所有的函数, 都有一个prototype属性, 属性值也是一个普通的对象.
    4. 所有的引用类型,__proto__属性值指向它的构造函数的'prototype'属性值.
    5. 当试图得到一个对象的某个属性时, 如果这个对象本身没有, 那么会去它的__proto__(它的构造函数的prototype)中寻找

### 2017年7月9日 17:47:06
1. z-index 只能在定位元素在奏效, 如position:absolute
2. 做nav时, 用ul li遇到图片和文字一起的时候, 默认文字在图片的baseline, 文字会往下移动, 这时候不用调负margin, 只用vertical middle就行

### 2017年5月20日 23:58:26
1. class命名最好大于等于三个字母, 如advertisement, 不要写ad (会被广告插件过滤掉,  写adv, avt

### 2017年5月18日 17:04:42
1. type='submit' 会刷新页面, 填入的数据都刷没了
2. hexo d更新时, 会把不是hexo的文件渲染掉(包括readme.md  解决方法就是在根目录的 _config.yml里面找到skip_render: 写入你不想被渲染掉的文件, 然后把文件放入public即可.
3. MongoDB连接时, mongod --dbpath 这条的路径最好在根目录下建个目录, 即一层目录, 不然会连接失败. 
4. .jade格式的文件不要用tab不然会错误, 用空格即可.(我用的notepad++来编辑, 在设置里可以把tab设置为空格

### 2017年3月22日 23:47:50
1. 掌握calc方法 css3重要方法 解决盒子溢出问题 2017年8月12日16:08:35 解决 calc在低版本ie不兼容
2. @media screen and (max/min-width:???px) { ... } 媒体查询, 能够让浏览器在不同宽度显示不同表现

### 2017年3月17日 18:27:50
1. 同一css里 background-size:cover; (自适应浏览器宽度)要放在 background-image:url(); 后面, 不然不起作用.
2. li里面的a标签 用display:inline-block  height100% width100% 可以扩充到整个li, hover时比较美观.
3. eg: .demo:hover~ .box 是hover demo类时 box类发生变化  前提是.box的css要有transition等属性 注意不同浏览器不同写法

### 2017年3月12日 22:19:58
1. 学会绘制三角形css方法 (虽然以后学bs好像更简洁
	用span或其他标签做个空容器,然后css写border
	border-left:5px solid transparent
	border-right:5px solid transparent	(注意这里的5px可改 不过要左右相等 才能对称
	border-bottom:10px solid 颜色自定义 (注意这里的10px可改  不过要是左右的和 才能是等边三角形
	display:inline-block; 这里inline-block是能让后面的在同一行显示

### 2017年3月2日 00:14:38
1. 理解文本流和文档流(normal flow)区别 =>2017年5月20日 00:14:04解决
2. 什么是dom和cssom => 2017年8月12日16:05:14 解决
3. box-sizing干什么的 => 2017年8月12日16:05:26 解决 两种模式下区别

### 2017年3月1日 00:05:24
1. 双飞翼布局和圣杯布局 要搞透 =>2017年5月20日 00:13:49 解决
2. 文本首行缩进用css { text-indent: 属性值 ;} 

### 2017年2月26日 22:02:37
1. li横向排列 用display:inline
2. 顶部浮动float的导航栏 下方要清除浮动clear:both/left/right 不然会遮挡住 补充 2017年3月1日 00:07:46  clearfix的多种方法,优缺点 =>2017年5月20日 00:10:01解决 clear:both 添加div破坏语义; overflow:hidden 会影藏超出父元素的内容; ::after伪元素 没有明显弊端,适合使用
3. 顶部导航栏左右两边有空隙 待解决/ok => 2017年2月26日 23:19:50解决 浏览器自己的坑 本身浏览器body有margin和padding, 需要把其二清除.

### 2017年2月26日 23:03:14 
1. 补充一点, 用notepad++写 用utf-8 无ROM 编写 上传github 中文会乱码!