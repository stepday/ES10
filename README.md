# ES10
```
ES2019/ES10 虽然还只是一个草案，但是除了Obejct.fromEntries 之外，Chrome浏览器已经支持大多数新特性了。
```

## ES10 新特性成员 概览
```
ES10 新增了8个特性，分四个阶段,目前已经进行到了第三阶段，第三阶段目前还有三个特性未明。
```
[ECMA官方参考地址](https://github.com/tc39/proposals)

- globalThis:用于在任何平台上访问全局作用域

> 出现这个东西主要是为了在各大平台统一，以前获取全局对象的方式各异，可以通过self、this、window、frames、this获取,这样在有些地方会引起错乱。

```
//比如定义了全局的变量
var a = 100;
//可以通过globalThis.a 来进行提取

//访问全局数组构造函数
globalThis.Array(1,2,3,)
=> [1,2,3]

```

- import():动态导入模块文件 

> 由于import是异步的 所以需要采用Promise或async await来进行处理

```
// 编写模块脚本文件 module.js
export default {
	name: "stepday",
	sex: "男",
}

export const user = {
	name: "stepday",
	sex: "男",
}

export var age=30;



//编写一个index.html 加入我们需要在页面某个代码区块内动态导入module.js

方式一：用Promise的机制

import(`./module.js`).then(module =>{
	console.log(module.default.name); //stepday 
	console.log(module.user.sex); //男
})
.catch(err => {
	throw new Error(err.message);
});

方式二：采用async + await
async loadModule function(){
	const module = await import(`./module.js`);
	
	console.log(module.default.name);// stepday
	console.log(module.age); //30
}
```
更加详细的介绍可[点这里](https://github.com/tc39/proposal-dynamic-import)

- BigInt:定义任意精度整数
> Number类型最大值是2<sup>53</sup>,在过去不支持大于9007199254740992的整数值，如果超过，该值将锁定为 Numer.MAX_SAFE_INTEGER + 1

```
const x = Number.MAX_SAFE_INTEGER;
// ↪ 9007199254740991, this is 1 less than 2^53

const y = x + 1;
// ↪ 9007199254740992, ok, checks out

const z = x + 2
// ↪ 9007199254740992, wait, that’s the same as above!
```
> BigInt 是一种新型的数据类型
```
typeof 1n; // bigint
```
这里查看[BigInt详情](https://github.com/tc39/proposal-bigint)

- import.meta: 目前还未支持
> 官方已经有详解[猛戳这里](https://github.com/tc39/proposal-import-meta)

- matchAll(): 类似正则匹配，返回迭代器类型 可用for of 进行结果遍历 [暂无浏览器支持]
> ES6内已经支持了字符串首尾查找的判断 match 、startsWith 和 endsWith 函数，ES10内的matchAll()  
> match()只返回第一个匹配 matchAll() 会将匹配到的都返回 更多细节[猛戳这里](https://github.com/tc39/proposal-string-matchall)
```
// Match all occurrences of the letters: "e" or "l" 
let iterator = "hello".matchAll(/[el]/);
for (const match of iterator)
    console.log(match);
```

- Array.flat(): 扁平化多维数组
```
let multi = [1,2,3,[4,5,6,[7,8,9,[10,11,12]]]];
multi.flat();               // [1,2,3,4,5,6,Array(4)]
multi.flat().flat();        // [1,2,3,4,5,6,7,8,9,Array(3)]
multi.flat().flat().flat(); // [1,2,3,4,5,6,7,8,9,10,11,12]
multi.flat(Infinity);       // [1,2,3,4,5,6,7,8,9,10,11,12]
```

- Array.flatMap():直接将遍历结果扁平化

```
//定义一个一维数组
let array = [1, 2, 3, 4, 5];

现在我们想得到一个 [1,2,2,4,3,6,4,8,5,10] 的数组怎么做最快呢？
重新定义一个空数组
let arr = [];
array.map( x => arr = arr.concat([x,x*2]) );

这个方法是不是有点繁琐,既然有flat()直接扁平化数组，索性就直接遍历的时候就返回扁平化数组

array.flatMap(v => [v, v * 2]);
=> [1, 2, 2, 4, 3, 6, 4, 8, 5, 10]
是不是很屌！

```

- Object.fromEntries()： 将键值对列表转换为对象

```
Object.entrides() 将对象快速转换为键值对列表

Object.entrides({
	name:"stepday",
	sex:"男"
});

=>
0: (2) ["name", "stepday"]
1: (2) ["sex", "男"]


如何快速将键值对列表转换为对象？按照老套的方式只能这样干!
var obj = [["name","stepday"],["sex","男"]];

var target = {}; //定义一个空对象
obj.forEach(item => {
	target[item[0]] = item[1];
});

console.log(target) => {name: "stepday", sex: "男"}

如果用fromEntries() 直接一句代码搞定
Object.fromEntries(obj) => {name: "stepday", sex: "男"}

So Easy!!!
```

- String.trimStart() 和 String.trimEnd()  去掉字符串首尾的空格
> 没有这个函数之前都是用正则去解决这个事情的

```
//正则去掉字符串前面的空格
"  stepday".replace(/^\s*/g,'') // "stepday"
//正则去掉字符串后面的空格
"  stepday  ".replace(/\s*$/g,'') // "  stepday"

//去掉首尾的正则
" name is ".replace(/(\s*$)|(^\s*)/g,'') // "name is"

针对String的原型方法目前已经提供了trim()函数去掉首尾空格

这次单独增加了首尾空格的替换函数,算是一个补充
```

- JSON.stringify(): 优化了这个JSON转字符串的函数 让其格式更良好
> 此更新修复了字符 U+D800 到 U+DFFF 的处理，有时可以进入 JSON 字符串。 这可能是一个问题，因为 JSON.stringify 可能会将这些数字格式化为没有等效 UTF-8 字符的值, 但 JSON 格式需要 UTF-8 编码

```
JSON.stringify({name:"stepday",age:20});
=>
'{"name":"stepday","age":20}'
//将属性都加上了双引号


```

- Array.prototype.sort(): 更加稳定
> V8 之前的实现对包含10个以上项的数组使用了一种不稳定的快速排序算法 <br/> 一个稳定的排序算法是当两个键值相等的对象在排序后的输出中出现的顺序与在未排序的输入中出现的顺序相同时

```
var fruit = [
    { name: "Apple",      count: 13, },
    { name: "Pear",       count: 12, },
    { name: "Banana",     count: 12, },
    { name: "Strawberry", count: 11, },
    { name: "Cherry",     count: 11, },
    { name: "Blackberry", count: 10, },
    { name: "Pineapple",  count: 10, }
];
// 创建排序函数:
let my_sort = (a, b) => a.count - b.count;
// 执行稳定的ES10排序:
let sorted = fruit.sort(my_sort);
console.log(sorted);
=> 是直接倒序的 即使count值一样 也会按照初始的状态进行严格先后顺序排序

//不稳定额情况输出是这样子的
0: {name: "Blackberry", count: 10}
1: {name: "Pineapple", count: 10}
2: {name: "Strawberry", count: 11}
3: {name: "Cherry", count: 11}
4: {name: "Pear", count: 12}
5: {name: "Banana", count: 12}
6: {name: "Apple", count: 13}

count=10 11 12 的顺序 都没有按照sort之前的那样排列了的

```

- Symbol.description: 返回Symbol对象的可选描述 是一个只读属性

```
let mySymbol = 'My Symbol';
let symObj = Symbol(mySymbol);
symObj; // Symbol(My Symbol)
symObj.description; // "My Symbol"
```

- 类的私有private、static和公共成员
> 新的语法字符 #octothorpe（hash tag）现在用于直接在类主体的范围内定义变量，函数，getter 和 setter ......以及构造函数和类方法。

```
这个还在讨论阶段，关于这个的讨论也是相当的激烈，我个人是很讨厌这种用#来表示私有的。

class Counter extends HTMLElement {
  #xValue = 0;

  get #x() { return #xValue; }
  set #x(value) {
    this.#xValue = value; 
    window.requestAnimationFrame(this.#render.bind(this));
  }

  #clicked() {
    this.#x++;
  }

  constructor() {
    super();
    this.onclick = this.#clicked.bind(this);
  }

  connectedCallback() { this.#render(); }

  #render() {
    this.textContent = this.#x.toString();
  }
}
window.customElements.define('num-counter', Counter);

你看了这个类是不是很蛋疼，还不如直接用private和public来很容易理解
```
更多详情[猛戳这里](https://github.com/tc39/proposal-private-methods)