# JavaScript深入之从原型到原型链

> 原文地址：[JavaScript深入之从原型到原型链](https://github.com/mqyqingfeng/Blog/issues/2)

## 笔记

- 不仅仅 `Function.__proto__ === Function.prototype`，`Object.__proto__ === Function.prototype` 也等于，`Array.__proto__ === Function.prototype` 也等于。
仔细想想就能明白，`Function`，`Object`，`Array` 最根本还是函数`fn()`，这跟我们自定义类（通常大写）的原理一致，也就是为什么 `typeof Array`、`typeof Object`、`typeof Function` 的结果都是 `function`。
我们很容易知道 `Function`、`Object`、`Array` 其实都是 `Function` 的实例，所以 `Function.__proto__ === Function.prototype`、`Object.__proto__ === Function.prototype`。
js的基础类型（除了 `null` 和 `undefined`）都是方法，都是为了生成对象（包括函数对象）的，所以它们都继承至 `Function`
- 首先 `new` 的时候 产生ECMA原生对象，接着此对象的 `[[Prototype]] `属性是根据 函数 `PERSTON` 的 `prototype` 来设定的，如果此时 `prototype` 是一个对象，`[[Prototype]]` = 那个设定的对象,如果不是一个对象，设定 `obj` 的 `[[Prototype]]` 内部属性为 `Object` 的 `prototype` 对象

