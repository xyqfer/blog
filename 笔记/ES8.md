# ES8

## Object.values

### 参考资料
- [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_objects/Object/values)
- [ECMA](https://tc39.github.io/ecma262/#sec-object.values)
- [TC39](https://github.com/tc39/proposal-object-values-entries)

### 笔记
- `Object.values()` 方法返回一个给定**对象自己**的所有**可枚举**属性值的数组，值的顺序与使用 `for...in` 循环的顺序相同
	- 区别在于 `for-in` 循环枚举**原型链**中的属性
- 语法：
	- Object.values(obj)
		- `obj`：被返回可枚举属性值的对象
	- 返回：一个包含对象自身的所有**可枚举**属性值的**数组**
- when we use **numeric keys**, the value returned in a **numerical order** according to the keys
- non-object argument will be coerced to an object

### 例子
``` js
var obj = { foo: 'bar', baz: 42 };
Object.values(obj); // ['bar', 42]

var an_obj = { 100: 'a', 2: 'b', 7: 'c' };
Object.values(an_obj); // ['b', 'c', 'a']

Object.values('foo'); // ['f', 'o', 'o']
```

## Object.entries

### 参考资料
- [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/entries)
- [ECMA](https://www.ecma-international.org/ecma-262/8.0/#sec-object.entries)
- [TC39](https://github.com/tc39/proposal-object-values-entries)

### 笔记
- `Object.entries()` 方法返回一个给定对象**自身可枚举**属性的键值对数组，其排列与使用 `for...in` 循环遍历该对象时返回的顺序一致
	- 区别在于 `for-in` 循环也枚举**原型链**中的属性
- 语法：
	- Object.entries(obj)
		- obj：可以返回其可枚举属性的键值对的对象
	- 返回：给定对象**自身可枚举**属性的**键值对数组**
- when we use **numeric keys**, the value returned in a **numerical order** according to the keys
- non-object argument will be coerced to an object

### 例子
``` js
const obj = { foo: 'bar', baz: 42 };
Object.entries(obj); // [ ['foo', 'bar'], ['baz', 42] ]

const anObj = { 100: 'a', 2: 'b', 7: 'c' };
Object.entries(anObj); // [ ['2', 'b'], ['7', 'c'], ['100', 'a'] ]

Object.entries('foo'); // [ ['0', 'f'], ['1', 'o'], ['2', 'o'] ]
```

## String.prototype.padStart

### 参考资料
- [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/padStart)
- [TC39](https://github.com/tc39/proposal-string-pad-start-end)
- [ECMA](https://www.ecma-international.org/ecma-262/8.0/#sec-string.prototype.padstart)

### 笔记
- `padStart()` 方法用另一个字符串填充当前字符串(重复，如果需要的话)，以便产生的字符串达到给定的长度。填充从当前字符串的开始(**左侧**)应用的
- 语法：
	- String.prototype.padStart(targetLength)
		- targetLength：当前字符串需要填充到的目标长度
			- 如果这个数值小于等于(**not greater than**)当前字符串的长度，则返回当前字符串本身
	- String.prototype.padStart(targetLength, padString)
		- padString：填充字符串
			- 如果字符串太长，使填充后的字符串长度超过了目标长度，则只保留最左侧的部分，其他部分会被截断
			- 此参数的缺省值为 " "（**U+0020**）
	- 返回：在原字符串**开头**填充指定的填充字符串直到**目标长度**所形成的**新字符串**

### 例子
``` js
'abc'.padStart(10) // "       abc"
'abc'.padStart(10, "foo") // "foofoofabc"
'abc'.padStart(1) // "abc"
```

## String.prototype.padEnd

### 参考资料
- [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/padEnd)
- [TC39](https://github.com/tc39/proposal-string-pad-start-end)
- [ECMA](https://www.ecma-international.org/ecma-262/8.0/#sec-string.prototype.padend)

### 笔记
- `padEnd()` 方法会用一个字符串填充当前字符串（如果需要的话则重复填充），返回填充后达到指定长度的字符串。从当前字符串的末尾（**右侧**）开始填充
- 语法：
	- String.prototype.padEnd(targetLength)
		- targetLength：当前字符串需要填充到的目标长度
			- 如果这个数值小于等于(**not greater than**)当前字符串的长度，则返回当前字符串本身
	- String.prototype.padEnd(targetLength, padString)
		- padString：填充字符串
			- 如果字符串太长，使填充后的字符串长度超过了目标长度，则只保留最左侧的部分，其他部分会被截断
			- 此参数的缺省值为 " "（**U+0020**）
	- 返回：在原字符串**末尾**填充指定的填充字符串直到目标长度所形成的**新字符串**

### 例子
``` js
'abc'.padEnd(10) // "abc       "
'abc'.padEnd(10, "foo") // "abcfoofoof"
'abc'.padEnd(1) // "abc"
```

## Object.getOwnPropertyDescriptors

### 参考资料
- [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptors)
- [TC39](https://github.com/tc39/proposal-object-getownpropertydescriptors)
- [ECMA](https://www.ecma-international.org/ecma-262/8.0/#sec-object.getownpropertydescriptors)

### 笔记
- `Object.getOwnPropertyDescriptors()` 方法用来获取一个对象的所有自身属性的描述符

### 例子
``` js
const shallowClone = (object) => Object.create(
  Object.getPrototypeOf(object),
  Object.getOwnPropertyDescriptors(object)
);

const shallowMerge = (target, source) => Object.defineProperties(
  target,
  Object.getOwnPropertyDescriptors(source)
);
```

## Trailing commas(尾后逗号)

### 参考资料
- [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Trailing_commas)
- [TC39](https://github.com/tc39/proposal-trailing-function-commas)

### 笔记
- ECMAScript 2017 支持**函数参数**中的尾后逗号
- JSON 不支持尾后逗号
- 仅仅包含逗号的函数参数定义或者函数调用会抛出 SyntaxError
- 当使用剩余参数的时候，不支持尾后逗号

### 例子
``` js
var arr = [
  1, 
  2, 
  3, 
];
arr; // [1, 2, 3]
arr.length; // 3

var arr = [1, 2, 3,,,];
arr.length; // 5

var object = { 
  foo: "bar", 
  baz: "qwerty",
  age: 42,
};

function f(p,) {}
```

## Async Functions

### 参考资料
- [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function)
- [TC39](https://github.com/tc39/ecmascript-asyncawait)
- [ECMA](https://www.ecma-international.org/ecma-262/8.0/#sec-async-function-definitions)

### 笔记
- `await` 关键字仅仅在 `async function` 中有效
- `async function` 的返回值将隐式传递给 `Promise.resolve`

### 例子
``` js
function resolveAfter2Seconds(x) {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(x);
    }, 2000);
  });
}

async function add1(x) { 
  var a = await resolveAfter2Seconds(20); 
  var b = await resolveAfter2Seconds(30); 
  return x + a + b; 
}
 
add1(10).then(v => { 
  console.log(v); // prints 60 after 4 seconds. 
});
```

