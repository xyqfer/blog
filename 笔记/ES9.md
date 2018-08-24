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


