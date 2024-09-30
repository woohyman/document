### 1. 介绍 js 的基本数据类型。

js 一共有六种基本数据类型，分别是 `Undefined`、`Null`、`Boolean`、`Number`、`String`，还有在 ES6 中新增的 `Symbol` 和 ES10 中新增的 `BigInt` 类型。

`Symbol` 代表创建后独一无二且不可变的数据类型，它的出现我认为主要是为了解决可能出现的全局变量冲突的问题。

`BigInt` 是一种数字类型的数据，它可以表示任意精度格式的整数，使用 `BigInt` 可以安全地存储和操作大整数，即使这个数已经超出了 `Number` 能够表示的安全整数范围。

### 2. JavaScript 有几种类型的值？你能画一下他们的内存图吗？

涉及知识点：

- 栈：原始数据类型（`Undefined`、`Null`、`Boolean`、`Number`、`String`）
- 堆：引用数据类型（对象、数组和函数）

两种类型的区别是：存储位置不同。

原始数据类型直接存储在栈（`stack`）中的简单数据段，占据空间小、大小固定，属于被频繁使用数据，所以放入栈中存储。

引用数据类型存储在堆（`heap`）中的对象，占据空间大、大小不固定。如果存储在栈中，将会影响程序运行的性能；引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体。

回答：

js 可以分为两种类型的值，一种是基本数据类型，一种是复杂数据类型。

基本数据类型....（参考1）

复杂数据类型指的是 `Object` 类型，所有其他的如 `Array`、`Date` 等数据类型都可以理解为 `Object` 类型的子类。

两种类型间的主要区别是它们的存储位置不同，基本数据类型的值直接保存在栈中，而复杂数据类型的值保存在堆中，通过使用在栈中保存对应的指针来获取堆中的值。

![内存图](https://img.kancloud.cn/84/03/840337fc07dcf89621255644fdb339c7_311x390.png)

### 3. 什么是堆？什么是栈？它们之间有什么区别和联系？

堆和栈的概念存在于数据结构中和操作系统内存中。

在数据结构中，栈中数据的存取方式为先进后出。而堆是一个优先队列，是按优先级来进行排序的，优先级可以按照大小来规定。完全二叉树是堆的一种实现方式。

在操作系统中，内存被分为栈区和堆区。

栈区内存由编译器自动分配释放，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈。

堆区内存一般由程序员分配释放，若程序员不释放，程序结束时可能由垃圾回收机制回收。

### 4. 内部属性 [[Class]] 是什么？

所有 `typeof` 返回值为 `"object"` 的对象（如数组）都包含一个内部属性 `[[Class]]`（我们可以把它看作一个内部的分类，而非传统的面向对象意义上的类）。这个属性无法直接访问，一般通过 `Object.prototype.toString(..)` 来查看。例如：

```javascript
Object.prototype.toString.call( [1,2,3] );
// "[object Array]"

Object.prototype.toString.call( /regex-literal/i );
// "[object RegExp]"

// 我们自己创建的类就不会有这份特殊待遇，因为 toString() 找不到 toStringTag 属性时只好返回默认的 Object 标签
// 默认情况类的[[Class]]返回[object Object]
class Class1 {}
Object.prototype.toString.call(new Class1()); // "[object Object]"
// 需要定制[[Class]]
class Class2 {
  get [Symbol.toStringTag]() {
    return "Class2";
  }
}
Object.prototype.toString.call(new Class2()); // "[object Class2]"
```

### 5. 介绍 js 有哪些内置对象？

#### 涉及知识点：

全局的对象（`global objects`）或称标准内置对象，不要和 "全局对象（`global object`）" 混淆。这里说的全局的对象是说在
全局作用域里的对象。全局作用域中的其他对象可以由用户的脚本创建或由宿主程序提供。

标准内置对象的分类

（1）值属性，这些全局属性返回一个简单值，这些值没有自己的属性和方法。

例如 `Infinity`、`NaN`、`undefined`、`null` 字面量

（2）函数属性，全局函数可以直接调用，不需要在调用时指定所属对象，执行结束后会将结果直接返回给调用者。

例如 `eval()`、`parseFloat()`、`parseInt()` 等

（3）基本对象，基本对象是定义或使用其他对象的基础。基本对象包括一般对象、函数对象和错误对象。

例如 `Object`、`Function`、`Boolean`、`Symbol`、`Error` 等

（4）数字和日期对象，用来表示数字、日期和执行数学计算的对象。

例如 `Number`、`Math`、`Date`

（5）字符串，用来表示和操作字符串的对象。

例如 `String`、`RegExp`

（6）可索引的集合对象，这些对象表示按照索引值来排序的数据集合，包括数组和类型数组，以及类数组结构的对象。例如 `Array`

（7）使用键的集合对象，这些集合对象在存储数据时会使用到键，支持按照插入顺序来迭代元素。

例如 `Map`、`Set`、`WeakMap`、`WeakSet`

（8）矢量集合，`SIMD` 矢量集合中的数据会被组织为一个数据序列。

例如 `SIMD` 等

（9）结构化数据，这些对象用来表示和操作结构化的缓冲区数据，或使用 JSON 编码的数据。

例如 `JSON` 等

（10）控制抽象对象

例如 `Promise`、`Generator` 等

（11）反射

例如 `Reflect`、`Proxy`

（12）国际化，为了支持多语言处理而加入 `ECMAScript` 的对象。

例如 `Intl`、`Intl.Collator` 等

（13）`WebAssembly`

（14）其他

例如 `arguments`

回答：

js 中的内置对象主要指的是在程序执行前存在全局作用域里的由 js 定义的一些全局值属性、函数和用来实例化其他对象的构造函
数对象。

一般我们经常用到的如全局变量值 `NaN`、`undefined`，全局函数如 `parseInt()`、`parseFloat()` 用来实例化对象的构造函数如 `Date`、`Object` 等，还有提供数学计算的单体内置对象如 `Math` 对象。

### 6. undefined 与 undeclared 的区别？

已在作用域中声明但还没有赋值的变量，是 `undefined` 的。相反，还没有在作用域中声明过的变量，是 `undeclared` 的。

对于 `undeclared` 变量的引用，浏览器会报引用错误，如 `ReferenceError: b is not defined`。但是我们可以使用 `typeof` 的安全防范机制来避免报错，因为对于 `undeclared`（或者 `not defined` ）变量，`typeof` 会返回 `"undefined"`。

### 7. null 和 undefined 的区别？

首先 `Undefined` 和 `Null` 都是基本数据类型，这两个基本数据类型分别都只有一个值，就是 `undefined` 和 `null`。

`undefined` 代表的含义是未定义，`null` 代表的含义是空对象。一般变量声明了但还没有定义的时候会返回 `undefined`，`null` 主要用于赋值给一些可能会返回对象的变量，作为初始化。

`undefined` 在 js 中不是一个保留字，这意味着我们可以使用 `undefined` 来作为一个变量名，这样的做法是非常危险的，它会影响我们对 `undefined` 值的判断。但是我们可以通过一些方法获得安全的 `undefined` 值，比如说 `void 0`。

当我们对两种类型使用 `typeof` 进行判断的时候，`Null` 类型化会返回 “`object`”，这是一个历史遗留的问题。当我们使用双等号对两种类型的值进行比较时会返回 `true`，使用三个等号时会返回 `false`。

### 8. 如何获取安全的 undefined 值？

因为 `undefined` 是一个标识符，所以可以被当作变量来使用和赋值，但是这样会影响 `undefined` 的正常判断。

表达式 `void ___` 没有返回值，因此返回结果是 `undefined`。`void` 并不改变表达式的结果，只是让表达式不返回值。

按惯例我们用 `void 0` 来获得 `undefined`。

### 10. JavaScript 原型，原型链？ 有什么特点？

在 js 中我们是使用构造函数来新建一个对象的，每一个构造函数的内部都有一个 `prototype` 属性值，这个属性值是一个对象，这个对象包含了可以由该构造函数的所有实例共享的属性和方法。当我们使用构造函数新建一个对象后，在这个对象的内部将包含一个指针，这个指针指向构造函数的 `prototype` 属性对应的值，在 ES5 中这个指针被称为对象的原型。一般来说我们是不应该能够获取到这个值的，但是现在浏览器中都实现了 `__proto__` 属性来让我们访问这个属性，但是我们最好不要使用这个属性，因为它不是规范中规定的。ES5 中新增了一个 `Object.getPrototypeOf()` 方法，我们可以通过这个方法来获取对
象的原型。

当我们访问一个对象的属性时，如果这个对象内部不存在这个属性，那么它就会去它的原型对象里找这个属性，这个原型对象又会有自己的原型，于是就这样一直找下去，也就是原型链的概念。原型链的尽头一般来说都是 `Object.prototype` 所以这就是我们新建的对象为什么能够使用 `toString()` 等方法的原因。

特点：

JavaScript 对象是通过引用来传递的，我们创建的每个新对象实体中并没有一份属于自己的原型副本。当我们修改原型时，与
之相关的对象也会继承这一改变。

### 11. js 获取原型的方法？

- `p.__proto__`
- `p.constructor.prototype`
- `Object.getPrototypeOf(p)`

### 12. 在 js 中不同进制数字的表示方式

- 以 `0X`、`0x` 开头的表示为十六进制。
- 以 `0`、`0O`、`0o` 开头的表示为八进制。
- 以 `0B`、`0b` 开头的表示为二进制格式。

### 13. js 中整数的安全范围是多少？

安全整数指的是，在这个范围内的整数转化为二进制存储的时候不会出现精度丢失，能够被“安全”呈现的最大整数是 2^53 - 1，即 9007199254740991，在 ES6 中被定义为 `Number.MAX_SAFE_INTEGER`。最小整数是 -9007199254740991，在 ES6 中被定义为 `Number.MIN_SAFE_INTEGER`。

如果某次计算的结果得到了一个超过 JavaScript 数值范围的值，那么这个值会被自动转换为特殊的 `Infinity` 值。如果某次计算返回了正或负的 `Infinity` 值，那么该值将无法参与下一次的计算。判断一个数是不是有穷的，可以使用 `isFinite` 函数
来判断。

### 14. typeof NaN 的结果是什么？

`NaN` 意指“不是一个数字”（not a number），NaN 是一个“警戒值”（sentinel value，有特殊用途的常规值），用于指出数字类型中的错误情况，即“执行数学运算没有成功，这是失败后返回的结果”。

```javascript
typeof NaN; // "number"
```

### 15. isNaN 和 Number.isNaN 函数的区别？

函数 `isNaN` 接收参数后，会尝试将这个参数转换为数值，任何不能被转换为数值的的值都会返回 `true`，因此非数字值传入也会返回 `true` ，会影响 `NaN` 的判断。

函数 `Number.isNaN` 会首先判断传入参数是否为数字，如果是数字再继续判断是否为 `NaN` ，这种方法对于 `NaN` 的判断更为准确。

### 16. Array 构造函数只有一个参数值时的表现？

Array 构造函数只带一个数字参数的时候，该参数会被作为数组的预设长度（length），而非只充当数组中的一个元素。这样创建出来的只是一个空数组，只不过它的 length 属性被设置成了指定的值。

构造函数 Array(..) 不要求必须带 new 关键字。不带时，它会被自动补上。

### 17. 其他值到字符串的转换规则？

规范的 9.8 节中定义了抽象操作 ToString ，它负责处理非字符串到字符串的强制类型转换。

（1）Null 和 Undefined 类型 ，null 转换为 "null"，undefined 转换为 "undefined"，

（2）Boolean 类型，true 转换为 "true"，false 转换为 "false"。

（3）Number 类型的值直接转换，不过那些极小和极大的数字会使用指数形式。

（4）Symbol 类型的值直接转换，但是只允许显式强制类型转换，使用隐式强制类型转换会产生错误。

（3）对普通对象来说，除非自行定义 toString() 方法，否则会调用 toString(（Object.prototype.toString()）来返回内部属性 [[Class]] 的值，如"[object Object]"。如果对象有自己的 toString() 方法，字符串化时就会调用该方法并使用其返回值。

### 18. 其他值到数字值的转换规则？

有时我们需要将非数字值当作数字来使用，比如数学运算。为此 ES5 规范在 9.3 节定义了抽象操作 ToNumber。

（1）Undefined 类型的值转换为 NaN。

（2）Null 类型的值转换为 0。

（3）Boolean 类型的值，true 转换为 1，false 转换为 0。

（4）String 类型的值转换如同使用 Number() 函数进行转换，如果包含非数字值则转换为 NaN，空字符串为 0。

（5）Symbol 类型的值不能转换为数字，会报错。

（6）对象（包括数组）会首先被转换为相应的基本类型值，如果返回的是非数字的基本类型值，则再遵循以上规则将其强制转换为数字。

为了将值转换为相应的基本类型值，抽象操作 ToPrimitive 会首先（通过内部操作 DefaultValue）检查该值是否有 valueOf() 方法。如果有并且返回基本类型值，就使用该值进行强制类型转换。如果没有就使用 toString() 的返回值（如果存在）来进行强制类型转换。

如果 valueOf() 和 toString() 均不返回基本类型值，会产生 TypeError 错误。

### 19. 其他值到布尔类型的值的转换规则？

ES5 规范 9.2 节中定义了抽象操作 ToBoolean，列举了布尔强制类型转换所有可能出现的结果。

以下这些是假值：• undefined• null• false• +0、-0 和 NaN• ""

假值的布尔强制类型转换结果为 false。从逻辑上说，假值列表以外的都应该是真值。

### 20. {} 和 [] 的 valueOf 和 toString 的结果是什么？

{} 的 valueOf 结果为 {} ，toString 的结果为 "[object Object]"

[] 的 valueOf 结果为 [] ，toString 的结果为 ""

### 22. ~ 操作符的作用？

~ 返回 2 的补码，并且 ~ 会将数字转换为 32 位整数，因此我们可以使用 ~ 来进行取整操作。

~x 大致等同于 -(x+1)。

### 23. 解析字符串中的数字和将字符串强制类型转换为数字的返回结果都是数字，它们之间的区别是什么？

解析允许字符串（如 parseInt() ）中含有非数字字符，解析按从左到右的顺序，如果遇到非数字字符就停止。而转换（如 Number ()）不允许出现非数字字符，否则会失败并返回 NaN。

### 24. + 操作符什么时候用于字符串的拼接？

据 ES5 规范 11.6.1 节，如果某个操作数是字符串或者能够通过以下步骤转换为字符串的话，+ 将进行拼接操作。如果其中一个操作数是对象（包括数组），则首先对其调用 ToPrimitive 抽象操作，该抽象操作再调用[[DefaultValue]]，以数字作为上下文。如果不能转换为字符串，则会将其转换为数字类型来进行计算。

简单来说就是，如果 + 的其中一个操作数是字符串（或者通过以上步骤最终得到字符串），则执行字符串拼接，否则执行数字加法。

那么对于除了加法的运算符来说，只要其中一方是数字，那么另一方就会被转为数字。

### 25. 什么情况下会发生布尔值的隐式强制类型转换？

（1） if (..) 语句中的条件判断表达式。（2） for ( .. ; .. ; .. ) 语句中的条件判断表达式（第二个）。（3） while (..) 和 do..while(..) 循环中的条件判断表达式。（4） ? : 中的条件判断表达式。（5） 逻辑运算符 ||（逻辑或）和 &&（逻辑与）左边的操作数（作为条件判断表达式）。

### 26. || 和 && 操作符的返回值？

|| 和 && 首先会对第一个操作数执行条件判断，如果其不是布尔值就先进行 ToBoolean 强制类型转换，然后再执行条件判断。

对于 || 来说，如果条件判断结果为 true 就返回第一个操作数的值，如果为 false 就返回第二个操作数的值。

&& 则相反，如果条件判断结果为 true 就返回第二个操作数的值，如果为 false 就返回第一个操作数的值。

|| 和 && 返回它们其中一个操作数的值，而非条件判断的结果

### 27. Symbol 值的强制类型转换？

ES6 允许从符号到字符串的显式强制类型转换，然而隐式强制类型转换会产生错误。

Symbol 值不能够被强制类型转换为数字（显式和隐式都会产生错误），但可以被强制类型转换为布尔值（显式和隐式结果都是 true ）。

### 28. == 操作符的强制类型转换规则？

（1）字符串和数字之间的相等比较，将字符串转换为数字之后再进行比较。

（2）其他类型和布尔类型之间的相等比较，先将布尔值转换为数字后，再应用其他规则进行比较。

（3）null 和 undefined 之间的等比较，结果为真。其他值和它们进行比较都返回假值。

（4）对象和非对象之间的相等比较，对象先调用 ToPrimitive 抽象操作后，再进行比较。

（5）如果一个操作值为 NaN ，则相等比较返回 false（ NaN 本身也不等于 NaN ）。

（6）如果两个操作值都是对象，则比较它们是不是指向同一个对象。如果两个操作数都指向同一个对象，则相等操作符返回 true，否则，返回 false。

### 29. 如何将字符串转化为数字，例如 '12.3b'?

（1）使用 Number() 方法，前提是所包含的字符串不包含不合法字符。

（2）使用 parseInt() 方法，parseInt() 函数可解析一个字符串，并返回一个整数。还可以设置要解析的数字的基数。当基数的值为 0，或没有设置该参数时，parseInt() 会根据 string 来判断数字的基数。

（3）使用 parseFloat() 方法，该函数解析一个字符串参数并返回一个浮点数。

（4）使用 + 操作符的隐式转换。

### 33. javascript 创建对象的几种方式？

**1、new 操作符 + Object 创建对象**

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
var person = new Object();
    person.name = "lisi";
    person.age = 21;
    person.family = ["lida","lier","wangwu"];
    person.say = function(){
        alert(this.name);
    }
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**2、\**字面式创建对象\****

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
var person ={
        name: "lisi",
        age: 21,
        family: ["lida","lier","wangwu"],
        say: function(){
            alert(this.name);
        }
    };
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)](javascript:void(0);)

以上两种方法在使用同一接口创建多个对象时，会产生大量重复代码，为了解决此问题，工厂模式被开发。

**3、\**工厂模式\****

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
function createPerson(name,age,family) {
    var o = new Object();
    o.name = name;
    o.age = age;
    o.family = family;
    o.say = function(){
        alert(this.name);
    }
    return o;
}

var person1 =  createPerson("lisi",21,["lida","lier","wangwu"]);   //instanceof无法判断它是谁的实例，只能判断他是对象，构造函数都可以判断出
var person2 =  createPerson("wangwu",18,["lida","lier","lisi"]);
console.log(person1 instanceof Object);                           //true
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)](javascript:void(0);)

工厂模式解决了重复实例化多个对象的问题，但没有解决对象识别的问题（但是工厂模式却无从识别对象的类型，因为全部都是Object，不像Date、Array等，本例中，得到的都是o对象，对象的类型都是Object，因此出现了构造函数模式）。

**4、\**构造函数模式\****

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
function Person(name,age,family) {
    this.name = name;
    this.age = age;
    this.family = family;
    this.say = function(){
        alert(this.name);
    }
}
var person1 = new Person("lisi",21,["lida","lier","wangwu"]);
var person2 = new Person("lisi",21,["lida","lier","lisi"]);
console.log(person1 instanceof Object); //true
console.log(person1 instanceof Person); //true
console.log(person2 instanceof Object); //true
console.log(person2 instanceof Person); //true
console.log(person1.constructor);      //constructor 属性返回对创建此对象的数组、函数的引用
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**对比工厂模式有以下不同之处：**

1、没有显式地创建对象

2、直接将属性和方法赋给了 this 对象

3、没有 return 语句

**以此方法调用构造函数步骤：**

1、创建一个新对象

2、将构造函数的作用域赋给新对象（将this指向这个新对象）

3、执行构造函数代码（为这个新对象添加属性）

4、返回新对象 ( 指针赋给变量person ？？？ )

可以看出，构造函数知道自己从哪里来（通过 instanceof 可以看出其既是Object的实例，又是Person的实例）

构造函数也有其缺陷，每个实例都包含不同的Function实例（ 构造函数内的方法在做同一件事，但是实例化后却产生了不同的对象，方法是函数 ，函数也是对象）

因此产生了原型模式

**5、\**原型模式\****

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```javascript
function Person() {
}

Person.prototype.name = "lisi";
Person.prototype.age = 21;
Person.prototype.family = ["lida","lier","wangwu"];
Person.prototype.say = function(){
    alert(this.name);
};
console.log(Person.prototype);   //Object{name: 'lisi', age: 21, family: Array[3]}

var person1 = new Person();        //创建一个实例person1
console.log(person1.name);        //lisi

var person2 = new Person();        //创建实例person2
person2.name = "wangwu";
person2.family = ["lida","lier","lisi"];
console.log(person2);            //Person {name: "wangwu", family: Array[3]}
// console.log(person2.prototype.name);         //报错
console.log(person2.age);              //21
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)](javascript:void(0);)

原型模式的好处是所有对象实例共享它的属性和方法（即所谓的共有属性），此外还可以如代码第16,17行那样设置实例自己的属性（方法）（即所谓的私有属性），可以覆盖原型对象上的同名属性（方法）。

**6、\**混合模式（构造函数模式+原型模式）\****

构造函数模式用于定义实例属性，原型模式用于定义方法和共享的属性

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```javascript
function Person(name,age,family){
    this.name = name;
    this.age = age;
    this.family = family;
}

Person.prototype = {
    constructor: Person,  //每个函数都有prototype属性，指向该函数原型对象，原型对象都有constructor属性，这是一个指向prototype属性所在函数的指针
    say: function(){
        alert(this.name);
    }
}

var person1 = new Person("lisi",21,["lida","lier","wangwu"]);
console.log(person1);
var person2 = new Person("wangwu",21,["lida","lier","lisi"]);
console.log(person2);
```

[![复制代码](https://assets.cnblogs.com/images/copycode.gif)](javascript:void(0);)

可以看出，混合模式共享着对相同方法的引用，又保证了每个实例有自己的私有属性。最大限度的节省了内存。

高程中还提到了动态原型模式，寄生构造函数模式，稳妥构造函数模式。

### 34. JavaScript 继承的几种实现方式？

一.原型链继承：
       原理：将父类的实例作为子类的原型

```javascript
  function Father(){
    this.age=10
    this.phone={
      first:"华为",
      second:"小米"
    }
  }
  Father.prototype.getage=function(){
    return this.age
  }
  function Son(name,money){
    this.name=name
    this.money=money
  }
  Son.prototype=new Father() //子类型的原型为父类型的一个实例对象
  Son.prototype.constructor=Son //让子类型的原型的constructor指向子类型
  Son.prototype.getmoney=function(){
    return this.money
  }
  var son=new Son("小米",1000)//
  var son2=new Son()
  console.log(son.age)//10
  console.log(son.getage())//10
  console.log(son.name)//小米
  console.log(son.getmoney())//1000
  console.log(son instanceof Son)//true
  console.log(son instanceof Father)//true
  son.phone.first="魅族"//更改一个子类的引用属性，其他子类也会受影响
  console.log(son2.phone.first)//魅族
```
'
运行运行
           优点：1.通过子类实例可以直接访问父类原型链上和实例上的成员

​              2.  相对简单

​       缺点：1.创建子类实例时，无法向父类构造函数传参

​                  2.父类的所有引用属性会被所有子类共享，更改一个子类的引用属性，其他子类也会受影响

二.构造函数继承：
       原理：在子类构造函数中调用父类构造函数，可以在子类构造函数中使用call()和apply()方  法改变this指向

```javascript
  function Father(name,age){
    this.name=name
    this.age={age:age}
  }
  Father.prototype.getname=function(){
    return this.name
  }
  function Son(name,age,money){
    Father.call(this,name,age)//修改Father的this
    this.money=money
  }
  Son.prototype.getmoney=function(){
    return this.money
  }
  var son=new Son("小明",12,1000)
  var son2=new Son("小李",11,999)
  console.log(son.name)//小明
  console.log(son.getname())//报错 无法继承父类原型上的属性与方法
  console.log(son.money)//1000
  console.log(son.getmoney())//1000
  console.log(son instanceof Father)//false
  console.log(son instanceof Son)//true
  console.log(son.age.age)//12
  console.log(son2.age.age)//11 父类的引用属性不会被共享
    优点：1.可以在子类实例中直接向父类构造函数传参

               2.父类的引用属性不会被子类共享

    缺点：1.无法继承父类原型上的属性与方法
```

三.组合继承：
       原理：组合上述两种方法就是组合继承。用原型链实现对原型属性和方法的继承，用借用构造函数技术来实现实例属性的继承。

```javascript
  function Father(name,age){
    this.name=name
    this.age={age:age}
  }
  Father.prototype.getname=function(){
      return  this.name
  }
  function Son(name,age,money){
    Father.call(this,name,age)//能够看到父类型属性
    this.money=money
  }
  Son.prototype=new Father()//能看到父元素方法
  Son.prototype.constructor=Son//让子类型的原型的constructor指向子类型
  Son.prototype.getmoney=function(){
    return this.money
  }
  var son=new Son("小明",12,1000)
  var son2=new Son("小李",18,1999)
  console.log(son.name)//小明
  console.log(son.getname())//小明
  console.log(son.money)//1000
  console.log(son.getmoney())//1000
  console.log(son instanceof Father)//true
  console.log(son instanceof Son)//true
  console.log(son.age.age)//12
  console.log(son2.age.age)//18 父类构造函数中的引用属性不会被共享
```
'
运行运行
         优点: 1.可以在子类实例中直接向父类构造函数传参
                  2. 通过子类实例可以直接访问父类原型链和实例的成员

​              3.父类构造函数中的引用属性不会被子类共享

​     缺点：调用了两次supertype构造函数，一次在赋值Son的原型时，一次在实例化子类时call                                          调用，这次调用会屏蔽原型中的两个同名属性。

四：原型式继承：
           原理：利用一个空对象作为中介，将某个对象直接赋值给空对象构造函数的原型。

```javascript
  function object(obj){
    function F(){}
    F.prototype=obj//对传入其中的对象执行了一次浅复制，将构造函数F的原型直接指向传入的对象。
    return new F()
  }
  var person={
    name:"小李",
    friends:["小米","小兰"],
    sayname:function(){
      console.log(this.name)
    }
  }
  var person1=object(person)
  person1.name="小王"
   person1.friends.push("小黑")
  console.log(person1.friends)//['小米', '小兰', '小黑']
  person1.sayname()//小王
  var person2=object(person)
  person2.name="小鱼"
  person2.friends.unshift("小葵")
  console.log(person2.friends)// ['小葵', '小米', '小兰', '小黑']
  person2.sayname()//小鱼
  console.log(person.friends)//['小葵', '小米', '小兰', '小黑']
```
'
运行运行
            缺点： 1.子类实例不能向父类传参

​                    2.父类的所有引用属性会被所有子类共享

五：寄生式继承
           原理： 在原型式继承的基础上，增强对象，返回构造函数

```javascript
  function object(obj){
    function F(){}
    F.prototype=obj
    return new F()
  }
  function createAnother(obj){
    var clone=object(obj)
    clone.getname=function(){    //增强对象
      console.log(this.name)
    }
    return clone
  }
  var person={
    name:"小李",
    friends:["小米","小兰"],
  }
  var person1=createAnother(person)
  person1.friends.push("小黑")
  person1.name="小红"
  console.log(person1.friends)// ['小米', '小兰', '小黑']
  person1.getname()//小红
  var person2=createAnother(person)
  console.log(person2.friends)//['小米', '小兰', '小黑']
  person2.getname()//小李
```
'
运行运行
             缺点： 1.子类实例不能向父类传参

​                     2.父类的所有引用属性会被所有子类共享

​                      (同原型式继承)

六：寄生式组合继承：
           原理：结合借用构造函数传递参数和寄生模式实现继承

```javascript
function object(obj){
  function F(){}
  F.prototype=obj
  return new F()
}
function GetPrototype(Father,Son){
  var prototype=object(Father.prototype)  // 创建对象，创建父类原型的一个副本
  prototype.constructor=Son  // 增强对象，弥补因重写原型而失去的默认的constructor 属性
  Son.prototype=prototype  // 指定对象，将新创建的对象赋值给子类的原型
}
function Father(name){
  this.name=name
  this.color=["blue","pink","black"]
}
Father.prototype.getname=function(){
  return this.name
}
function Son(name,age){
  Father.call(this,name)
  this.age=age
}
GetPrototype(Father,Son) 这一句，替代了组合继承中的Son.prototype = new Father() 
Son.prototype.getage=function(){
  return this.age
}
var son1=new Son("小米",18)
 var son2=new Son()
son1.color.push("green")
console.log(son1.getname())  //小米
console.log(son1.name)  //小米
console.log(son1.color)  //['blue', 'pink', 'black', 'green']
console.log(son2.color)  // ['blue', 'pink', 'black']
console.log(son1 instanceof Father)//true
```
'
运行运行
            优点：1. 只调用一次父类构造函数

                   2. 子类可以向父类传参
    
                                      3. 父类方法可以复用
    
                                      4. 父类的引用属性不会被共享

​                   这是最成熟的方法

七:拷贝继承：
       原理：通过遍历复制前一个对象的属性和方法达到拷贝的效果

```javascript
function Father(){
  this.name="小明",
  this.phone={
    first:"小米",
    second:"华为"
  }
}
Father.prototype.getname=function(){
  return this.name
}
function Son(name){
  var father=new Father()
  for(var i in father){
    Son.prototype[i]=father[i]
  }
}
var son=new Son()
var son2=new Son()
son.phone.first="小李"
console.log(son.name)//小明
console.log(son.getname())//小明
console.log(son2.phone.first)//小李
console.log(son instanceof Father)//false
```

运行运行缺点：1.效率极低，内存占用高（因为要拷贝父类的属性）

​                   2.无法获取父类不可枚举的方法（for in不能访问到的）

​                   3.父类的所有引用属性会被所有子类共享

八：ES6 class继承

​     

```javascript
 class Father {
        constructor(name,age){
            this.name=name
            this.age=age
        }
        call(){
            return "打电话"
        }
      }
      //子类继承父类——语法：class 子类 extends 父类
      class Son extends Father{
        constructor(name,age,height){
            //super在子类的构造方法中调用父类的构造方法
            super(name,age)     //this操作必须放在super后面
            this.height=height
        }
        play(){
            console.log("玩游戏")
        }
      }
      var son= new Son("小王",16,180)
      console.log(son.name)//小王
      console.log(son.call())//打电话
      console.log(son.height)//180
         优点：原理还是参照寄生组合继承,基本原理是一样,语法糖,写起来方便,比较完美
```

#### 36. Javascript 的作用域链？

作用域链的作用是保证对执行环境有权访问的所有变量和函数的有序访问，通过作用域链，我们可以访问到外层环境的变量和函数。



作用域链的本质上是一个指向变量对象的指针列表。变量对象是一个包含了执行环境中所有变量和函数的对象。作用域链的前端始终都是当前执行上下文的变量对象。全局执行上下文的变量对象（也就是全局对象）始终是作用域链的最后一个对象。



当我们查找一个变量时，如果当前执行环境中没有找到，我们可以沿着作用域链向后查找。



作用域链的创建过程跟执行上下文的建立有关....

### 37. 谈谈 This 对象的理解。

`this`是 JavaScript 语言的一个关键字。

它是函数运行时，在函数体内部自动生成的一个对象，只能在函数体内部使用。

> ```javascript
> function test() {
> 　this.x = 1;
> }
> ```

上面代码中，函数`test`运行时，内部会自动有一个`this`对象可以使用。

那么，`this`的值是什么呢？

函数的不同使用场合，`this`有不同的值。总的来说，`this`就是函数运行时所在的环境对象。下面分四种情况，详细讨论`this`的用法。

**情况一：纯粹的函数调用**

这是函数的最通常用法，属于全局性调用，因此`this`就代表全局对象。请看下面这段代码，它的运行结果是1。

> ```javascript
> var x = 1;
> function test() {
>    console.log(this.x);
> }
> test();  // 1
> ```

**情况二：作为对象方法的调用**

函数还可以作为某个对象的方法调用，这时`this`就指这个上级对象。

> ```javascript
> function test() {
>   console.log(this.x);
> }
> 
> var obj = {};
> obj.x = 1;
> obj.m = test;
> 
> obj.m(); // 1
> ```

**情况三 作为构造函数调用**

所谓构造函数，就是通过这个函数，可以生成一个新对象。这时，`this`就指这个新对象。

> ```javascript
> function test() {
> 　this.x = 1;
> }
> 
> var obj = new test();
> obj.x // 1
> ```

运行结果为1。为了表明这时this不是全局对象，我们对代码做一些改变：

> ```javascript
> var x = 2;
> function test() {
>   this.x = 1;
> }
> 
> var obj = new test();
> x  // 2
> ```

运行结果为2，表明全局变量`x`的值根本没变。

**情况四 apply 调用**

`apply()`是函数的一个方法，作用是改变函数的调用对象。它的第一个参数就表示改变后的调用这个函数的对象。因此，这时`this`指的就是这第一个参数。

> ```javascript
> var x = 0;
> function test() {
> 　console.log(this.x);
> }
> 
> var obj = {};
> obj.x = 1;
> obj.m = test;
> obj.m.apply() // 0
> ```

`apply()`的参数为空时，默认调用全局对象。因此，这时的运行结果为`0`，证明`this`指的是全局对象。

如果把最后一行代码修改为

> ```javascript
> obj.m.apply(obj); //1
> ```

运行结果就变成了`1`，证明了这时`this`代表的是对象`obj`。

### 40. 写一个通用的事件侦听器函数。

```javascript
const EventUtils = {
  // 视能力分别使用dom0||dom2||IE方式 来绑定事件
  // 添加事件
  addEvent: function(element, type, handler) {
    if (element.addEventListener) {
      element.addEventListener(type, handler, false);
    } else if (element.attachEvent) {
      element.attachEvent("on" + type, handler);
    } else {
      element["on" + type] = handler;
    }
  },

  // 移除事件
  removeEvent: function(element, type, handler) {
    if (element.removeEventListener) {
      element.removeEventListener(type, handler, false);
    } else if (element.detachEvent) {
      element.detachEvent("on" + type, handler);
    } else {
      element["on" + type] = null;
    }
  },

  // 获取事件目标
  getTarget: function(event) {
    return event.target || event.srcElement;
  },

  // 获取 event 对象的引用，取到事件的所有信息，确保随时能使用 event
  getEvent: function(event) {
    return event || window.event;
  },

  // 阻止事件（主要是事件冒泡，因为 IE 不支持事件捕获）
  stopPropagation: function(event) {
    if (event.stopPropagation) {
      event.stopPropagation();
    } else {
      event.cancelBubble = true;
    }
  },

  // 取消事件的默认行为
  preventDefault: function(event) {
    if (event.preventDefault) {
      event.preventDefault();
    } else {
      event.returnValue = false;
    }
  }
};
```

### 43. 事件委托是什么？

事件委托本质上是利用了浏览器事件冒泡的机制。因为事件在冒泡过程中会上传到父节点，并且父节点可以通过事件对象获取到目标节点，因此可以把子节点的监听函数定义在父节点上，由父节点的监听函数统一处理多个子元素的事件，这种方式称为事件代理。



使用事件代理我们可以不必要为每一个子元素都绑定一个监听事件，这样减少了内存上的消耗。并且使用事件代理我们还可以实现事件的动态绑定，比如说新增了一个子节点，我们并不需要单独地为它添加一个监听事件，它所发生的事件会交给父元素中的监听函数来处理。

### 44. ["1", "2", "3"].map(parseInt) 答案是多少？

parseInt() 函数能解析一个字符串，并返回一个整数，需要两个参数 (val, radix)，其中 radix 表示要解析的数字的基数。（该值介于 2 ~ 36 之间，并且字符串中的数字不能大于 radix 才能正确返回数字结果值）。



此处 map 传了 3 个参数 (element, index, array)，默认第三个参数被忽略掉，因此三次传入的参数分别为 "1-0", "2-1", "3-2"



因为字符串的值不能大于基数，因此后面两次调用均失败，返回 NaN ，第一次基数为 0 ，按十进制解析返回 1。

### 45. 什么是闭包，为什么要用它？

一、什么是闭包？

声明一个变量，声明一个函数，在函数内部访问外部的变量，那么这个函数加这个变量叫做闭包。 如下代码：

```
 代码解读复制代码var x='变量'
function f(){
    console.log(x)
}
```

2、闭包的用途是什么？

1.从外部读取内部的变量

```
 代码解读复制代码function f1(){
    var n=666;
    function f2(){
        console.log(n);
    }
    return f2;
}
var resule=f1();
resule()  ///666
```

2.将创建的变量的值始终保持在内存中:

```
 代码解读复制代码function f1(){
    var n=12;
    function f2(){
        console.log(++n);
        
    }
    return f2;
}
var result=f1();
result(); //13
```

上面代码中，函数`f1`中的局部变量`n`一直保存在内存中，并没有在`f1`调用后被自动清除。 3.封装对象的私有属性和私有方法。

```
 代码解读复制代码function f1(n) {
  return function () {
    return n++;
  };
}
var a1 = f1(1);
a1() // 1
a1() // 2
a1() // 3
var a2 = f1(5);
a2() // 5
a2() // 6
a2() // 7
//这段代码中，a1 和 a2 是相互独立的，各自返回自己的私有变量。
```

3、闭包的优缺点是什么？

**优点：** 闭包的优点是可以避免全局变量的污染；

**缺点：** 1.由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。

2.闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（`object`）使用，把闭包当作它的公用方法（`Public Method`），把内部变量当作它的私有属性`（private value）`，这时一定要小心，不要随便改变父函数内部变量的值。

作者：爱学习的颜妙妙
链接：https://juejin.cn/post/6844904162396766215
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。46. 使用 Object.defineProperty() 来进行数据劫持有什么缺点？

有一些对属性的操作，使用这种方法无法拦截，比如说通过下标方式修改数组数据或者给对象新增属性，vue 内部通过重写函数解决了这个问题。在 Vue3.0 中已经不使用这种方式了，而是通过使用 Proxy 对对象进行代理，从而实现数据劫持。使用 Proxy 的好处是它可以完美的监听到任何方式的数据改变，唯一的缺点是兼容性的问题，因为这是 ES6 的语法。

### 47. javascript 代码中的 "use strict"; 是什么意思 ? 使用它区别是什么？

use strict 是一种 ECMAscript5 添加的（严格）运行模式，这种模式使得 Javascript 在更严格的条件下运行。



设立"严格模式"的目的，主要有以下几个：



- 消除 Javascript 语法的一些不合理、不严谨之处，减少一些怪异行为;
- 消除代码运行的一些不安全之处，保证代码运行的安全；
- 提高编译器效率，增加运行速度；
- 为未来新版本的 Javascript 做好铺垫。



**区别：**



- 禁止使用 with 语句。
- 禁止 this 关键字指向全局对象。
- 对象不能有重名的属性。



**回答** use strict 指的是严格运行模式，在这种模式对 js 的使用添加了一些限制。比如说禁止 this 指向全局对象，还有禁止使用 with 语句等。设立严格模式的目的，主要是为了消除代码使用中的一些不安全的使用方式，也是为了消除 js 语法本身的一些不合理的地方，以此来减少一些运行时的怪异的行为。同时使用严格运行模式也能够提高编译的效率，从而提高代码的运行速度。我认为严格模式代表了 js 一种更合理、更安全、更严谨的发展方向。

### 48. 如何判断一个对象是否属于某个类？

第一种方式是使用 instanceof 运算符来判断构造函数的 prototype 属性是否出现在对象的原型链中的任何位置。



第二种方式可以通过对象的 constructor 属性来判断，对象的 constructor 属性指向该对象的构造函数，但是这种方式不是很安全，因为 constructor 属性可以被改写。



第三种方式，如果需要判断的是某个内置的引用类型的话，可以使用 Object.prototype.toString() 方法来打印对象的[[Class]] 属性来进行判断。

### 49. instanceof 的作用？

instanceof运算符用于测试构造函数的prototype属性是否出现在对象的原型链中的任何位置

```javascript
function instanceOf(obj, Constructor) { // obj 表示左边的对象，Constructor表示右边的构造函数
    let rightP = Constructor.prototype // 取构造函数显示原型
    let leftP = obj.__proto__ // 取对象隐式原型
    // 到达原型链顶层还未找到则返回false
    if (leftP === null) {
        return false
    }
    // 对象实例的隐式原型等于构造函数显示原型则返回true
    if (leftP === rightP) {
        return true
    }
    // 查找原型链上一层
    return instanceOf(obj.__proto__, Constructor)
}
```

复制代码

### 50. new 操作符具体干了什么呢？如何实现？

从上面介绍中，我们可以看到`new`关键字主要做了以下的工作：

- 创建一个新的对象`obj`
- 将对象与构建函数通过原型链连接起来
- 将构建函数中的`this`绑定到新建的对象`obj`上
- 根据构建函数返回类型作判断，如果是原始值则被忽略，如果是返回对象，需要正常处理

举个例子：

```js
function Person(name, age){
    this.name = name;
    this.age = age;
}
const person1 = new Person('Tom', 20)
console.log(person1)  // Person {name: "Tom", age: 20}
t.sayName() // 'Tom'
```

流程图如下：

![img](https://static.vue-js.com/b429b990-7a39-11eb-85f6-6fac77c0c9b3.png)

### 51. Javascript 中，有一个函数，执行时对象查找时，永远不会去查找原型，这个函数是？

hasOwnProperty

所有继承了 Object 的对象都会继承到 hasOwnProperty 方法。这个方法可以用来检测一个对象是否含有特定的自身属性，和 in 运算符不同，该方法会忽略掉那些从原型链上继承到的属性。

### 68.什么是 Promise 对象，什么是 Promises/A+ 规范？

Promise 对象是异步编程的一种解决方案，最早由社区提出。Promises/A+ 规范是 JavaScript Promise 的标准，规定了一个 Promise 所必须具有的特性。

Promise 是一个构造函数，接收一个函数作为参数，返回一个 Promise 实例。一个 Promise 实例有三种状态，分别是 pending、resolved 和 rejected，分别代表了进行中、已成功和已失败。实例的状态只能由 pending 转变 resolved 或者 rejected 状态，并且状态一经改变，就凝固了，无法再被改变了。状态的改变是通过 resolve() 和 reject() 函数来实现的，我们可以在异步操作结束后调用这两个函数改变 Promise 实例的状态，它的原型上定义了一个 then 方法，使用这个 then 方法可以为两个状态的改变注册回调函数。这个回调函数属于微任务，会在本轮事件循环的末尾执行。

### 69. ECMAScript6 怎么写 class，为什么会出现 class 这种东西?

在我看来 ES6 新添加的 class 只是为了补充 js 中缺少的一些面向对象语言的特性，但本质上来说它只是一种语法糖，不是一个新的东西，其背后还是原型继承的思想。通过加入 class 可以有利于我们更好的组织代码。

在 class 中添加的方法，其实是添加在类的原型上的。

### 73. .call() 和 .apply() 的区别？

它们的作用一模一样，区别仅在于传入参数的形式的不同。

apply 接受两个参数，第一个参数指定了函数体内 this 对象的指向，第二个参数为一个带下标的集合，这个集合可以为数组，也可以为类数组，apply 方法把这个集合中的元素作为参数传递给被调用的函数。

call 传入的参数数量不固定，跟 apply 相同的是，第一个参数也是代表函数体内的 this 指向，从第二个参数开始往后，每个参数被依次传入函数。

### 74. JavaScript 类数组对象的定义？

常见的类数组转换为数组的方法有这样几种：

（1）通过 call 调用数组的 slice 方法来实现转换

```javascript
Array.prototype.slice.call(arrayLike);
```

（2）通过 call 调用数组的 splice 方法来实现转换

```javascript
Array.prototype.splice.call(arrayLike, 0);
```

（3）通过 apply 调用数组的 concat 方法来实现转换

```javascript
Array.prototype.concat.apply([], arrayLike);
```

（4）通过 Array.from 方法来实现转换

```javascript
Array.from(arrayLike);
```

### 75. 数组和对象有哪些原生方法，列举一下？

数组和字符串的转换方法：toString()、toLocalString()、join() 其中 join() 方法可以指定转换为字符串时的分隔符。

数组尾部操作的方法 pop() 和 push()，push 方法可以传入多个参数。

数组首部操作的方法 shift() 和 unshift() 重排序的方法 reverse() 和 sort()，sort() 方法可以传入一个函数来进行比较，传入前后两个值，如果返回值为正数，则交换两个参数的位置。

数组连接的方法 concat() ，返回的是拼接好的数组，不影响原数组。

数组截取办法 slice()，用于截取数组中的一部分返回，不影响原数组。

数组插入方法 splice()，影响原数组查找特定项的索引的方法，indexOf() 和 lastIndexOf() 迭代方法 every()、some()、filter()、map() 和 forEach() 方法

数组归并方法 reduce() 和 reduceRight() 方法

### 76. 数组的 fill 方法？

fill() 方法用一个固定值填充一个数组中从起始索引到终止索引内的全部元素。不包括终止索引。fill 方法接受三个参数 value，start 以及 end，start 和 end 参数是可选的，其默认值分别为 0 和 this 对象的 length 属性值。

### 78. JavaScript 中的作用域与变量声明提升？

变量提升的表现是，无论我们在函数中何处位置声明的变量，好像都被提升到了函数的首部，我们可以在变量声明前访问到而不会报错。

造成变量声明提升的本质原因是 js 引擎在代码执行前有一个解析的过程，创建了执行上下文，初始化了一些代码执行时需要用到的对象。当我们访问一个变量时，我们会到当前执行上下文中的作用域链中去查找，而作用域链的首端指向的是当前执行上下文的变量对象，这个变量对象是执行上下文的一个属性，它包含了函数的形参、所有的函数和变量声明，这个对象的是在代码解析的时候创建的。这就是会出现变量声明提升的根本原因。

### 79. 如何编写高性能的 Javascript ？

1.使用位运算代替一些简单的四则运算。2.避免使用过深的嵌套循环。3.不要使用未定义的变量。4.当需要多次访问数组长度时，可以用变量保存起来，避免每次都会去进行属性查找。

### 84.什么是 Promise 对象，什么是 Promises/A+ 规范？

Promise 对象是异步编程的一种解决方案，最早由社区提出。Promises/A+ 规范是 JavaScript Promise 的标准，规定了一个 Promise 所必须具有的特性。

Promise 是一个构造函数，接收一个函数作为参数，返回一个 Promise 实例。一个 Promise 实例有三种状态，分别是 pending、resolved 和 rejected，分别代表了进行中、已成功和已失败。实例的状态只能由 pending 转变 resolved 或者 rejected 状态，并且状态一经改变，就凝固了，无法再被改变了。状态的改变是通过 resolve() 和 reject() 函数来实现的，我们可以在异步操作结束后调用这两个函数改变 Promise 实例的状态，它的原型上定义了一个 then 方法，使用这个 then 方法可以为两个状态的改变注册回调函数。这个回调函数属于微任务，会在本轮事件循环的末尾执行。

### 87.观察者模式和发布订阅模式有什么不同？

发布订阅模式其实属于广义上的观察者模式

在观察者模式中，观察者需要直接订阅目标事件。在目标发出内容改变的事件后，直接接收事件并作出响应。

而在发布订阅模式中，发布者和订阅者之间多了一个调度中心。调度中心一方面从发布者接收事件，另一方面向订阅者发布事件，订阅者需要在调度中心中订阅事件。通过调度中心实现了发布者和订阅者关系的解耦。使用发布订阅者模式更利于我们代码的可维护性。

### 91. 介绍一下 js 的节流与防抖？

函数防抖是指在事件被触发 n 秒后再执行回调，如果在这 n 秒内事件又被触发，则重新计时。这可以使用在一些点击请求的事件上，避免因为用户的多次点击向后端发送多次请求。

函数节流是指规定一个单位时间，在这个单位时间内，只能有一次触发事件的回调函数执行，如果在同一个单位时间内某事件被触发多次，只有一次能生效。节流可以使用在 scroll 函数的事件监听上，通过事件节流来降低事件调用的频率。

### 92. Object.is() 与原来的比较操作符 “===”、“==” 的区别？

使用双等号进行相等判断时，如果两边的类型不一致，则会进行强制类型转化后再进行比较。

使用三等号进行相等判断时，如果两边的类型不一致时，不会做强制类型准换，直接返回 false。

使用 Object.is 来进行相等判断时，一般情况下和三等号的判断相同，它处理了一些特殊的情况，比如 -0 和 +0 不再相等，两个 NaN 认定为是相等的。###93.escape,encodeURI,encodeURIComponent 有什么区别

encodeURI 是对整个 URI 进行转义，将 URI 中的非法字符转换为合法字符，所以对于一些在 URI 中有特殊意义的字符不会进行转义。

encodeURIComponent 是对 URI 的组成部分进行转义，所以一些特殊字符也会得到转义。

escape 和 encodeURI 的作用相同，不过它们对于 unicode 编码为 0xff 之外字符的时候会有区别，escape 是直接在字符的 unicode 编码前加上 %u，而 encodeURI 首先会将字符转换为 UTF-8 的格式，再在每个字节前加上 %。

### 95. js 的事件循环是什么？

因为 js 是单线程运行的，在代码执行的时候，通过将不同函数的执行上下文压入执行栈中来保证代码的有序执行。在执行同步代码的时候，如果遇到了异步事件，js 引擎并不会一直等待其返回结果，而是会将这个事件挂起，继续执行执行栈中的其他任务。当异步事件执行完毕后，再将异步事件对应的回调加入到与当前执行栈中不同的另一个任务队列中等待执行。任务队列可以分为宏任务对列和微任务对列，当当前执行栈中的事件执行完毕后，js 引擎首先会判断微任务对列中是否有任务可以执行，如果有就将微任务队首的事件压入栈中执行。当微任务对列中的任务都执行完成后再去判断宏任务对列中的任务。

微任务包括了 promise 的回调、node 中的 process.nextTick 、对 Dom 变化监听的 MutationObserver。

宏任务包括了 script 脚本的执行、setTimeout ，setInterval ，setImmediate 一类的定时事件，还有如 I/O 操作、UI 渲染等。

### 96. js 中的深浅拷贝实现？

浅拷贝指的是将一个对象的属性值复制到另一个对象，如果有的属性的值为引用类型的话，那么会将这个引用的地址复制给对象，因此两个对象会有同一个引用类型的引用。浅拷贝可以使用  Object.assign 和展开运算符来实现。

深拷贝相对浅拷贝而言，如果遇到属性值为引用类型的时候，它新建一个引用类型并将对应的值复制给它，因此对象获得的一个新的引用类型而不是一个原有类型的引用。深拷贝对于一些对象可以使用 JSON 的两个函数来实现，但是由于 JSON 的对象格式比 js 的对象格式更加严格，所以如果属性值里边出现函数或者 Symbol 类型的值时，会转换失败。

### 99. 为什么 0.1 + 0.2 != 0.3？如何解决这个问题？

当计算机计算 0.1+0.2 的时候，实际上计算的是这两个数字在计算机里所存储的二进制，0.1 和 0.2 在转换为二进制表示的时候会出现位数无限循环的情况。js 中是以 64 位双精度格式来存储数字的，只有 53 位的有效数字，超过这个长度的位数会被截取掉这样就造成了精度丢失的问题。这是第一个会造成精度丢失的地方。在对两个以 64 位双精度格式的数据进行计算的时候，首先会进行对阶的处理，对阶指的是将阶码对齐，也就是将小数点的位置对齐后，再进行计算，一般是小阶向大阶对齐，因此小阶的数在对齐的过程中，有效数字会向右移动，移动后超过有效位数的位会被截取掉，这是第二个可能会出现精度丢失的地方。当两个数据阶码对齐后，进行相加运算后，得到的结果可能会超过 53 位有效数字，因此超过的位数也会被截取掉，这是可能发生精度丢失的第三个地方。

对于这样的情况，我们可以将其转换为整数后再进行运算，运算后再转换为对应的小数，以这种方式来解决这个问题。

我们还可以将两个数相加的结果和右边相减，如果相减的结果小于一个极小数，那么我们就可以认定结果是相等的，这个极小数可以使用 es6 的 Number.EPSILON
