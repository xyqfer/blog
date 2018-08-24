# ES7

## Array.prototype.includes

### 参考资料
- [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)
- [TC39](https://github.com/tc39/Array.prototype.includes)
- [ECMA](http://www.ecma-international.org/ecma-262/7.0/index.html#sec-array.prototype.includes)
- [JavaScript 中的相等性判断](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Equality_comparisons_and_sameness)

### 笔记
- 语法
	- `Array.prototype.includes(searchElement)`
		- `searchElement`：需要查找的元素值
	- `Array.prototype.includes(searchElement, fromIndex)`
		- `fromIndex`：从该索引处开始查找 `searchElement`
		- 如果为负值，则按升序从 `length + fromIndex` 的索引开始搜索；如果计算出的索引小于 `0`，则整个数组都会被搜索
		- 如果 `fromIndex` 大于等于数组长度，则返回 `false` 。该数组不会被搜索
		- 默认为 `0`
	- 返回值：`true` 或 `false`
- 原为 `Array.prototype.contains`，后因[兼容性问题](https://esdiscuss.org/topic/having-a-non-enumerable-array-prototype-contains-may-not-be-web-compatible)(`MooTools `在 `Array.prototype` 上添加了 `contains` 方法)改为 `Array.prototype.includes`
- 使用 `SameValueZero` 进行比较，因此 `[NaN].includes(NaN) === true`
	- `Array.prototype.indexOf` 使用 Strict Equality Comparison (`===`) 进行比较，因此 `[NaN].indexOf(NaN) === -1`
- 不会跳过 `missing array elements`，会被当做 `undefined` 对待

	``` js
	[,,].includes(undefined) // true
	[,,].indexOf(undefined) === -1 // true
	```
- 为什么使用 `includes` 而不是 `has`？
	- `has` 习惯用于 "keys"，如 `has(key)`
	- `includes` 习惯用于 "values"
- 比较方法：
	- Abstract Equality Comparison (==)：抽象相等比较
	- Strict Equality Comparison (===)：严格相等比较
		- 用于 `Array.prototype.indexOf`、`Array.prototype.lastIndexOf` 和 `case`
	- SameValueZero：同值零
		- 用于 `%TypedArray%` 和 `ArrayBuffer` 的 `constructor`，以及 `Map` 和 `Set`
	- SameValue：同值
		- 用于所有其他地方

### 例子
``` js
[1, 2, 3].includes(2) // true
[1, 2, 3].includes(4) // false

[1, 2, NaN].includes(NaN) // true

[1, 2, -0].includes(+0) // true
[1, 2, +0].includes(-0) // true

["a", "b", "c"].includes("a", 1) // false
["a", "b", "c"].includes("a", 100) // false
["a", "b", "c"].includes("a", -100) // true

[,,].includes(undefined) // true
```

## Exponentiation Operator

### 参考资料
- [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#%E5%B9%82_(**))
- [TC39](https://github.com/rwaldron/exponentiation-operator)
- [ECMA](http://www.ecma-international.org/ecma-262/7.0/index.html#sec-exp-operator)

### 笔记
- 禁止使用带歧义的幂运算表达式。比如，底数前不能紧跟一元运算符（`+/-/~/!/delete/void/typeof`）
- The result of base `**` exponent when base is `1` or `-1` and exponent is `+Infinity` or `-Infinity` differs from IEEE 754-2008. The first edition of ECMAScript specified a result of `NaN` for this operation, whereas later versions of IEEE 754-2008 specified `1`. The historical ECMAScript behaviour is preserved for compatibility reasons.

### 例子
``` js
2 ** 2 // 4

-2 ** 2 // Uncaught SyntaxError: Unexpected token **
NaN ** 2 // NaN

(1) ** (+Infinity) // NaN
(-1) ** (+Infinity) // NaN
(-1) ** (-Infinity) // NaN
```

 


