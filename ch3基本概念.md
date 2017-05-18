# ch3 基本概念

标签（空格分隔）： JavaScript

---
# 语法
## 标识符
    - 第一个字符必须是字母、下划线或美元
    - 驼峰大小写格式
## 严格模式
ECMAScript5引入，定义了一种解析和执行模型。此时，ECMAScript3中的一些不确定行为将得到处理，对某些不安全的操作也会抛出错误。
```javascript:n
"use strict";
```
# 关键字与保留字
### 关键字

|关键字|关键字|关键字|关键字|
|-----|------|------|------|
|break| do| instanceof| typeof|
|case| else |new |var
|catch |finally |return| void
|continue |for|switch| while
|debugger[^newAdd] |function |this| with
|default |if |throw
|delete |in |try
### 保留字
|保留字|保留字|保留字|保留字|
|-----|------|------|------|
|abstract |enum |int |short
|boolean |export |interface |static
|byte |extends |long |super
|char |final |native |synchronized
|class |float |package |throws
|const |goto |private |transient
|debugger |implements |protected |volatile
|double |import |public
# 变量
用var定义的变量将成为定义该变量的作用域中的局部变量。
```javascript:n
function test(){
    var message="hi";//局部变量，函数退出后就会被销毁
}
test();
alert(message);//Uncaught ReferenceError: message is not defined
```
~~省略var可以创建全局变量~~
# 数据类型
5种简单的数据类型（基本数据类型）
`注意大小写`
- Undefined
- Null
- Boolean
- Number
- String

1种复杂数据类型
- Object（本质是无序的键值对）
## typeof操作符
用例是检测数据类型的。对变量应用typeof操作符可能返回的结果：
`注意大小写`
- undefined
- boolean
- string
- number
- object
- function
使用
```javascript:n
typeof message;
typeof (message);
typeof 9;
```
`typeof是操作符，不是函数`，上面第二行代码中的括号可以加上，但不是必需的。
```javascript:n
typeof null;//object
```
这是因为null被认为是空的对象引用。
## Undefined类型
该类型就一个值，undefined。如果声明了变量却没初始化，这个变量的值就是undefined。
## Null类型
该类型只有一个值，null，表示空对象指针。所以
```javascript:n
typeof null;//返回object
```
实际上，undefined值派生自null值，因此规范规定两者的相等性测试返回true
```javascript:n
undefined==null;//true(转换了操作数)
undefined===null://false
```
## Boolean类型
两个值，true和false。这两个值与数字值不是一回事，因此true不一定等于1，false也不一定等于0。
所有类型的值都可以调用函数Boolean()转换成Boolean类型的值。转换关系如下：
```table
数据类型 | 什么值能转换成true | 什么值能转换成false
Boolean|true|false
String|非空字符串|""
Number|非零数字，包括无穷大|0和NaN
Object|任何对象|null
Undefined|N/A(不适用)|undefined
```
即
```javascript:n
Boolean(false);//false
Boolean("");//false
Boolean(0);//false
Boolean(Nan);//false
Boolean(null);//false
Boolean(undefined);//false
```
流控制语句如if语句能自动执行Boolean转换。如
```javascript:n
var message="hi";
if(message){
    //这里会被执行
}
```
## Number类型
八进制第一位必须是0，如果字面值超出范围，那么前导0会被忽略，后面的值被当作十进制处理
```javascript:n
var num=079;//无效的八进制，会被解析成79
```
###数值范围
- Number.MIN_VALUE 
- Number.MAX_VALUE
- Number.NEGATIVE_INFINITY 即-Infinity 
- Number.POSITIVE_INFINITY 即Infinity
无穷不是能参与数值计算的数值。
isFinite()函数，返回true和false
### NaN
一个特殊的数值，这个数值用于表示一个本来要返回数值的操作数未返回数值的情况（这样就不会抛出错误了）。例如，在其他编程语言中，任何数值除以0 都会导致错误，从而停止代码执行。~~但在ECMAScript 中，任何数值除以0 会返回NaN，因此不会影响其他代码的执行。~~只有0除以0才会得到NaN，正数除以0得到Infinity，负数除以0得到-Infinity。
两个特点：
1. 任何涉及NaN的操作都返回NaN
2. NaN与任何值都不相等，包括NaN自身
#### isNaN()函数
接受一个参数，该参数可以是任何类型，而函数会帮我们确定这个参数是否“不是数值”。isNaN()在接收到一个值之后，会尝试将这个值转换为数值。某些不是数值的值会直接转换为数值。
```javascript:n
isNaN(NaN);//true
isNaN(10);//false
isNaN("10");//转换成数字10，false
isNaN("abc");//不能被转换成数值，true
isNaN(true);//可以被转成数值1，false
```
isNaN()确实也适用于对象。在基于对象调用isNaN()函数时，会首先调用对象的valueOf()方法，然后确定该方法返回的值是否可以转换为数值。如果不能，则基于这个返回值再调用toString()方法，再测试返回值。

### 数值转换
将非数值转换为数值的三个函数：

 - Number()，参数为任意数据类型
 
 - parseInt()，参数为字数串
 
 - parseFloat()，参数为字符串
 
 
#### Number()的转换规则：
 
 - 参数是Boolean值时，true返回1，false返回0
 - 参数是数字值，只是简单的传入和返回
 - 参数是null值，返回0
 - 参数是对象，则调用对象的valueOf()方法，然后依照前面的规则转换返回的值。如果转换结果是NaN，则调用对象的toString()方法，然后再次依照前面的规则转换返回的字符串值
 - 参数是字符串
    1. 如果字符串中只包含数字（包括前面带正号或负号的情况），则将其转换为十进制数值。（前导0会被忽略）
    2. 如果字符串中包含有效的浮点格式，如"1.1"，则将其转换为对应的浮点数值（同样，也会忽
略前导零）
    3. 如果字符串中包含有效的十六进制格式，例如"0xf"，则将其转换为相同大小的十进制整
数值；
    4. 如果字符串是空的（不包含任何字符），则将其转换为0
    5. 如果字符串中包含除上述格式之外的字符，则将其转换为NaN。
    
例子：
```javascript
Number('Hello World'); //NaN
Number(''); //0
Number('000011'); //11
Number(true); //1
```
#### parseInt()的转换规则
它会忽略字符串前面的空格，直至找到第一个非空格字符。如果第一个字符不是数字字符或者负号，parseInt()
就会返回NaN；也就是说，用parseInt()转换空字符串会返回NaN（Number()对空字符返回0）。如果第一个字符是数字字符，parseInt()会继续解析第二个字符，直到解析完所有后续字符或者遇到了一个非数字字符。
例子：
```javascript
parseInt("123blue') //123
parseInt('') //NaN
parseInt("0xA") //10
parseInt("22.5") //22
parseInt('070') //70
parseInt('070',8) //56
parseInt('70') //70
parseInt('0xf') //15
parseInt("AF", 16); //175
parseInt("AF"); //NaN
```
#### parseFloat()的转换规则
也是从第一个字符（位置0）开始解析每个字符。而且也是一直解析到字符串末尾，或者解析到遇见一个无效的浮点数字字符为止。也就是说，字符串中的第一个小数点是有效的，而第二个小数点就是无效的了，因此它后面的字符串将被忽略。**它始终都会忽略前导的零**。parseFloat()可以识别前面讨论过的所有浮点数值格式，也包括十进制整数格式。但十六进制格式的字符串则始终会被转换成0。**由于parseFloat()只解析十进制值**，因此它没有用第二个参数指定基数的用法。最后还要注意一点：如果字符串包含的是一个可解析为整数的数（没有小数点，或者小数点后都是零），parseFloat()会返回整数。
例子：
```javascript
parseFloat('1234blue') //1234
parseFloat('0xA') //0
parseFloat('22.5') //22.5
parseFloat('22.34.5') //22.34
parseFloat('0908.5') //908.5
parseFloat（'3.125e7) //31250000
```
## String类型
1. 用双引号表示的字符串和用单引号表示的字符串完全相同。

2. 字符串是不可变的，也就是说，字符串一旦创建，它们的值就不能改变。要改变
某个变量保存的字符串，首先要销毁原来的字符串，然后再用另一个包含新值的字符串填充该变量。
例如：
```javascript
var lang='Java';
lang+='Script';
```
实际操作过程如下：首先创建一个能容纳10个字符的新字符串，然后在这个字符串中填充"Java"和"Script"，最后一步是销毁原来的字符串"Java"和字符串"Script"，因为这两个字符串已经没用了。
3. 把一个值转换为字符串有两种方式：toString()方法和String()。
<center>toString()方法</center>

例子：
```javascript
var age = 11;
var ageAsString = age.toString(); // 字符串"11"
var found = true;
var foundAsString = found.toString(); //字符串"true"
```
**null 和undefined 值没有toString()方法**
toString()可以接受参数：输入数值的基数
```javascript
var num = 10;
alert(num.toString()); // "10"
alert(num.toString(2)); // "1010"
alert(num.toString(8)); // "12"
alert(num.toString(10)); // "10"
alert(num.toString(16)); // "a"
```
<center>String()</center>

- 如果值有toString()方法，则调用该方法（没有参数）并返回相应的结果；
- 如果值是null，则返回"null"；
- 如果值是undefined，则返回"undefined"
例子：
```javascript
var value1 = 10;
var value2 = true;
var value3 = null;
var value4;
alert(String(value1)); // "10"
alert(String(value2)); // "true"
alert(String(value3)); // "null"
alert(String(value4)); // "undefined"
```
## Object类型
- 用new创建
例子：
```javascript
var o=new Object();
var o=new Object;//不传递参数的情况下，可以省去括号。不推荐
```
- Object类型是它所有实例的基础。Object的每个实例都具有下列属性和方法
1. constructor：保存着用于创建当前对象的函数。对于前面的例子而言，构造函数（constructor）
就是Object()。
2. hasOwnProperty(propertyName)：用于检查给定的属性在当前对象实例中（而不是在实例
的原型中）是否存在。其中，作为参数的属性名（propertyName）必须以字符串形式指定（例
如：o.hasOwnProperty("name")）
3. isPrototypeOf(object)：用于检查传入的对象是否是传入对象的原型
4. propertyIsEnumerable(propertyName)：用于检查给定的属性是否能够使用for-in语句来枚举。与hasOwnProperty()方法一样，作为参数的属性名必须以字符串形式指定。
5. toLocaleString()：返回对象的字符串表示，该字符串与执行环境的地区对应。
6. toString()：返回对象的字符串表示。
7. valueOf()：返回对象的字符串、数值或布尔值表示。通常与toString()方法的返回值
相同。

# 操作符
## 一元操作符
### 递增和递减操作符
即++和--
```javascript
var num1 = 2;
var num2 = 20;
var num3 = --num1 + num2; // 等于21
var num4 = num1 + num2; // 等于21
```
```javascript
var num1 = 2;
var num2 = 20;
var num3 = num1-- + num2; // 等于22
var num4 = num1 + num2; // 等于21
```
```javascript
var s1 = "2";
var s2 = "z";
var b = false;
var f = 1.1;
var o = {
valueOf: function() {
return -1;
  }
};
s1++; // 值变成数值3(字符串包含有效数字字符，则先将其转为数字值，再加减1)
s2++; // 值变成NaN(字符串中没有有效字符，将变量值设置为NaN,对NaN加减1还是NaN)
b++; // 值变成数值1(先将false转为0，再加减1。同理，true会转成1)
f--; // 值变成0.10000000000000009（由于浮点舍入错误所致）
o--; // 值变成数值-2(先调用对象的valueOf方法以取得可操作的值，再加减1。)
```
### 一元加和减操作符
即+和-
对非数值应用一元加(减)操作符时，该操作符会像Number()转型函数一样对这个值执行转换。
换句话说，布尔值false 和true 将被转换为0 和1，字符串值会被按照一组特殊的规则进行解析，而
对象是先调用它们的valueOf()和（或）toString()方法，再转换得到的值。
```javascript
var s1 = "01";
var s2 = "1.1";
var s3 = "z";
var b = false;
var f = 1.1;
var o = {
valueOf: function() {
return -1;
}
};
s1 = +s1; // 值变成数值1
s2 = +s2; // 值变成数值1.1
s3 = +s3; // 值变成NaN
b = +b; // 值变成数值0
f = +f; // 值未变，仍然是1.1
o = +o; // 值变成数值-1
```
```javascript
var s1 = "01";
var s2 = "1.1";
var s3 = "z";
var b = false;
var f = 1.1;
var o = {
valueOf: function() {
return -1;
}
};
s1 = -s1; // 值变成了数值-1
s2 = -s2; // 值变成了数值-1.1
s3 = -s3; // 值变成了NaN
b = -b; // 值变成了数值0
f = -f; // 变成了-1.1
o = -o; // 值变成了数值1
```
## 位操作符
- NOT
**本质：操作数的负值减1**
```javascript
var num1 = 25; // 二进制00000000000000000000000000011001
var num2 = ~num1; // 二进制11111111111111111111111111100110
alert(num2); // -26
```
- AND
```javascript
var result = 25 & 3;
alert(result); //1
```
25 = 0000 0000 0000 0000 0000 0000 0001 1001
&nbsp;&nbsp;3 = 0000 0000 0000 0000 0000 0000 0000 0011
AND = 0000 0000 0000 0000 0000 0000 0000 0001

- OR
```javascript
var result = 25 | 3;
alert(result); //27
```
25 = 0000 0000 0000 0000 0000 0000 0001 1001
&nbsp;&nbsp;3 = 0000 0000 0000 0000 0000 0000 0000 0011
OR = 0000 0000 0000 0000 0000 0000 0001 1011

- XOR
```javascript
var result = 25 ^ 3;
alert(result); //26
```
25 = 0000 0000 0000 0000 0000 0000 0001 1001
&nbsp;&nbsp;3 = 0000 0000 0000 0000 0000 0000 0000 0011
XOR = 0000 0000 0000 0000 0000 0000 0001 1010

- 左移
**左移不会影响操作数的符号位。换句话说，如果将-2 向左移动5 位，结果将是-64，而非64。**
```javascript
var oldValue = 2; // 等于二进制的10
var newValue = oldValue << 5; // 等于二进制的1000000，十进制的64
```

- 有符号的右移
```javascript
var oldValue = 64; // 等于二进制的1000000
var newValue = oldValue >> 5; // 等于二进制的10 ，即十进制的2
```

- 无符号的右移
```javascript
var oldValue = 64; // 等于二进制的1000000
var newValue = oldValue >>> 5; // 等于二进制的10 ，即十进制的2
//负数以其绝对值的二进制补码形式表示
var oldValue = -64; // 等于二进制的11111111111111111111111111000000
var newValue = oldValue >>> 5; // 等于二进制的00000111111111111111111111111110,十进制的134217726
```
## 布尔操作符
- 逻辑非
```javascript
alert(!false); // true
alert(!"blue"); // false
alert(!0); // true
alert(!NaN); // true
alert(!""); // true
alert(!12345); // false
```
```javascript
alert(!!"blue"); //true
alert(!!0); //false
alert(!!NaN); //false
alert(!!""); //false
alert(!!12345); //true
```
- 逻辑或

如果第一个操作数是对象，则返回第二个操作数；
如果第二个操作数是对象，则只有在第一个操作数的求值结果为true 的情况下才会返回该
对象；
如果两个操作数都是对象，则返回第二个操作数；
如果有一个操作数是null，则返回null；
如果有一个操作数是NaN，则返回NaN；
如果有一个操作数是undefined，则返回undefined。

## 乘性操作符
- 乘法
```javascript
Infinity*0 //NaN
Infinity*NaN //NaN
Infinity*3 //Infinity
Infinity*-3 //-Infinity
Infinity*Infinity //Inifinity
```

## 比较操作符
### 相等 不相等
先转换类型再比较
== !=
规则：

- 如果有一个操作数是布尔值，则在比较相等性之前先将其转换为数值——false 转换为0，而
true 转换为1；
- 如果一个操作数是字符串，另一个操作数是数值，在比较相等性之前先将字符串转换为数值；
- 如果一个操作数是对象，另一个操作数不是，则调用对象的valueOf()方法，用得到的基本类
型值按照前面的规则进行比较；
- **null==undefined**
- 比较相等性之前，不能将null 和undefined 转换成其他任何值。
- 如果有一个操作数是NaN，则相等操作符返回false，而不相等操作符返回true。重要提示：
即使两个操作数都是NaN，相等操作符也返回false；因为按照规则，**NaN 不等于NaN**。
- 如果两个操作数都是对象，则比较它们是不是同一个对象。如果两个操作数都指向同一个对象，
则相等操作符返回true；否则，返回false。
```javascript
null == undefined //true 
true == 1 //true
"NaN" == NaN //false 
true == 2 //false
5 == NaN //false 
undefined == 0 //false
NaN == NaN //false 
null == 0 //false
NaN != NaN //true 
"5"==5 //true
false == 0 //true
```

### 全等 不全等
=== !==
**只在两个操作数未经转换就相等的情况下返回true**
```javascript
var result1 = ("55" == 55); //true，因为转换后相等
var result2 = ("55" === 55); //false，因为不同的数据类型不相等
null===undefiened; //false,因为它们是不同类型的值
```

# 语句
## with语句
语法：
with(expression) statement;
目的主要是为了简化多次编写同一个对象的工作。例如：
```javascript
var qs = location.search.substring(1);
var hostName = location.hostname;
var url = location.href;
```
上面几行代码都包含location 对象。如果使用with 语句，可以把上面的代码改写成如下所示：
```javascript
with(location){
    var qs=search.substring(1);
    var hostName=hostname;
    var url=href;
}
```
使用with 语句关联了location对象。这意味着在with语句的代码块内部，每个变量首先被认为是一个局部变量，而如果在局部环境中找不到该变量的定义，就会查询location对象中是否有同名的属性。如果发现了同名属性，则以location 对象属性的值作为变量的值。

# 函数
## 理解参数
ECMAScript 函数不介意传递进来多少个参数，也不在乎传进来参数是什么数据类型。也就是说，即便你定义的函数只接收两个参数，在调用这个函数时也未必一定要传递两个参数。可以传递一个、三个甚至不传递参数，而解析器永远不会有什么怨言。之所以会这样，原因是**ECMAScript中的参数在内部是用一个数组来表示的。函数接收到的始终都是这个数组，而不关心数组中包含哪些参数**（如果有参数的话）。如果这个数组中不包含任何元素，无所谓；如果包含多个元素，也没有问题。实际上，在函数体内可以通过arguments对象来访问这个参数数组，从而获取传递给函数的每一个参数。
**ECMAScript 中的所有参数传递的都是值，不可能通过引用传递参数。**
**未指定返回值的函数返回的是一个特殊的undefined 值。**
```javascript
function add(){
}
var a=add(); //undefined
```
## 没有重载
ECMAScript 函数不能像传统意义上那样实现重载。而在其他语言（如Java）中，可以为一个函数编写两个定义，只要这两个定义的签名（接受的参数的类型和数量）不同即可。如前所述，ECMAScirpt函数没有签名，因为其参数是由包含零或多个值的数组来表示的。而**没有函数签名，真正的重载是不可能做到的**。
如果在ECMAScript 中定义了两个名字相同的函数，则该名字只属于后定义的函数：
```javascript
function addSomeNumber(num){
    return num + 100;
}
function addSomeNumber(num) {
    return num + 200;
}
var result = addSomeNumber(100); //300
```
**通过检查传入函数中参数的类型和数量并作出不同的反应，可以模仿方法的重载。**
[^newAdd]:ECMAScript5新增