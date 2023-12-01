ss## var & let & const

- var 在 for 循环共享相同变量的引用，所以每次输出变量时值是相同的
- const 在 for 循环中创建变量时会报错，而在 for in 和 for of 中使用会创建新的绑定所以不会报错
- 在 const let 前使用 tpyeof 判断类型会报错

## 更好的 Unicode 支持

- Es5 时某些异体字是通过代理方式实现, 例如吉: 其 length 为 1 实际输出时为 2
- 同上在 Es6 时对异体字使用 charAt(0) 和 charAt(1)获取到的都是空字符串
- `新增`codePointAt()方法 用于返回字符串对应位置的码位
- `新增`String.formCodePoint() 通过码位生成对应字符 String.fromCodePoint(31119) 为福
- normaliza()方法 略
- 正则表达式 u 修饰符
  1. /^.$/u.test(text) 加上 u 后可以匹配用两个单元编码表示的日文字符
  2. 在不支持 ES6 的引擎中使用会导致语法错误，可用 try {var x = new RegExp(".", "u")} 来判断是否可用
- $\color{#f00}其他变更$
  - 字符串中子串识别
    1. `新增` includes()方法 字符串检测指定是否包含指定文本 返回 true false
    2. `新增` startsWith()方法 判断字符串起始位置是否为指定文本 返回 true false
    3. `新增` endsWith()方法 判断字符串结束位置是否为指定文本 返回 true false
  - repate() 方法 循环指定次数原字符串 "蓝瘦香菇".repate(2) => "蓝瘦香菇蓝瘦香菇"
  - 正则变更
    1. y 修饰符 略
    2. let re1 = /ab/i re2 = new RegExp(re1, 'g') 替换修饰符在 es6 能正常使用在 es5 会报错
    3. getFlags()方法 getFlags(/ab/g) 来获取正则的修饰符 'g'
  - 模版自变量
    1. ``正式的多行文本字符串的概念
    2. ${}字符串站位符 - 非严格模式下嵌入未定义变量也会报错
    3. String.raw() `` String.raw`第一行\第二行` 为 第一行\\第二行  ``

## 函数

- 默认参数

```
  // es5
  function gogogo(arg1, arg2, cb) {
    arg1 = arg1 || 114514
    cb = cb || function() {}
  }
  // es6
  function gogogo(arg1 = 114514, arg2, cb = function() {}) {
  }
```

- 默认参数表达式 略
- 默认参数死区

```
  function add(arg1=arg2, arg2) {
    return arg1 + arg2
  }
  add(undefined, 1)
  // 等同于
  let arg1 = arg2 // error!!!
  let arg2 = 1
```

- $\color{#f00}不定参数$

  1. function gogogo(arg1, ...args) {} args 数组形式
  2. 不定参数的限制 `只能放在末尾`
  3. 无论是否使用不定参数 arguments 对象始终是所有传入函数的参数

- 增强构造函数
  ```
    // new Function支持传入 默认参数和不定参数
    var nFun = new Function("...args", "return args[0] + args[1]")
    nFun(1, 2) // 3
  ```
- $\color{#f00}扩展运算符$
- 函数名 name 属性值

  - ```
    function funA(){}
    var funB = function() {}
    funA.name // funA
    funB.name // funB
    ```
  - name 属性的特殊情况

    ```
    var funA = function funB(){

    }
    funA.name // funB
    var funA = function(){}
    funA.bind().name() // bound funA
    new Function().name // anonymous
    ```

- 明确函数的多重用途 略
- 块级函数
- $\color{#da6}箭头函数$
  - 特性
    - 没有 this super arguments 和 new.target 的绑定
    - 不能用 new 调用
    - 没有原型 prototype 这个属性
    - 不支持 argument 对象
    - 不支持重复的命名参数
  - 创建立即执行函数表达式
    ```
      let person = ((name) => {
        return {
          getName: function() {
            return name;
          }
        }
      })('Ruriko')
      person.getName() // Ruriko
    ```
  - this 根据上下文决定
- 尾调用优化
  - es5 尾调用创建一个新的栈帧（stack frame）,推入调用栈表示函数调用（循环调用每个未用完的栈帧保存在内存中，过大时造成程序问题）
  - es6 缩减严格模式下尾调用栈的大小
    - 尾调不访问当前栈帧的变量（函数不是一个闭包）
    - 函数内部，尾调是最后一条语句
    - 尾调用的结果作为函数值返回

```
  // 阶乘例子
  function factorial(n) {
    if (n <= 1) {
      return 1
    } else {
      // 无法优化 必须在返回后执行乘法操作
      return n * factorial(n - 1)
    }
  }

  function factorial(n, p=1){
    if (n<=1) {
      return 1 * p
    } else {
      let result = n * p
      // 优化后
      return factorial(n - 1, result)
    }
  }
```

### 总结

- 函数最大改变添加了箭头函数(用以替代匿名函数): `语法简洁` `没有arguments对象` `函数内部this不可改变(不能作为构造函数)`
- 尾调优化使函数保持更小的调用栈,减少内存使用,满足优化条件时引擎自动优化
- 函数新增 name 属性: 有助于通过函数名称对其进行调试评估
- 普通函数触发函数[[call]]方法调用, new 关键词触发函数[[Construct]]方法调用。
  新增元属性 new.target 检测函数通过何种方式调用
- 展开运算符和不定参数

## 扩展对象的功能性

### 对象类别

- 普通对象（Ordinary）js 对象所有默认内部行为
- 特异对象（Exotic）具有与某些默认行为不符的内部行为
- 标准对象（Standard）Es6 规范中定义的对象 如 Array Date 等 标准对象可以是普通对象也可以是特异对象
- 内建对象 脚本开始执行时存在在 js 执行环境中的对象, 所有标准对象都是内建对象

### 对象字面量语法扩展

#### 属性初始值简写

    ```
      // es5
      (arg1, arg2) => {
        arg1: arg1
        arg2: arg2
      }
      // es6
      (arg1, arg2) => {
        arg1,
        arg2
      }
    ```

#### 对象简写

    ```
    // es5
      var objA = {
        name: 'Ruriko',
        sayNane: function() {
          console.log(this.name)
        }
      }
    // es6
    var objB = {
      name: 'Ruriko',
      sayName() {
        console.log(this.name)
      }
    }
    ```

#### 可计算属性名

```
  // es5
  var obj = {}, lastName = 'last name'
  obj['frist name'] = 'Ruriko'
  obj[lastName] = 'char'
  // es6
  var obj = {
  'frist name':'Ruriko'
  }
  // or
  var str = name
  var obj = {
    ['first'+str]: 'Ruriko',
    ['last'+str]: 'char'
  }

```

### 新增方法

#### Object.is() 补全全等运算符的不准确运算

```
+0 === -0 // true
Object.is(+0, -0) // false
NaN == NaN // false
Object(NaN, NaN) // true
5 == '5' // true
5 === '5' // false
Object(5, '5') // false
Object(5, 5) // true
```

#### Object.assign()

Object.assign(objA, objB) 同 {...objA, ...objB}

#### 重复对象字面量属性

```
  "use strict"
  // es5
  var obj = {
    name: 'Ruriko',
    name: 'Yuriko' // 严格模式下语法错误
  }
  // es6
  obj.name === 'Yuriko' // 没有错误并取最后一次赋值
```

#### 自有属性枚举顺序

- es6 严格规定自有属性枚举返回顺序(影响 Object.getOwnPropertyNames()以及 Reflect.ownKeys，Object.assign())
  1. 所有数字键升序排序
  2. 所有字符串键按加入对象的顺序排序
  3. symbol 键按照加入对象的顺序排序
  ```js
  var obj = {
    a: 1,
    0: 1,
    c: 1,
    2: 1,
    b: 1,
    1: 1,
  };
  obj.d = 1;
  Object.getOwnPropertyNames(obj.join("")); // 012acbd
  ```
  `for-in循环不同厂商无明确枚举顺序, 同Object.keys()与Object.string()也无明确顺序`

### 增强对象原型

#### 改变对象原型

`新增`Object.setPrototypeOf()

```js
let objA = {
  get() {
    return "A";
  },
};
let objB = {
  get() {
    return "B";
  },
};
// 以a为原型
let withA = Object.create(objA);
withA.get(); // 'A'
Object.getPrototypeOf(withA === objA); // true
// 将原型改为b
Object.setPrototypeOf(withA, objB);
withA.get(); // 'B'
Object.getPrototypeOf(withA === objB); // true
```

#### 简化原型链访问 Super 特性

```js
let objA = {
  get() {
    return "A";
  },
};
let objC = {
  get() {
    return Object.getPrototypeOf(this).get.call(this) + ",hi!";
  },
};
// 将原型改为a
Object.setPrototypeOf(objC, objA);
objC.get(); // 'A, hi!'
Object.getPrototypeOf(objC === objA); // true
// 等同
let objC = {
  get() {
    return super.get() + ",hi!";
  },
};
// 错误
let objC = {
  get: function () {
    // 必须在简写语法中使用
    return super.get() + ",hi!";
  },
};
```

`super解决多重继承问题`

```js
let objA = {
  get() {
    return "A";
  },
};
let objB = {
  get() {
    return Object.getPrototypeOf(this).get.call(this) + ",hi!";
  },
};
Object.setPrototypeOf(objB, objA)
let ObjC = Object.create(ObjB)
objC.get() // error

// super
let objB = {
  get (){
    super.get() =",hi"
  }
}
objC.get() // 'A, hi"
```

### 总结

- Object.assign()一次性修改对象多个属性, Object.is()严格等价判断
- Object.setPrototypeOf()在对象创建后修改他的原型
- super 关键字调用原型上方法, 此时 this 自动设置为当前作用域 this 值

## 解构：数据访问更加便捷

### 对象解构

```js
let obj= {
  keyA:'valueA'
  keyB:'valueB'
}
let{keyA, keyB} = obj
var {key, keyB} // 语法错误!!! var,let不强制提供初始化程序 const必须提供初始化程序
function echo(value) {
  console.log(value === obj) // true
}
echo({keyA,keyB}=obj)
let {keyA} = null // error
let {keyA} = undefined //error
```

### 默认值

```js
let obj = {};
let { keyA } = obj;
console.log(keyA); // undefined
```

### 非同名变量赋值

```js
let obj = { keyA: "valueA" };
const { keyA: keyB } = obj;
console.log(keyB); // "valueA"
const { keyC: keyD = "valueD" } = obj;
console.log(keyD); // "valueD"
```

### 对象嵌套结构

```js
let deepObj={
  loc: {
    start: {line: 1}
    end: {lane: 1}
  }
}
const{loc: {start}} = deepObj;
console.log(start.line) // 1
const{loc: {reStart}} = deepObj;
console.log(reStart.line) // 1
```

### 数组结构

#### 解构赋值

```js
let array= ['a1',a2','a3']
let [valA, valB] = array
// valA : 'a1' valB: 'a2'
let [,,valC] = array
// valC: 'a3'
```

#### 交换变量

```js
let a = 1,
  b = 2;
[a, b] = [b, a];
```

#### 默认值

```js
let array = ["valA"];
let [valA, valB = "valB"] = array;
console.log(valB); // 'valB'
```

#### 嵌套结构

```js
let array = ["valA", ["val1"], "valB"];
let [valA, [val1]] = array;
console.log(val1); // 'val1'
```

#### 不定元素

```js
let array = ["valA", "valB", "valC"];
let [valA, ...val] = array;
console.log(val); // ["valB", "valC"]
```

### 解构参数

```js
function setCookie(name, value, options = {}) {
  let { secure, path, domain, expires } = options;
}
setCookie("type", "js", { secure: true, expires: 6e5 });
// 重构后更加清晰 但不传第三个参数或第三个参数为null时会报错
function setCookie(name, value, { secure, path, domain, expires }) {}
// 添加默认值
function setCookie(name, value, { secure, path, domain, expires } = {}) {}
// 或者
function setCookie(
  name,
  value,
  {
    secure = false,
    path = "/",
    domain = "xxx.com",
    expires = new Date(Date.now() + 36e7),
  } = {
    secure: false,
    path: "/",
    domain: "xxx.com",
    expires: new Date(Date.now() + 36e7),
  }
) {}
```

#### 总结

- 解构: 对象模式从对象中提取数据 数组模式从数组中提取数据
- 赋值表达式右侧不可为 null 或 undefined

### Symbol 和 Symbol 属性

es5 五大原始类型: 字符串型、数字型、布尔型、null、undefined
es6 新增 Symbol

#### 创建 Symbol

```js
let name = Symbol();
let person = {};
person[name] = "Ruriko";
console.log(person[name]); // Ruriko
```

tips: `Symbol为原始值, new Symbol会报错`

```js
let name = Symbol("Ruriko");
let person = {};
person[name] = "Char";
console.log("Ruriko" in person); // false
console.log(person[name]); // char
console.log(name); // Symbol('Ruriko')
console.log(typeOf name); // symbol 通过typeOf 判断是否为symbol
```

#### Symbol 的使用方法

```js
let firstName = Symbol('first name')
let person = {
  [firstName]: 'Ruriko'
Object.defineProperties(person,firstName, {
    writable: false
})
}
let lastName = Symbol('last name')
Object.defineProperties(person, {
  [lastName]: {
    value: 'Char',
    writable: false
  }
})
console.log(person[firstName], person[lastName]); // Ruriko Char
```

#### Symbol 的共享 Symbol.for() 以及 Symbol.keyFor()

```js
let uid = Symbol.for("uid");
let object = { [uid]: "12345" };
let uid2 = Symbol.for("uid");
let uid3 = Symbol("uid");
console.log(uid === uid2); // true
console.log(uid === uid3); // false
console.log(Symbol.keyFor(uid)); // uid
console.log(Symbol.keyFor(uid3)); // undefined
// tips: 使用第三方组件时使用Symbol键的命名1空间以减少命名冲突
```

#### Symbol 与类型的强制转换 略

#### Symbol 属性检索 略

#### 通过 well-known Symbol 暴露内部操作

- Symbol.hasInstance 略
- Symbol.isCoactSpreadable 略
- Symbol.iterator 略
- Symbol.match 略
- Symbol.replace 略
- Symbol.search 略
- Symbol.species 略
- Symbol.split 略
- Symbol.toPrimitive 略
- Symbol.toStringTag 略
- Symbol.unescapable 略

## Set 和 Map 合集

### Set

#### Es6 中的 Set 集合

- set 中 -0 和 +0 是相等的,但数字字符串和数字间作为两个独立元素存在
- `set.has()` 检测是否存在指定值
- `set.size()` 获取 set 中值的数量
- `set.delete()` 删除 set 中的指定值
- `set.clear()` 移除集合中的所有值

#### Set 集合的 forEach 方法 略

#### Set 集合转换为数组

```
  let set = new Set([1,2,3,3,4])
  console.log([...set]) // [1,2,3,4]
```

#### Weak Set 集合 略

### Map

```js
let map = new Map();
map.set("key", "value");
console.log(map.get("key")); // 'value'
console.log(map.get("key2")); // undefined
```

#### Map 集合支持的方法

- `.has(key)` 检测指定键名在 Map 集合中是否存在
- `.delete(key)` 从 Map 集合中移除指定键名以及对应值

#### Map 集合的初始化方法

```js
let map = new Map([
  ["key", "value"],
  ["key1", "value1"],
]);
console.log(map.get("key")); // value
console.log(map.has("key")); // true
console.log(map.size); // 2
map.forEach((value, key, map) => {
  console.log(value, key, map); // 'value' 'key' map
});
```

#### weakMap

## 迭代器 Iterator 和生成器 Generator

### 迭代器

```js
function createIterator(items) {
  var i = 0;
  return {
    next: function () {
      var done = i >= items.length;
      var value = !done ? items[i++] : undefined;
      return {
        done,
        value,
      };
    },
  };
}
var iterator = createIterator([1, 2, 3]);
console.log(iterator.next()); // {value:1, done: false}
console.log(iterator.next()); // {value:2, done: false}
console.log(iterator.next()); // {value:3, done: false}
console.log(iterator.next()); // {value:undefined, done: true}
console.log(iterator.next()); // {value:undefined, done: true}
```

### 生成器

```js
function* createIterator() {
  yield 1;
  yield 2;
  yield 3;
}
let iterator = createIterator();
console.log(iterator.next().value); // 1
console.log(iterator.next().value); // 2
console.log(iterator.next().value); // 3
// 生成器实现迭代器
function* createIterator(items) {
  for (let i = 0; i < items.length; i++) {
    yield items[i];
  }
}
var iterator = createIterator([1, 2, 3]);
console.log(iterator.next()); // {value:1, done: false}
console.log(iterator.next()); // {value:2, done: false}
console.log(iterator.next()); // {value:3, done: false}
console.log(iterator.next()); // {value:undefined, done: true}
console.log(iterator.next()); // {value:undefined, done: true}
```

#### 生成器函数表达式

tips: 不能用箭头函数创建生成器

#### 生成器对象方法

```js
let obj = {
  createIterator: function* (item) {
    for (let i = 0; i > item.length; i++) {
      yield items[i];
    }
  },
};
// 简化
let obj = {
  *createIterator(items) {
    for (let i = 0; items.length; i++) {
      yield items[i];
    }
  },
};
let iterator = createIterator([1, 2, 3]);
```

### 可迭代对象和 for-of 循环

```js
// 将for-of 用于不可迭代的对象或null 或undefined会报错
for (let num of [1, 2, 3]) {
  console.log(num); // 1 2 3 在done值为true时退出 所以不会输出undefined
}
```

#### 访问默认迭代器 复杂略

#### 创建可迭代对象 复杂略

### 内建迭代器

#### 集合对象迭代器

- entries() 返回一个迭代器 其值为多个键值对
- values() 返回一个迭代器 其值为集合的值
- keys() 返回一个迭代器 其值为集合中的所有键名

```js
console.log([...["red", "green", "blue"].entries()]); // [[0,'red'],[1:'green'],[2:'blue']]
console.log(new Set([1,2,3]).entries()]); // [[1,1],[2:2],[3:3]]
console.log(new Map([['key','value'],['key1', 'value1']]).entries()); // [['key', 'value'],['key1', 'value1']]
```

#### 字符串迭代器

es5 无法通过 text[i]获取异体字
es6 支持通过 text[i]获取异体字

#### nodelist 迭代器 略

### 展开运算符与非数组可迭代对象 ...

### 高级迭代器功能

#### 给迭代器传参

```js
function* createIterator() {
  let first = yield 1;
  let second = yield first + 2; // 4 + 2
  yield second + 3; // 5 + 3
}
let iterator = createIterator();
console.log(iterator.next()); // {value: 1, done: false}
console.log(iterator.next(4)); // {value: 6, done: false}
console.log(iterator.next(5)); // {value: 8, done: false}
console.log(iterator.next()); // {value: undefined, done: false}
// next中传入参数替代上一次结果值
```

#### 在迭代器中抛出错误

```js
function* createIterator() {
  let first = yield 1;
  let second = yield first + 2; // 4 + 2
  yield second + 3; // 5 + 3
}
let iterator = createIterator();
console.log(iterator.next()); // {value: 1, done: false}
console.log(iterator.next(4)); // {value: 6: done: false}
console.log(iterator.throw(new Error("Boom"))); // 从生成器中抛出错误
// 捕获错误
function* createIterator() {
  let first = yield 1;
  let second;
  try {
    second = yield first + 2; // yield 4 + 2,然后抛出错误
  } catch (ex) {
    second = 6;
  }
  yield second + 3; // 5 + 3
}
console.log(iterator.next()); // {value: 1, done: false}
console.log(iterator.next(4)); // {value: 6: done: false}
console.log(iterator.throw(new Error("Boom"))); // {value: 9: done: false}
console.log(iterator.next()); // {value: undefined: done: true}
```

#### 生成器返回语句

```js
function* createIterator() {
  yield 1;
  return;
  yield 2;
  yield 3;
}
let iterator = createIterator();
iterator.next(); // {value:1, done: flase}
iterator.next(); // {value:undefined, done: true}
function* createIterator() {
  yield 1;
  return 114514;
  yield 2;
  yield 3;
}
let iterator = createIterator();
iterator.next(); // {value:1, done: flase}
iterator.next(); // {value:114514, done: false}
iterator.next(); // {value:undefined, done: true}
```

#### 委托生成器

给 yield 语句添加\*，即可将生成数据的过程委托给其他迭代函数

```js
function* createNumberIterator() {
  yield 1;
  yield 2;
}
function* createColorIterator() {
  yield "red";
  yield "green";
}
function* createCombinedIterator() {
  yield* createNumberIterator();
  yield* createColorIterator();
  yield true;
}
let iterator = createCombinedIterator();
console.log(iterator.next()); // {value:1, done: false}
console.log(iterator.next()); // {value:2, done: false}
console.log(iterator.next()); // {value:'red', done: false}
console.log(iterator.next()); // {value:'green', done: false}
console.log(iterator.next()); // {value:true, done: false}
console.log(iterator.next()); // {value:undefined, done: false}

// 例2
function* createNumberIterator() {
  yield 1;
  yield 2;
  return 3;
}
function* createRepeatingIterator(count) {
  for (let i = 0; i < count; i++) {
    yield "repeat";
  }
}
function* createCombinedIterator() {
  let result = yield* createNumberIterator();
  yield result; // 在这里输出3
  yield* createRepeatingIterator(result);
}
let iterator = createCombinedIterator();
console.log(iterator.next()); // {value: 1, done: false}
console.log(iterator.next()); // {value: 2, done: false}
console.log(iterator.next()); // {value: 3, done: false}
console.log(iterator.next()); // {value: 'repeat', done: false}
console.log(iterator.next()); // {value: 'repeat', done: false}
console.log(iterator.next()); // {value: 'repeat', done: false}
console.log(iterator.next()); // {value: undefined, done: true}
```

### 异步任务执行

```js
// node读取文件
let fs = require("fs");
fs.readFile("config.json", function (err, contents) {
  if (err) {
    throw err;
  }
  doSomeThingWith(contents);
  console.log("Done");
});
```

#### 简单任务执行器

```js
function run(taskDef) {
  // 创建一个无限制使用的迭代器
  let task = taskDef();
  // 开始执行任务
  let result = task.next();
  // 循环调用next()函数
  function step() {
    // 如果任务未完成将继续执行
    if (!result.done) {
      result = task.next();
      step();
    }
  }
  // 迭代开始执行
  step();
}
run(function* () {
  console.log(1);
  yield;
  console.log(2);
  yield;
  console.log(3);
});
// 循环输出 1, 2, 3
```

#### 向任务执行器传递数据

```js
function run(taskDef) {
  // 创建一个无限制使用的迭代器
  let task = taskDef();
  // 开始执行任务
  let result = task.next();
  // 循环调用next()函数
  function step() {
    // 如果任务未完成将继续执行
    if (!result.done) {
      result = task.next();
      step();
    }
  }
  // 迭代开始执行
  step();
}
run(function* () {
  let first = yield 1;
  console.log(first); // 1
  let second = yield first + 3;
  console.log(second); // 4
});
```

#### 异步任务执行器 暂时略

### 小结

- 降低 for-of 使用成本, es6 内置默认迭代器，例如数组、Map 集合、Set 集合
- 生成器是特殊函数 定义时额外添加\* 调用时额外生成迭代器，通过 yield 关键字标识每次迭代器 next 方法的返回值

## JavaScript 中的类

es5 通过 prototype 添加在构造函数上，new 操作符创建实例。

### 类的声明

```js
class PersonClass {
  // 等价于PersonType构造函数
  constructor(name) {
    this.name = name;
  }
  // 等价于PersonType.prototype.sayName
  sayName() {
    console.log(this.name);
  }
}
let person = new PersonClass("Ruriko");
person.sayName(); // Ruriko
```

tips: `于函数不同，类属性不可赋予新值，PersonClass.prototype只是可读的类属性`

#### 如何使用类语法

- 函数声明能被提升，而类和 let 相同，不能提升，在执行声明语句前一直存在在临时死区中
- 类声明中所有代码都执行在严格模式下，而且无法强制脱离严格模式执行
- 自定义类型中需要通过`Object.defineProperty()`方法手工指定不可枚举,而类所有方法都不可枚举
- 每个类都有[[Construct]]的内部方法，通过 new 关键字调用不含[[Construct]]的方法都会抛出错误
- 除 new 以外的关键字调用类都会抛出错误
- 在类中修改类名会抛出错误

### 类表达式

#### 基本类表达式语法 略

#### 命名类表达式

```js
let PersonClass = class PersonClass2 {
  constructor(name) {
    this.name = name;
  }
  sayName() {
    console.log(this.name);
  }
};
console.log(typeOf PersonClass); // 'function'
console.log(typeOf PersonClass2); // undefined
// 以上代码等同于
let PersonClass = (function () {
  "use strict";
  const PersonClass2 = function (name) {
    if (typeOf new.target === void 0) {
      return new Error("必须通过关键字new调用构造函数");
    }
    this.name = name;
  };
  Object.defineProperty(PersonClass2.prototype, "sayName", {
    value: function () {
      if (typeOf new.target === void 0) {
        return new Error("必须通过关键字new调用构造函数");
      }
      console.log(this.name);
    },
    enumerable: false,
    writable: true,
    configurable: true,
  });
  return PersonClass2;
})();
// 因此PersonClass2只能在函数内部使用
```

### 一等类

```js
let person = new (class {
  constructor(name) {
    this.name = name;
  }
  sayName() {
    console.log(this.name);
  }
})("Ruriko");
person.sayName(); // 'Ruriko'
```

### 访问器属性 暂时略

### 可计算成员名称

```js
let propertyName = "html";
class CustomHTMLElement {
  constructor(element) {
    this.element = element;
  }
  get [propertyName]() {
    return this.element.innerHTML();
  }
  set [propertyName](value) {
    this.element.innerHTML = value;
  }
}
```

### 类的生成器方法 略

### 静态方法

`static` 关键词来定义
不可在实例中访问静态成员,必须要直接在类中访问静态成员

### 继承与派生类

```js
class Rectangle {
  constructor(length, width) {
    this.length = length;
    this.width = width;
  }
  getArea() {
    return this.length * this.width;
  }
}
class Square extends Rectangle {
  consructor(length) {
    // 等同 Rectangle.call(this, langth, length)
    super(length, length);
  }
}
var square = new Square(3);
console.log(square.getArea()); // 9
console.log(square instanceof Square); // true
// 不使用构造函数时有默认构造函数
class Square extends Rectangle {
  // 没有构造函数
}
// 等同于
class Square extends Rectangle {
  constructor(...args) {
    super(...args);
  }
}
```

#### super 小结

- 只能在派生类中使用 super() ,在非派生类 extends 声明的类中使用会导致程序报错
- 构造函数在访问 this 之前一定要调用 super(),它负责初始化 this, 在 suoer()前调用 this 会报错
- 如果不想调用 super,只能在类的构造函数返回一个对象

#### 类方法遮蔽

```js
class Square extends Rectangle {
  constructor(length) {
    super(length, length);
  }
  getArea() {
    return this.length * this.length;
  }
}
class Square extends Rectangle {
  constructor(length) {
    super(length, length);
  }
  getArea() {
    return super.getArea();
  }
}
```

#### 静态成员继承 略

#### 派生自表达式的类 重要略

#### 内建对象的继承 略

#### Symbol.species 略

### 在类的构造函数中使用 new.target 暂时略

## 改进的数组功能

### 创建数组

#### Array.of() 重要略

#### Array.form() 重要略

### 为所以数组添加新方法

#### find() 和 findIndex()

```js
let number = [25, 30, 35, 40, 45];
console.log(number.find((n) => n > 33)); // 35 返回值
console.log(number.findIndex((n) => n > 33)); // 2 返回值的索引
```

#### fill()

```js
let numbers = [1, 2, 3, 4];
number.fill(1); // numbers: [1,1,1,1]
number.fill(1, 2); // numbers: [1,2,1,1]
number.fill(0, 1, 2); // numbers: [1,0,0,1] 从第1位开始修改为0总共2个
```

#### copyWithin()

```js
let numbers = [1, 2, 3, 4];
number.copyWithin(2, 0, 1); // [1,2,1,4] 从索引2开始粘贴从索引0到索引1的复制值
```

### 定型数组

#### 数值数据类型 略

#### 数据缓冲区 略

#### 通过视图操作数组缓冲区 略

### 定型数组与普通数组的相似之处

#### 通用方法

`map()` `forEach()` `indexOf()` `some()` `find()` `sort()` `join()` `slice()` `filter()` `findIndex()`<br> `copyWithin()` `lastIndexOf()` `entries()` `fill()` `reduce()` `reduceRight()` `values()` `keys()` `reverse()`

#### 相同的迭代器 略

#### of() 和 from() 方法

### 定型数组和普通数组差别

```js
let ints = new Int16Array([25, 50]);
console.log(ints instanceof Array); // false
console.log(Array.isArray(ints)); // false0
// 定型数组不继承与Array, 所以通过Array.isArray() 返回为false
```

#### 行为差距

- 定型数组长度不可改变 - 赋予原数组不存在索引的值时丢失, 赋值存在的值时 最终由 0 代替

```js
let ints = new Int16Array([25, 50]),
  mapped = ints.map((v) => "hi");
ints[2] = 999;
console.log(ints[2], mapped[0], mapped[1], mapped.length); // undefined 0 0 2
```

#### 丢失的方法（及改变数组长度的方法）

`concat()` `shift()` `pop()` `splice()` `push()` `unshift()`

#### 附加方法

- set()
- subarray()

### 小结

- 新增 Array.of() 和 Array.from()
- fill() 修改值 copyWithin() 复制修改值 find() 找值 findIndex() 找值的 index

## promise 与异步编程

### 异步编程的背景知识

- js 是基于单线程事件循环概念构建的，同一时间只允许一个代码块执行
- 同一时间只允许一个代码块执行，即将运行的代码块会被放在任务队列`事件循环`会在一段代码执行结束后执行下一个任务

#### 事件模型 略

#### 回调模式 略

### promise 基础知识

#### promise 的生命周期

- 生命周期 进行中（pending）-> 未处理（unsettled） -> 已处理（settled）->Fulfilled（异步操作完成） 或 Rejected（异步操作错误或其他原因失败）
  重要略
  <font color="red">待补充</font>

#### 创建未完成的 Promise

#### 创建已处理的 Promise

#### 执行器错误

### 全局 Promisse 拒绝处理

#### Node.js 环境中的拒绝处理

#### 浏览器环境中的拒绝处理

### 串联 Promise

#### 捕获错误

#### Promise 链的返回值

#### Promise 链中返回 Promise

### 响应多个 Promise

#### Promise.all() promise 全返回触发

#### Promise.race() promise 列第一个触发值

### 自 Promise 继承

### 基于 Promise 的异步任务执行

## Proxy(代理) 和 Reflection(反射)

proxy: 拦截并改变底层 js 引擎的包装器
略

## 用模块封装代码

### 基础导出 export

### 基础到吐 import

### 单个导入

```js
import { Button } from "antd";
```

### 多个导入

```js
import { Button, Input } from "antd";
```

### 导入整个模块

```js
import * as lodash from "lodash";
```

tips: `只能在其他语句和函数外使用` `export不能出现在if中`

#### 导入绑定值的怪处 略

### 导入和导出时重命名

```js
function sun() {}
export { sum as add };
import { add } from "./x.js";
add();
import { add as num } from "./x.js";
sum();
```

### 模块的默认值

#### 导出默认值

```js
export default function(num1, num2) {
  return num1 + num2
}
// or
const sum = (num1, num2) => {
  return num1 + num2
}
export default sum
// or
export {sum as default}
```

#### 导入默认值

```js
import sum from "./x.js";
// 混合导入
export let color = "red";
export default function (num1, num2) {
  return num1 + num2;
}
import sum, { color } from "./x.js";
// or
import { default as sum, color } from "./x.js";
```

#### 重新导出一个绑定

```js
export { sum } from "./x.js";
// or 重命名
export { sum as add } from "./x.js";
// or 导出所有值
export * from "./x.js";
```

### 加载模块

```html
<!-- 加载一个js模块文件 -->
<script type="module" src="module.js"></script>
<!-- 内联引入一个模块 -->
<script type="module">
  import {sum } form './x.js'
  sum(1,2)
</script>
```

## es6 其他小改动
### 新的Math方法 Math.log10() 等
### 略
