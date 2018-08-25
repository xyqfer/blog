# ES9

## s (dotAll) flag for regular expressions

### 参考资料
- [TC39](https://github.com/tc39/proposal-regexp-dotall-flag)

### 笔记
- In regular expression patterns, the dot `.` matches a single character, regardless of what character it is. In ECMAScript, there are two exceptions to this:
	- `.` doesn’t match astral characters. Setting the `u` (`unicode`) flag fixes that
	- `.` doesn’t match line terminator characters
- When both the `s` (`dotAll`) and `m` (`multiline`) flags are set, `.` matches any character while still allowing `^` and `$` to match, respectively, just after and just before line terminators within the string.

### 例子
``` js
const re = /foo.bar/s; // Or, `const re = new RegExp('foo.bar', 's');`.
re.test('foo\nbar');
// → true
```

## RegExp Named Capture Groups

### 参考资料
- [TC39](https://github.com/tc39/proposal-regexp-named-groups)

### 笔记
- A capture group can be given a name using the `(?<name>...)` syntax, for any identifier `name`
- A named group can be accessed within a regular expression via the `\k<name>` construct
- Named references can also be used simultaneously with numbered references
- Named groups can be referenced from the replacement value passed to `String.prototype.replace` too. If the value is a string, named groups can be accessed using the $<name> syntax
- If the second argument to `String.prototype.replace` is a function, then the named groups can be accessed via a new parameter called groups. The new signature would be `function (matched, capture1, ..., captureN, position, S, groups)`. Named captures would still participate in numbering, as usual
- Properties are created on the `groups` object for all groups which are mentioned in the RegExp; if they are not encountered in the match, the value is `undefined`
- `\k<foo>` in non-Unicode RegExps will continue to match the literal string `"k<foo>"` unless the RegExp contains a named group, in which case it will match that group or be a syntax error, depending on whether or not the RegExp has a named group named `foo`

### 例子
``` js
let re = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/u;
let result = re.exec('2015-01-02');
// result.groups.year === '2015';
// result.groups.month === '01';
// result.groups.day === '02';

// result[0] === '2015-01-02';
// result[1] === '2015';
// result[2] === '01';
// result[3] === '02';

let duplicate = /^(?<half>.*).\k<half>$/u;
duplicate.test('a*b'); // false
duplicate.test('a*a'); // true

let triplicate = /^(?<part>.*).\k<part>.\1$/u;
triplicate.test('a*a*a'); // true
triplicate.test('a*a*b'); // false

let re = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/u;
let result = '2015-01-02'.replace(re, '$<day>/$<month>/$<year>');
// result === '02/01/2015'

let re = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/u;
let result = '2015-01-02'.replace(re, (...args) => {
 let {day, month, year} = args[args.length - 1];
 return `${day}/${month}/${year}`;
});
// result === '02/01/2015'
```

## Rest/Spread Properties

### 参考资料
- [TC39](https://github.com/tc39/proposal-object-rest-spread)

### 笔记
- Shallow Clone (excluding prototype)
- only iterates over **own** properties
- **duplicate keys are allowed** because this enables the functional style updating of records
	- property order in the object literal is **order dependent**
- Getters are **executed** on any source rather than copying the descriptors over to the new object. This seems natural and is consistent with Object.assign
- Properties on object literals are defined as new value on the object. It does **not invoke** any newly defined setters. This is different than what Object.assign on a recently defined object does

### 例子
``` js
let oldRecord = { x: 1, y: 2 };
let newRecord = { ...oldRecord, y: 3 };
newRecord; // { x: 1, y: 3 }

let oldRecord = { x: 1, y: 2 };
let newRecord = { y: 3, ...oldRecord };
newRecord; // { x: 1, y: 2 }

let record = { x: 1 };
let obj = { set x() { throw new Error(); }, ...record }; // not error
obj.x; // 1

let emptyObject = { ...null, ...undefined }; // no runtime error

function ownX({ ...properties }) {
  return properties.x;
}
ownX(Object.create({ x: 1 })); // undefined

let { x, y, ...z } = a;
// is not equivalent to
let { x, ...n } = a;
let { y, ...z } = n;
// because x and y use the prototype chain

let { x, y, ...z } = null; // runtime error
let { ...x, y, z } = obj; // syntax error
let { x, ...y, ...z } = obj; // syntax error

var o = Object.create({ x: 1, y: 2 });
o.z = 3;

var x, y, z;

({ x, ...{ y, z } } = o);
x; // 1
y; // undefined
z; // 3
```

## RegExp Lookbehind Assertions

### 参考资料
- [TC39](https://github.com/tc39/proposal-regexp-lookbehind)

### 笔记
- 之所以叫后行断言，是因为正则表达式引擎在匹配字符串和表达式时，是从前向后逐个扫描字符串中的字符，并判断是否与表达式符合，当在表达式中遇到后行断言时，正则表达式引擎需要往字符串前端检测已扫描过的字符，相对于扫描方向是向后的

### 例子
``` js
const
  reLookbehind = /(?<=\D)\d+/,
  match        = reLookbehind.exec('$123.89');

console.log( match[0] ); // 123.89

const
  reLookbehindNeg = /(?<!\D)\d+/,
  match           = reLookbehind.exec('$123.89');

console.log( match[0] ); // null
```

## RegExp Unicode Property Escapes

### 参考资料
- [TC39](https://github.com/tc39/proposal-regexp-unicode-property-escapes)

### 例子
``` js
const reGreekSymbol = /\p{Script=Greek}/u;
reGreekSymbol.test('π'); // true
```

## Promise.prototype.finally

### 参考资料
- [TC39](https://github.com/tc39/proposal-promise-finally)

### 笔记
- `promise.finally(func)` is similar to `promise.then(func, func)`, but is different in a few critical ways:
	- When creating a function inline, you can pass it once, instead of being forced to either declare it twice, or create a variable for it
	- A `finally` callback will not receive any argument, since there's no reliable means of determining if the promise was fulfilled or rejected. This use case is for precisely when you do not care about the rejection reason, or the fulfillment value, and so there's no need to provide it
	- Unlike `Promise.resolve(2).then(() => {}, () => {})` (which will be resolved with undefined), `Promise.resolve(2).finally(() => {})` will be resolved with 2
	- Similarly, unlike `Promise.reject(3).then(() => {}, () => {})` (which will be resolved with undefined), `Promise.reject(3).finally(() => {})` will be rejected with 3
-  a `throw` (or returning a rejected promise) in the `finally` callback will reject the new promise with that rejection reason


### 例子
``` js
let isLoading = true;

fetch(myRequest).then(function(response) {
    var contentType = response.headers.get("content-type");
    if(contentType && contentType.includes("application/json")) {
      return response.json();
    }
    throw new TypeError("Oops, we haven't got JSON!");
  })
  .then(function(json) { /* process your JSON further */ })
  .catch(function(error) { console.log(error); })
  .finally(function() { isLoading = false; });
```

## Asynchronous Iteration

### 参考资料
- [TC39](https://github.com/tc39/proposal-async-iteration)

### 笔记
- Async for-of statements are only allowed within async functions and async generator functions

### 例子
``` js
async function process(array) {
  for await (let i of array) {
    doSomething(i);
  }
}
```


