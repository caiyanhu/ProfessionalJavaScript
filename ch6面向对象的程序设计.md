# ch6 面向对象的程序设计

标签（空格分隔）： JavaScript

---
JS中没有类的概念，因此它的对象其它面向对象语言中的对象有所不同。**可以把ECMAScript 的对象想象成散列表：无非就是一组名值对，其中值可以是数据或函数。**
# 理解对象
## 属性类型
Object.defineProperty()
```javascript:n
var book={
    _year: 2004,//这种下划线是一种常用记号，表示该属性只能通过对象方法来访问
    edition: 1
};
Object.defineProperty(book,"year",{
    get:function(){
        return this._year;
    },
    set:function(newValue){
        if(newValue>2004){
            this._year=newValue;
            this.edition+=newValue-2004;
        }
    }
});
```
## 定义多个属性
Object.defineProperties()
```javacript:n
var book={}
Object.defineProperties(book,{
    _year:{
        value:2004
    },
    edition:{
        value:1
    },
    year:{
        get:function(){
            return this._year;
        },
        set:function(newValue){
            if(newValue>2004){
                this._year=newValue;
                this.edition+=newValue-2004;
            }
        }
    }
});
```
为对象book定义了两个数据属性(_year edition)和一个访问器属性year
## 读取属性
Object.getOwnPropertyDescriptor()
```javascript:n
var descriptor=Object.getOwnPropertyDescriptor(book,"_year");
descriptor.value;//2004
descriptor.configurable;//false
typeof descriptor.get;//undefined

var descriptor=Object.getOwnPropertyDescriptor(book,"year");
descriptor.value;//undefined
descriptor.enumerable;//false
typeof descriptor.get;//function
```

# 创建对象
如果想创建两个person对象
```javascript:n
var person1={
    name:"name1",
    age:23,
    job:"worker",
    sayname:function(){
        alert(this.name);
    }
};
var person2={
    name:"name2",
    age:24,
    job:"teacher",
    sayname:function(){
        alert(this.name);
    }
};
```
会发现定义两个person对象时产生了很多重复的代码。
解决：**工厂模式**
## 工厂模式
```javascript:n
function createPerson(name,age,job){
    var o=new Object();
    o.name=name;
    o.age=age;
    o.job=job;
    o.sayname=function(){
        alert(this.name);
    };
    return o;
}
var person1=createPerson("name1",23,"worker");
var person2=createPerson("name2",24,"teacher");
```
将重复的代码写到工厂函数中，这样不管生成多少个person对象，负责生成的代码只用写一次。
新的问题：**怎么知道一个对象的类型**
## 构造函数模式
```javascript:n
function Person(name,age,job){
    this.name=name;
    this.age=age;
    this.job=job;
    this.sayName=function(){
        alert(this.name);
    };
}
var person1=new Person("name1",23,"worker");
var person2=new Person("name2",24,"teacher");
```
这里是用自定义的构造函数来创建person对象，构造函数中的代码：

- 没有显式的创建对象
- 直接将属性和方法赋给了this
- 没有return语句

因为是通过构造函数来创建的对象，所以就解决了工厂模式中存在的问题--知道了创建出的对象的类型。
person1和person2都是Person的实例，这两个对象都有**constructor**属性，指向自己的构造函数：
```javascript:n
person1.constructor==Person;//true
person1 instanceOf Object;//true
person1 instanceOf Person;//true
```
### 将构造函数当作普通函数
```javascript:n
//构造函数
var person=new Person("Nicholas", 29, "Software Engineer");
person.sayName();//"Nicholas"

//普通函数
Person("Greg", 27, "Doctor"); // 属性和方法均添加到window对象
window.sayName(); //"Greg"

// 在另一个对象的作用域中调用
var o = new Object();
Person.call(o, "Kristen", 25, "Nurse");
o.sayName(); //"Kristen"
```
最后3行代码是在某个特殊对象的作用域内来调用Person()函数，因为是在对象o的作用域内调用的，所以对象o就有了所有属性和方法。
### 构造函数的问题
Person构造函数中有一个方法sayName()，在JS中，方法（或者函数）也是对象，是Function类型的实例。因此每创建一个Person实例，里面的函数都要被重新创建一次。
其实没有必要创建多次有相同功能的Function实例，所以可以把函数定义转移到构造函数的外面来：
```javascript:n
function Person(name,age,job){
    this.name=name;
    this.age=age;
    this.job=job;
    this.sayName=sayName;//sayName属性保存着指向全局函数sayName()的指针
}
function sayName(){
    alert(this.name);
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```
person1和person2共享了在全局作用域内的同一个sayName()函数。
新的问题：如果对象里要定义很多方法，那就需要定义很多个全局函数，于是我们自定义的引用类型就没有封装性可言了。

## 原型模式
每个函数都有一个原型属性(prototype)，该属性是个指针，指向的对象中包含了所有实例共享的那部分内容（即属性和方法）。这个对象叫原型对象，它是所有新创建出来的实例的原型机。因此不必在构造函数中定义对象实例的信息，而是可以把这些信息直接添加到原型对象中。例:
```javascript:n
//构造函数是空的，里面不再有对象实例的任何信息
function Person(){
}
//Person.prototype Person函数的原型属性，指向原型对象
Person.prototype.name="Neo";
Person.prototype.age=29;
Person.prototype.job="Sofware";
Person.prototype.sayName=function(){
    alert(this.name);
};
var person1=new Person();
person1.sayName();//"Neo"
var person2=new Person();
person2.sayName();//"Neo"
```
现在虽然构造函数为空了，但是仍可以通过构造函数来创建新对象，只不过所有新对象都包含相同的属性和方法。**这些属性和方法是所有新对象共享的。**
### 理解原型对象
prototype属性 constructor属性 \_\_proto\_\_属性

 - prototype属性:每个函数都有，指向该函数的原型对象。 
 - constructor属性：每个对象都有，指向该对象的构造函数
 - \_\_proto\_\_属性：每个对象都有，指向原型对象。ECMA-262 第5 版中管这个指针叫**[[Prototype]]**。
 
>原型对象的constructor属性是个指针，指向的是包含prototype属性的那个函数。因此Person.prototype.constructor指向的是Person函数。

>在有了自定义构造函数之后，它的原型对象默认只有constructor属性；原型对象的其它方法都是从Object继承而来。通过这个构造函数，可以继续为原型对象添加别的属性和方法。

>当通过构造函数创建了新的实例后，该实例内部将包含\_\_proto\_\_属性，指向构造函数的原型对象。


![](http://opffh6qw3.bkt.clouddn.com/%E5%8E%9F%E5%9E%8B%E5%AF%B9%E8%B1%A1.png)
上展示了Person 构造函数、Person的原型对象以及Person现有的两个实例之间的关系。在此，Person.prototype指向了原型对象，而Person.prototype.constructor又指回了Person。原型对象中除了包含constructor属性之外，还包括后来添加的其他属性。Person 的每个实例——person1和person2都包含一个内部属性（\_\_proto\_\_属性，或[[Prototype]]），该属性仅仅指向了原型对象Person.prototype；换句话说，**它们与构造函数没有直接的关系。**
注意，person1 person2中都不包含属性和方法，但是却可以调用person1.sayName()，这是通过查找对象属性的过程来实现的。

- isPrototypeOf()方法
当原型对象是某实例的原型时，返回true。从本质上讲，如果该实例的[[Prototype]]指向调用isPrototypeOf()方法的对象（Person.prototype），那么这个方法就返回true。

```javascript:n
Person.prototype.isPrototypeOf(person1);//true
```
- Obj.getPrototypeOf()
返回[[prototype]]的值，或者说\_\_proto\_\_属性的值

```javascript:n
Obj.getPrototypeOf(person1)==Person.prototype;//true
Obj.getPrototypeOf(person1).name;//"Neo"
```

- 如何查找对象的属性(**原型搜索机制**)
每当代码读取某个对象的某个属性时，都会执行一次搜索，目标是具有给定名字的属性。搜索首先从对象实例本身开始。如果在实例中找到了具有给定名字的属性，则返回该属性的值；如果没有找到，则继续搜索该实例的的原型对象，在原型对象中查找具有给定名字的属性。如果在原型对象中找到了这个属性，则返回该属性的值。也就是说，在我们调用person1.sayName()的时候，会先后执行两次搜索。
首先，解析器会问：“实例person1有sayName属性吗？”答：“没有。”然后，它继续搜索，再问：“person1的原型有sayName属性吗？”答：“有。”于是，它就读取那个保存在原型对象中的函数。当我们调用person2.sayName()时，将会重现相同的搜索过程，得到相同的结果。而这正是多个对象实例共享原型所保存的属性和方法的基本原理。

- 实例属性与原型对象属性
实例可以访问原型对象中的属性，但不能修改原型对象中的属性。当实例自定义的属性与原型对象中的属性同名时，根据上面的查找过程，会优先使用实例自己的属性。使用**delete**操作符完全删除实例中的属性后，才能重新访问原型对象中的同名属性。

```javascript:n
function Person(){
}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
    alert(this.name);
};
var person1 = new Person();
var person2 = new Person();

person1.name="Greg";//覆盖了原型对象中的同名属性
alert(person1.name); //"Greg"——来自实例
alert(person2.name); //"Nicholas"——来自原型

delete person1.name;
alert(person1.name); //"Nicholas"——来自原型
```

- hasOwnProperty()
检测一个属性是存在于实例中，还是存在于原型中。这个方法只在给定属性存在于对象实例中时，才会返回true。

### 原型与in操作符

- 单独使用in操作符
使用方式： 属性名 in 对象
只要对象能访问到该属性就返回true，而不管是对象自身的属性还是原型对象的属性。

```javascript:n
function Person(){
}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
    alert(this.name);
};
var person1 = new Person();
var person2 = new Person();

alert(person1.hasOwnProperty("name")); //false
alert("name" in person1); //true

person1.name = "Greg";
alert(person1.name); //"Greg" ——来自实例
alert(person1.hasOwnProperty("name")); //true
alert("name" in person1); //true

alert(person2.name); //"Nicholas" ——来自原型
alert(person2.hasOwnProperty("name")); //false
alert("name" in person2); //true

delete person1.name;
alert(person1.name); //"Nicholas" ——来自原型
alert(person1.hasOwnProperty("name")); //false
alert("name" in person1); //true
```

- 通过for-in循环使用in
返回的是所有能够通过对象访问的、可枚举的（enumerated）属性，其中既包括存在于实例中的属性，也包括存在于原型中的属性。

```javascript:n
var o={
    toString:function(){
        return "My";
    }
};
//找实例o可以访问的可枚举的属性
for(var prop in o){
    if(prop=="toString"){
        alert("Found");
    }
}
```
实例o自定义了方法toString，覆盖了原型对象中toString()（不可枚举）。

- Object.keys()
取得对象上（不会去取该对象的原型对象）所有可枚举的属性，返回数组

```javascript:n
var keys=Object.keys(Person.prototype);
keys;//["name", "age", "job", "sayName"]

var p1 = new Person();
p1.name = "Rob";
p1.age = 31;

var p1keys=Object.keys(p1);
p1keys;//["name", "age"]
```

- Object.getOwnPropertyNames()
得到实例所有的属性，无论它是否可枚举，返回数组

```javascript:n
var keys=Object.getOwnPropertyNames(Person.prototype);
keys;//["constructor", "name", "age", "job", "sayName"]
```

### 更简单的原型语法
之前原型对象的写法是

```javascript:n
function Person(){
}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
    alert(this.name);
};
```
更直观的写法是：

```javascript:n
function Person(){
}
Person.prototype={
    name:"Nicholas",
    age:29,
    job:"Software Engineer",
    sayName:function(){
        alert(this.name);
    }
}
```
少写了很多Person.prototype。这种通过字面量形式创建原型对象的方法有一点与原来不同：**constructor 属性不再指向Person 了。**
前面曾经介绍过，每创建一个函数，就会同时创建它的prototype 对象，这个对象也会自动获得constructor 属性。而我们在
这里使用的语法，本质上完全重写了默认的prototype对象，因此constructor属性也就变成了新对象的constructor属性（指向Object 构造函数），不再指向Person 函数。证据如下：
```javascript:n
var friend=new Person();
friend instanceOf Object;//true
friend instanceOf Person;//true
friend.constructor==Person;//false
friend.constructor==Object;//true
```
为了修正这一问题，让原型对象的constructor重新指向Person，可以这么写：
```javascript:n
function Person(){
}
Person.prototype={
    constructor:Person,
    name:"Nicholas",
    age:29,
    job:"Software Engineer",
    sayName:function(){
        alert(this.name);
    }
}
```
### 原型的动态性
由于在原型中查找值的过程是一次搜索，因此我们对原型对象所做的任何修改都能够立即从实例上反映出来——即使是先创建了实例后修改原型也照样如此。
```javascript:n
var friend=new Person();
Person.prototype.sayHi=function(){
    alert("hi");
};
friend.sayHi();//"hi"
```
这是因为在属性的查找过程中，先找实例friend的属性和方法，没找到就去找friend.\_\_proto\_\_即原生对象中去找。结果就找到了。
但是如果在创建实例后，重写了整个原生对象时，由于实例中\_\_proto\_\_属性是指向原来的原型对象，所以把原型对象修改后，实例只能调用自身的属性和方法，而找不到新的原生对象中的属性和方法。例：
```javascript:n
function Person(){
}
var friend = new Person();
Person.prototype = {
    constructor: Person,
    name : "Nicholas",
    age : 29,
    job : "Software Engineer",
    sayName : function () {
        alert(this.name);
    }
};
friend.sayName(); //Uncaught TypeError: friend.sayName is not a function
```
在这个例子中，我们先创建了Person的一个实例，然后又重写了其原型对象。然后在调用friend.sayName()时发生了错误，因为friend 指向的原型中不包含以该名字命名的属性。
具体过程如下：
![](http://opffh6qw3.bkt.clouddn.com/%E9%87%8D%E5%86%99%E5%8E%9F%E5%9E%8B%E5%AF%B9%E8%B1%A1.png)
重写原型对象切断了现有原型与任何之前已经存在的对象实例之间的联系；它们引用的仍然是最初的原型。

### 原生对象的原型
所有原生引用类型（Object、Array、String，等等）都在其构造函数的原型上定义了方法
```javascript:n
typeof Array.prototype.sort;//function
typeof String.prototype.substring;//function
```
通过原生对象的原型，不仅可以取得所有默认方法的引用，而且也可以定义新方法。可以像修改自定义对象的原型一样修改原生对象的原型，因此可以随时添加方法。下面的代码就给基本包装类型String 添加了一个名为startsWith()的方法。
```javascript:n
String.prototype.startsWith=function(text){
    return this.indexOf(text)==0;
};
var msg="Hello World!";
msg.startsWith("Hello");//true
```

## 组合使用构造函数模式和原型对象模式
通过构造函数模式来定义实例本身的属性和方法，通过原型对象模式来定义所以共享的方法和属性。结果，每个实例都会有自己的一份实例属性的副本，但同时又共享着对方法的引用，最大限度地节省了内存。
```javascript:n
function Person(name,age,job){
    this.name=name;
    this.age=age;
    this.job=job;
    this.friends=["a","b"];
}
Person.prototype={
    constructor: Person,
    sayName: function(){
        alert(this.name);   
    }
};
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");

person1.friends.push("c");

person1.friends;//["a","b","c"]
person2.friends;//["a","b"]

person1.friends==person2.friends;//false
person1.sayName==person2.sayName;//true
```

# 继承
许多面向对象语言都支持两种继承方式：接口继承和实现继承。所谓接口继承只继承方法签名，而实现继承则继承实际的方法。由于JS中函数没有重载，没有签名，所以不能实现接口继承。只能支持实现继承，而实现继承主要是通过**原型链**实现的。

## 原型链
简单回顾一下构造函数、原型和实例的关系：每个构造函数都有一个原型对象（通过函数的prototype属性指向），原型对象都包含一个指向构造函数的指针（原型的constructor属性指向），而实例都包含一个指向原型对象的内部指针（\_\_proto\_\_属性指向）。

>通过原型链实现继承的主要思想是：利用原型，让一个引用类型继承另一个引用类型的属性和方法。
假如让原型对象a等于另一个实例对象o1，因为o1的\_\_proto\_\_属性会指向另一个原型对象b，所以a也就包含了指向b的指针。同样如果b等于另一个实例对象o2，因为o2的\_\_proto\_\_属性会指向另一个原型对象c，所以b也包含了指向c的指针。如此层层递进，就构成了实例与原型的链条。这就是原型链的基本概念。

实现原型链的一种基本模式：
```javascript:n
function SuperType(){
    this.property=true;
}
SuperType.prototype.getSuperValue=function(){
    return this.property;
};

function SubType(){
    this.subProperty=false;
}
//继承了SuperType
SubType.prototype=new SuperType();

SubType.prototype.getSubValue=function(){
    return this.subProperty;
}

var instance=new SubType();
alert(instance.getSuperValue());//true
```
对应的示意图：
![](http://opffh6qw3.bkt.clouddn.com/%E5%8E%9F%E5%9E%8B%E9%93%BE.png)
最终结果就是这样的：instance指向SubType的原型，SubType的原型又指向SuperType的原型。getSuperValue()方法仍然还在
SuperType.prototype 中，但property则位于SubType.prototype中。这是因为property是一个实例属性，而getSuperValue()则是一个原型方法。既然SubType.prototype现在是SuperType的实例，那么property当然就位于该实例中了。**此外，要注意instance.constructor 现在指向的是SuperType，这是因为SubType的原型指向了另一个对象——SuperType的原型，而这个原型对象的constructor 属性指向的是SuperType。**。

- 原型搜索机制
当访问实例的属性或方法时，首先在实例本身找，找不到就往上找实例的原型，还找不到就找原型的原型，直到原型链的末端为止。
- 原型链的末端是什么
末端是Object()构造函数的原型对象。因为所有引用类型都继承自Object，而这个继承也是通过原型链实现的。**所有函数的默认原型都是Object的实例**
![](http://opffh6qw3.bkt.clouddn.com/%E6%9C%89Object%E7%9A%84%E5%8E%9F%E5%9E%8B%E9%93%BE.png)
instance是SubType的实例，其[[prototype]]指向SubType的原型对象，即SuperType的实例。所以该实例又指向SuperType的原型对象。而这个原型对象又是Object的实例，所以它又指向Object的原型对象。这个原型对象就是原型链的末端。
所以instance可以访问Object Prototype中的toString()，可以访问SuperType Prototype中的getSuperValue()，可以访问SubType Prototype中的property，当然还可以访问自身的subProperty。
- 确定原型和实例的关系
两种方法：
instanceof操作符（对，它是操作符，和typeof一样）。只要用这个操作符来测试实例与原型链中出现过的构造函数，结果就会返回true。

```javascript:n
instance instanceof Object;//true
instance instanceof SuperType;//true
instance instanceof SubType;//true
```
isPrototypeOf()方法。只要是原型链中出现过的原型，都可以说是该原型链所派生的实例的原型，因此isPrototypeOf()方法也会返回true。

```javascript:n
Object.prototype.isPrototypeOf(instance);//true
SuperType.prototype.isPrototypeOf(instance);//true
SubType.prototype.isPrototypeOf(instance);//true
```
- 谨慎地定义方法
在通过原型链实现继承时，**不能使用对象字面量创建原型方法**，因为这样会重写原型链。例:

```javascript:n
function SuperType(){
    this.property=true;
}
SuperType.prototype.getSuperValue=function(){
    return this.property;
};
function SubType(){
    this.subproperty=false;
}

//继承SuperType
SubType.prototype=new SuperType();

//使用字面量添加新方法，会导致上一行代码无效
SubType.prototype={
    getSubValue:function(){
        return this.subProperty;
    },
    someOtherMethod:function(){
        return false;
    }
}

var instance=new SubType();
instance.getSuperValue();//Uncaught TypeError: instance.getSuperValue is not a function
```
最后一行代码出错是因为，在第15行时，SubType的原型已经不再是SuperType的实例了，而是新的字面量形式定义的对象。当执行最后一行时，首先在instance自身找getSuperValue()方法，但是instance自身只有subProperty属性，没有方法，所以接着去SubType的原型去找；原型对象里有getSubValue()和someOtherMethod()方法，也没有getSuperValue()，所以结果报错了。

- 原型链的问题

```javascript:n
function SuperType(){
    this.colors = ["red", "blue", "green"];
}
function SubType(){
}
//继承
SubType.prototype=new SuperType();

var instance1=new SubType();
instance1.colors.push("black");
var instance2=new SubType();
instance2.colors;//["red", "blue", "green", "black"]
```
instance1对colors修改，会影响instance2。这是因为colors是被两个实例共享的。原因：SuperType的实例中有colors数组；这个实例又是SubType的原型，那instance1和instance2的\_\_proto\_\_就都指向了这个实例。因为SubType构造函数中没有colors数组，所以instance1和instance2访问的都是SuperType实例中的同一个colors，即这个实例中的所以属性都会被SubType的实例所共享。
这就是在**原型对象中包含引用类型的属性时，所产生的问题，即会被子类实例共享。**

## 借用构造函数
**即在子类型的构造函数中调用父类型的构造函数**。函数只不过是在特定环境中执行代码的对象，因此通过使用apply()和call()方法也可以在（将来）新创建的对象上执行构造函数。

```javascript:n
function SuperType(){
    this.colors=["red", "blue", "green"];
}
function SubType(){
    //在当前执行环境调用 SuperType的构造函数，继承了SuperType
    SuperType.call(this);
}
var instance1=new SubType();
instance1.colors.push("black");
instance1.colors;//["red", "blue", "green", "black"]

var instance2=new SubType();
instance2.colors;//["red", "blue", "green"]
```
在第8行、第12行创建SubType实例时，在实例环境下调用了SuperType的构造函数，这样就会在新的SubType对象上执行SuperType构造函数中定义的对象初始化的代码。这样一来，每个SubType实例都有了自己的colors属性副本。

- 可以在子类型构造函数中向父类型构造函数传递参数

```javascript:n
function Super(name){
    this.name=name;
}
function Sub(){
    //继承Super，同时向父构造函数传参
    Super.call(this,"Neo");
    //子类型实例属性
    this.age=29;
}
var instance=new Sub();
instance.name;//"Neo"
instance.age;//29
```

## 组合继承
**使用原型链实现对原型属性和方法的继承（共享的），而通过借用构造函数实现对实例属性的继承（自身的）。**

```javascript:n
function SuperType(name){
    this.name=name;
    this.colors=["r", "g", "b"];
}
SuperType.prototype.sayName=function(){
    alert(this.name);
};
function SubType(name,age){
    //继承属性
    Super.call(this,name);
    this.age=age;
}
//继承方法
SubType.prototype=new SuperType();
SubType.prototype.constructor=SubType;
SubType.prototype.sayAge=function(){
    alert(this.age);
};

var instance1 = new SubType("Nicholas", 29);
instance1.colors.push("black");
alert(instance1.colors); //"red,blue,green,black"
instance1.sayName(); //"Nicholas";
instance1.sayAge(); //29

var instance2 = new SubType("Greg", 27);
alert(instance2.colors); //"red,blue,green"
instance2.sayName(); //"Greg";
instance2.sayAge(); //27
```
第20行创建SubType实例时,先执行第10行代码，即调用SuperType构造函数，为本实例增加name和colors属性；然后执行第11行代码，为本实例增加age属性。第26行类似。所以instance1和instance2继承了SuperType中的name和colors属性。
当实例调用sayName时，因为实例本身没有这个方法，就通过自己的\_\_proto\_\_属性找到原型对象，即第14行SuperType的实例。这个实例也有name属性(值为null)和colors属性(["r", "g", "b"])，有方法sayAge()。因为没有sayName()方法，所以SuperType的实例通过自己的\_\_proto\_\_属性找到原型对象，即第5行，终于找到了这个函数。
实例调用sayAge()时类似，只不过在SuperType的实例中就找到了。
通过**组合继承**的方法，两个不同的SubType实例既拥有了自己的属性（name colors age），又拥有了相同的方法sayName()和sayAge()。

## 原型式继承
## 寄生式继承
## 寄生组合式继承

# 小结
ECMAScript 支持面向对象（OO）编程，但不使用类或者接口。对象可以在代码执行过程中创建和增强，因此具有动态性而非严格定义的实体。在没有类的情况下，可以采用下列模式创建对象。

- 工厂模式，使用简单的函数创建对象，为对象添加属性和方法，然后返回对象。这个模式后来被构造函数模式所取代。
- 构造函数模式，可以创建自定义引用类型，可以像创建内置对象实例一样使用new操作符。不过，构造函数模式也有缺点，即它的每个成员都无法得到复用，包括函数。由于函数可以不局限于任何对象（即与对象具有松散耦合的特点），因此没有理由不在多个对象间共享函数。
- 原型模式，使用构造函数的prototype属性来指定那些应该共享的属性和方法。组合使用构造函数模式和原型模式时，使用构造函数定义实例属性，而使用原型定义共享的属性和方法。

JavaScript 主要通过原型链实现继承。原型链的构建是通过将一个类型的实例赋值给另一个构造函数的原型实现的。这样，子类型就能够访问超类型的所有属性和方法，这一点与基于类的继承很相似。

原型链的问题是对象实例共享所有继承的属性和方法，因此不适宜单独使用。解决这个问题的技术是借用构造函数，即在子类型构造函数的内部调用超类型构造函数。这样就可以做到每个实例都具有自己的属性，同时还能保证只使用构造函数模式来定义类型。使用最多的继承模式是组合继承，这种模式**使用原型链继承共享的属性和方法，而通过借用构造函数继承实例属性。**