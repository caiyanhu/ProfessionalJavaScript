# ch4 变量 作用域和内存问题

标签（空格分隔）： JavaScript

---

# 基本类型和引用类型的值
变量有两种类型：
- 基本数据类型
    即Undefined Null String Number Boolean
    **是按值访问的，因为可以操作保存在变量中的实际的值。**
- 引用数据类型
引用类型的值是保存在内存中的**对象**。与其他语言不同，**JavaScript 不允许直接访问内存中的位置**，
也就是说不能直接操作对象的内存空间。在操作对象时，实际上是在操作对象的引用而不是实际的对象。
为此，引用类型的值是按引用访问的。
## 动态的属性
对于引用类型的值，我们可以为其添加属性和方法，也可以改变和删除其属性和方法:
```javascript:n
var person = new Object();
person.name = "Nicholas";
alert(person.name); //"Nicholas"
```
但是，我们不能给基本类型的值添加属性，尽管这样做不会导致任何错误:
```javascript:n
var name = "Nicholas";
name.age = 27;
alert(name.age); //undefined
```
**只能给引用类型值动态地添加属性**
## 复制变量值
复制**基本类型**的值，，会在变量对象上创建一个新值，然后把该值复制到为新变量分配的位置上。
```javascript:n
var num1 = 5;
var num2 = num1;
```
**num2中的5 与num1 中的5 是完全独立的**
复制**引用类型**的值时，同样也会将存储在变量对象中的值复制一份放到为新变量分配的空间中。**不同的是，这个值的副本实际上是一个指针**，而这个指针指向**存储在堆中**的一个对象。复制操作结束后，两个变量实际上将引用同一个对象。因此，改变其中一个变量，就会影响另一个变量。
```javascript:n
var obj1 = new Object();
var obj2 = obj1;
obj1.name = "Nicholas";
alert(obj2.name); //"Nicholas"
```
## 传递参数
**ECMAScript 中所有函数的参数都是按值传递的**
向参数传递引用类型的值时，是把**这个值在内存中的地址传给参数**。
参数num与变量count互不认识，只是有相同的值而已。
```javascript:n
function addTen(num){
    num+=10;
    return num;
}
var count=20; //20
var result=addTen(count); //30
```
在函数内部，obj和person引用的是同一个堆内存中的对象。
```javascript:n
function setName(obj){
    obj.name='Nicholas';
}
var person=new Object();
setName(person);
alert(person.name); // 'Nicholas'
```
```javascript:n
function setName(obj){
    obj.name="Neo";
    obj=new Object();
    obj.name='Greg';
}
var person=new Object();
setName(person);
alert(person.name); // 'Neo'
```
因为参数是按值传递的，传递的是person指向的对象在内存中的地址。此时obj指向该对象。第二行，该对象的name属性是Neo。第三行，obj重新指向一个新的对象，但person指向的对象并未变化，所以第八行打印出来的还是Neo。而obj新指向的是一个局部对象，在函数结束后就会被销毁。

# 执行环境和作用域
![作用域链](http://opffh6qw3.bkt.clouddn.com/imgscope-chain.png)
```javascript:n
var color = "blue";
function changeColor(){
    var anotherColor = "red";
    
    function swapColors(){
    var tempColor = anotherColor;
    anotherColor = color;
    color = tempColor;
    // 这里可以访问color、anotherColor 和tempColor
    }
    
    // 这里可以访问color 和anotherColor，但不能访问tempColor
    swapColors();
}
// 这里只能访问color
changeColor();
```
以上代码共涉及3 个执行环境：全局环境、changeColor()的局部环境和swapColors()的局部环境。全局环境中有一个变量color 和一个函数changeColor()。changeColor()的局部环境中有一个名为anotherColor的变量和一个名为swapColors()的函数，但它也可以访问全局环境中的变量color。swapColors()的局部环境中有一个变量tempColor，该变量只能在这个环境中访问到。
无论全局环境还是changeColor()的局部环境都无权访问tempColor。然而，在swapColors()内部则可以访问其他两个环境中的所有变量，因为那两个环境是它的父执行环境。
对于标识符解析（变量名或函数名搜索）是沿着作用域链一级一级地搜索标识符的过程。搜索过程始终从作用域链的前端（即上图的最下面）开始， 然后逐级地向后（全局执行环境）回溯，直到找到标识符为止。
## 块级作用域
**ES6之前是没有块级作用域的。**
在其他类C 的语言中，由花括号封闭的代码块都有自己的作用域（如果用ECMAScript的话来讲，就是它们自己的执行环境），因而支持根据条件来定义变量。例如，下面的代码在JavaScript 中并不会得到想象中的结果：
```javascript:n
if(true){
    var color="red";
}
alert(color); // red
```
如果是在C、C++或Java 中，color会在if语句执行完毕后被销毁。但在JavaScript中，if语句中的变量声明会将变量添加到当前的执行环境（在这里是全局环境）中。尤其是使用for语句的时候：
```javascript:n
for (var i=0; i < 10; i++){
    doSomething(i);
}
alert(i); //10
```
对于有块级作用域的语言来说，for 语句初始化变量的表达式所定义的变量，只会存在于循环的环境之中。而对于JavaScript来说，由for语句创建的变量i 即使在for 循环执行结束后，也依旧会存在于循环外部的执行环境中。
**ES6之后是有块级作用域的**
<center><strong>let</strong></center>
```javascript:n
if(true){
    let cas="das";
}
alert(cas); //cas is not defined
```
```javascript:n
for (let i=0; i < 10; i++){
    doSomething(i);
}
alert(i); //i is not defined
```
### 声明变量
使用var 声明的变量会自动被添加到最接近的环境中。在函数内部，最接近的环境就是函数的局部环境；在with 语句中，最接近的环境是函数环境。**如果初始化变量时没有使用var声明，该变量会自动被添加到全局环境。**
```javascript:n
function add(num1, num2) {
    var sum = num1 + num2;
    return sum;
}
var result = add(10, 20); //30
alert(sum); //sum is not defined
```
函数内定义的sum在函数外是访问不到的，所以第六行有问题。
```javascript:n
function add(num1, num2) {
    sum = num1 + num2;
    return sum;
}
var result = add(10, 20); //30
alert(sum); //30
```
此时因为sum在赋值的时候没有用var声明，所以这个变量是添加在全局环境下的，所以第六行能访问到它。
### 查询标识符
查询过程从作用域链的前端开始，向上逐级查询与给定名字匹配的标识符。如果在局部环境中找到了该标识符，搜索过程停止，变量就绪。如果在局部环境中没有找到该变量名，则继续沿作用域链向上搜索。搜索过程将一直追溯到全局环境的变量对象。如果在全局环境中也没有找到这个标识符，则意味着该变量尚未声明。
```javascript:n
var color = "blue";
function getColor(){
    return color;
}
alert(getColor()); //"blue"
```
首先，搜索getColor()的变量对象，查找其中是否包含一个名为color的标识符。在没有找到的情况下，搜索继续到下一个变量对象（全局环境的变量对象），然后在那里找到了名为color的标识符。因为搜索到了定义这个变量的变量对象，搜索过程宣告结束。
<center>![标识符查询过程](http://opffh6qw3.bkt.clouddn.com/%E6%90%9C%E7%B4%A2%E6%A0%87%E8%AF%86%E7%AC%A6.PNG)</center>
```javascript:n
var color = "blue";
function getColor(){
    var color = "red";
    return color;
}
alert(getColor()); //"red"
```
在getColor()函数中声明了一个名为color的局部变量。调用函数时，该变量就会被声明。而当函数中的第二行代码执行时，意味着必须找到并返回变量color的值。搜索过程首先从局部环境中开始，而且在这里发现了一个名为color 的变量，其值为"red"。因为变量已经找到了，所以搜索即行停止，return语句就使用这个局部变量，并为函数会返回"red"。也就是说，任何位于局部变量color的声明之后的代码，如果不使用window.color都无法访问全局color变量。

# 垃圾收集
JavaScript自动垃圾收集，原理：找出不再继续使用的变量，释放其占用的内存。但如何判断某个变量是没有的呢？两种方法：
 1. 标记清除(mark-and-sweep)
    最常用，当变量进入环境时，标记为"进入环境"（如函数中声明了一个变量），当变量离开环境时，标记为"离开"环境。
    如何标记？任何方式，如翻转某个特殊的位来记录变量何时进入环境，或者使用一个"进入环境"的变量列表和"离开环境"的变量列表来跟踪哪些变量发生了变化。
 2. 引用记数(refernce counting)
    不太常用。记录每个值被引用的次数。当声明了一个变量并将一个引用类型值赋给该变量时，则这个值的引用次数就是1。如果同一个值又被赋给另一个变量，则该值的引用次数加1。相反，如果包含对这个值引用的变量又取得了另外一个值，则这个值的引用次数减1。当这个值的引用次数变成0时，则说明没有办法再访问这个值了，因而就可以将其占用的内存空间回收回来。这样，当垃圾收集器下次再运行时，它就会释放那些引用次数为零的值所占用的内存。
**存在的问题**--循环引用
```javascript:n
function problem(){
    var objA=new Object();
    var objB=new Object();
    objA.someOtherObj=objB;
    objB.someOtherObj=objA;
}
```
对象A 中包含一个指向对象B的指针，而对象B中也包含一个指向对象A的引用,两者通过各自的属性互相引用。所以这两个对象的引用次数都是2。在“引用记数”的策略中，即使两个对象已经离开了作用域，但还是不能被清除。假如这个函数被重复多次调用，就会导致大量内存得不到回收。
3. 性能问题
**JavaScript垃圾收集是周期性的**。如何确定收集的时间间隔呢？
触发垃圾收集的变量分配、字面量和（或）数组元素都有一个临界值，到达临界值的时候触发垃圾收集。如果垃圾收集例程回收的内存分配量低于15%，则变量、字面量和（或）数组元素的临界值就会加倍。如果例程回收了85%的内存分配量，则将各种临界值重置回默认值
4. 管理内存
为了防止运行JavaScript网页耗尽内存而导致系统崩溃，分配给Web浏览器的内存往往要比分配给桌面应用的内存少。内存限制问题不仅会影响给变量分配内存，同时还会影响调用栈以及在一个线程中能够同时执行的语句数量。
确保占用最少的内存可以让页面获得更好的性能。而优化内存占用的最佳方式，就是为执行中的代码只保存必要的数据。一旦数据不再有用，最好通过将其值设置为null来释放其引用——这个做法叫做解除引用（dereferencing）。这一做法适用于大多数全局变量和全局对象的属性。局部变量会在它们离开执行环境时自动被解除引用
```javascript:n
function createPerson(name){
    var localPerson=new Object();
    localPerson.name=name;
    return localPerson;
}
var globalPerson=createPerson('Nicholas');
globalPerson=null; //手动解除globalPerson的引用
```
localPerson是局部变量，无需显式解除引用，但全局变量globalPerson则需要。
解除一个值的引用并不意味着马上自动回收该值所占用的内存。解除引用的真正作用是让值脱离执行环境，以便垃圾收集器**下次**运行时将其回收。
