# ch5 引用类型

标签（空格分隔）： JavaScript

---

# 引用类型 引用类型的值 对象
**引用类型是一种数据结构，用于将数据和功能组织在一起，也常被称为“类”。如Object就是引用类型，还有Array等等。引用类型的值就是对象，是引用类型的一个实例。**

# Object类型
创建Object实例的方式有两种：

- new
```javascript:n
var person=new Object();
person.name="Neo";
person.age=21;
```
- 字面量
```javascript:n
var person={
    name:'Neo',
    age:21
}
```
等价于：
```javascript:n
var person={
    "name":"Neo",
    "age":21
}
```
- 访问对象属性
```javascript:n
person.name; //点表示法
person["name"]; //方括号表示法，必须用字符串的形式
```
方括号表示的优点是可以通过变量访问属性：
```javascript:n
var propertyName="name";
person[propertyName]; //通过变量，这是变量不能再用字符串了
```
如果属性名中包含会导致语法错误的字符，或者属性名使用的是关键字或保留字，也可以使用方括
号表示法。
```javascript:n
person["first name"];
```

# Array类型
JS中的Array两个特点：

- 每一项可保存任何类型的数据
- 动态调整

创建数组的两种方式：

- Array构造函数
```javascript:n
var a=new Array();
var a=new Array(3); //数组有3项
var a = new Array("red", "blue", "green"); //数组有三个字符串
```
也可以省略new
```javascript:n
var a=Array(3);
var a=Array("red", "blue", "green");
```
- 数组字面量
```javascript:n
var colors = ["red", "blue", "green"]; // 创建一个包含3 个字符串的数组
var names = []; // 创建一个空数组
var values = [1,2,]; // 2项数组，内容为1，2
var options = [,,,,,]; // 5项的数组
```
## length属性的特点
它不是只读的
```javascript:n
var colors = ["red", "blue", "green"]; // 创建一个包含3 个字符串的数组
colors.length = 2;
alert(colors[2]); //undefined
```
如果将其length 属性设置为大于数组项数的值，则新增的每一项都会取得undefined 值
```javascript:n
var colors = ["red", "blue", "green"]; // 创建一个包含3 个字符串的数组
colors.length = 4;
aler t(colors[3]); //undefined
```
```javascript:n
var colors = ["red", "blue", "green"]; // 创建一个包含3 个字符串的数组
colors[colors.length] = "black"; //（在位置3）添加一种颜色
colors[colors.length] = "brown"; //（在位置4）再添加一种颜色
```

## 检测数组
Array.isArray(某个Array实例)，返回true或false

## 转换方法

- toLocaleString()
- toString()
- valueOf()

```javascript:n
var colors = ["red", "blue", "green"]; // 创建一个包含3 个字符串的数组
colors.toString(); // 返回字符串 "red,blue,green"
colors.valueOf(); // 返回数组 ["red", "blue", "green"]
alert(colors); // red,blue,green
```
因为alert接收的参数是字符串，所以第4行colors先调用toString()方法转成字符串再传入alert中。
**join()方法**

- 接收分隔符（字符串）（默认是逗号）
- 返回字符串

```javascript:n
var colors = ["red", "green", "blue"];
colors.join(); //"red,green,blue"
colors.join(undefined); //"red,green,blue"
colors.join("||"); //"red||green||blue"

colors[3]=undefined;
colors.join(); //"red,green,blue,"
```

## 栈方法

- push()
- pop()
**作用在栈顶 实现类似栈的行为**
push()方法可以接收任意数量的参数，把它们逐个添加到数组末尾，并返回修改后数组的长度。而pop()方法则从数组末尾移除最后一项，减少数组的length 值，然后返回移除的项。
```javascript:n
var colors = new Array(); // 创建一个数组
var count = colors.push("red", "green"); // 推入两项
alert(count); //2
count = colors.push("black"); // 推入另一项
alert(count); //3
var item = colors.pop(); // 取得最后一项
alert(item); //"black"
alert(colors.length); //2
```
## 队列方法
- shift()
- push()
**队列在列表的末端添加项，从列表的前端移除项**。由于push()是向数组末端添加项的方法，因此要模拟队列只需一个从数组前端取得项的方法。实现这一操作的数组方法就是shift()，它能够移除数组中的第一个项并返回该项，同时将数组长度减1。
```javascript:n
var colors = new Array(); //创建一个数组
var count = colors.push("red", "green"); //推入两项
alert(count); //2
count = colors.push("black"); //推入另一项
alert(count); //3
var item = colors.shift(); //取得第一项
alert(item); //"red"
alert(colors.length); //2
```
- unshift()
- pop()
顾名思义，unshift()与shift()的用途相反：它能在数组前端添加任意个项并返回新数组的长度。因此，同时使用unshift()和pop()方法，可以从相反的方向来模拟队列，即**在数组的前端添加项，从数组末端移除项**。
```javascript:n
var colors = new Array(); //创建一个数组
var count = colors.unshift("red", "green"); //推入两项
alert(count); //2
count = colors.unshift("black"); //推入另一项
alert(count); //3
var item = colors.pop(); //取得最后一项
alert(item); //"green"
alert(colors.length); //2
```
## 重排序方法

- reverse()
- sort()

sort()方法默认从小到大排序。实现方法：对每个数组项调用toString()方法，然后来比较这些字符串。即使数组中的每一项都是数值，sort()方法比较的也是字符串。例：
```javascript:n
var values = [0, 1, 5, 10, 15];
values.sort(); //[0, 1, 10, 15, 5]
```
可见sort()方法的默认排序并不理想，所以它可以接收一个比较函数来确定哪一个值应该放在前面。而比较函数接收两个参数：

- 如果第一个参数应该在第二个参数前面，则返回负数
- 如果两个参数相等，则返回0
- 如果第一个参数应该在第二个参数后面，则返回正数
如：
```javascript:n
function compare(v1,v2){
    if(v1 < v2){
        return -1;
    }
    else if(v1 == v2){
        return 0;
    }
    else{
        return 1;
    }
}
var values = [0, 1, 5, 10, 15];
values.sort(compare); //[0, 1, 5, 10, 15]
```
如果想实现降序，只需调转compare的比较结果即可。当然，如果只想反转数组原来的顺序，使用reverse()方法要更快一些。

**reverse()和sort()方法的返回值是经过排序之后的数组。即不会修改原数组**

## 操作方法

- concat()
- slice()
- splice()

### concat():基于当前数组中的所有项创建一个新数组。
具体实现：
1. 先创建当前数组的副本
2. 将接收到的参数添加到副本的结尾
3. 返回新构建的数组
特例：
1. 如果没有参数，将只是复制当前数组并返回副本
2. 如果传的参数是一个或多个数组，那这些数组全都添加到当前数组的后面
3. 如果传的不是数组，则只是简单的添加到当前数组结尾
```javascript:n
var colors = ["red", "green", "blue"];
var colors2 = colors.concat("yellow", ["black", "brown"]);
colors //["red", "green", "blue"]
colors2 //["red", "green", "blue", "yellow", "black", "brown"]
```
### slice():基于当前数组中的一或多个项创建一个新数组
slice:切片
输入：一到两个参数，起始和结束位置。
如果只有一个参数，则从该位置到数组的末尾；如果两个参数，则从起始位置到结束位置之间的项（但不包括结束位置）
**注意：**

- 不会影响原数组
- 如果结束位置小于起始位置，则返回空数组。
```javascript:n
var colors = ["red", "green", "blue", "yellow", "purple"];
var colors2 = colors.slice(1); //["green", "blue", "yellow", "purple"]
var colors3 = colors.slice(1,4); //["green", "blue", "yellow"]
var colors4 = colors.slice(-2,-1); //["yellow"]
```

### splice():主要用途是向数组的中部插入项

- 删除
输入：两个参数，要删除的第一项的位置和要删除的项数
例：splice(0,2)会删除数组中的前两项。
- 插入
向指定位置插入任意数量的项
输入：三个参数，起始位置；0（要删除的项数）；要插入的项；
如果要插入多个项，可以再传入第四、第五，以至任意多个项。例如，splice(2,0,"red","green")会从当前数组的位置2 开始插入字符串"red"和"green"。
- 替换
可以向指定位置插入任意数量的项，且同时删除任意数量的项
输入：三个参数，起始位置；要删除的项数；要插入的任意数量的项
例：splice (2,1,"red","green")会删除当前数组位置2的项，然后再从位置2开始插入字符串"red"和"green"。
**注意：**
- 会修改原数组
- 如果没有删除任何项，则返回一个空数组
```javascript:n
var colors = ["red", "green", "blue"];
var removed = colors.splice(0,1); //删除第一项
colors; //["green", "blue"]
removed; //["red"]

removed = colors.splice(1, 0, "yellow", "orange"); // 从位置1 开始插入两项
alert(colors); // ["green", "yellow", "orange", "blue"]
alert(removed); // []

removed = colors.splice(1, 1, "red", "purple"); // 插入两项，删除一项
alert(colors); // ["green", "red", "purple", "orange", "blue"]
alert(removed); // ["yellow"]
```

## 位置方法