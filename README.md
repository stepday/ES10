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
export default{
	name:"stepday",
	sex:"男"
}

//编写一个index.html 加入我们需要在页面某个代码区块内动态导入module.js

方式一：用Promise的机制

import(`./module.js`).then(module =>{
	console.log(module.default.name); //stepday 
})
.catch(err => {
	throw new Error(err.message);
});

方式二：采用async + await
async loadModule function(){
	const module = await import(`./module.js`);
	
	console.log(module.default.name);// stepday
}
```
更加详细的介绍可[点这里](https://github.com/tc39/proposal-dynamic-import)




