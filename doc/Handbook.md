# TypeScript手册

[TypeScript Handbook](http://www.typescriptlang.org/Handbook)

## 目录

* [基本类型](#基本类型)
  * [Boolean](#boolean)
  * [Number](#number)
  * [String](#string)
  * [Array](#array)
  * [Enum](#enum)
  * [Any](#any)
  * [Void](#void)
* [接口](#接口)
  * [第一个接口例子](#第一个接口例子)
  * [可选属性](#可选属性)
  * [函数类型](#函数类型)
  * [数组类型](#数组类型)
  * [类类型](#类类型)
  * [扩展接口](#扩展接口)
  * [混合类型](#混合类型)
* [类](#类)
  * [类](#Classes)
  * [继承](#继承)
  * [公共与私有的修饰语](#公共与私有修饰语)
  * [存取器](#存取器)

## 基本类型

为了写出有用的程序, 我们需要有能力去处理简单的数据单位: 数字, 字符串, 结构, 布尔值等. 在TypeScript里, 包含了与JavaScript中几乎相同的数据类型, 此外还有便于我们操作的枚举类型.

### Boolean

最基本的数据类型就是true/false值, 在JavaScript和TypeScript里叫做布尔值.

```typescript
var isDone: boolean = false;
```

### Number

与JavaScript一样, 所有的数字在TypeScript里都是浮点数. 它们的类型是'number'.

```typescript
var height: number = 6;
```

### String

像其它语言里一样, 我们使用'string'表示文本数据类型. 与JavaScript里相同, 可以使用双引号(")或单引号(')表示字符串.

```typescript
var name: string = "bob";
name = 'smith';
```

### Array

TypeScript像JavaScript一样, 允许你操作数组数据. 可以用两种方式定义数组. 第一种, 可以在元素类型后面接'[]', 表示由此此类元素构成的一个数组:

```typescript
var list:number[] = [1, 2 ,3];
```

第二种方式是使用数组泛型, Array<元素类型>:

```typescript
var list:Array<number> = [1, 2, 3];
```

### Enum

'enum'类型是对标准JavaScript数据类型的一个补充. 像其它语言, 如C#, 使用枚举类型可以为一组数值赋予友好的名字.

```typescript
enum Color {Red, Green, Blue};
var c: Color = Color.Green;
```

默认情况下, 从0开始为元素编号. 你也可以手动的指定成员的数值. 例如, 我们将上面的例子改成从1开始编号:

```typescript
enum Color {Red = 1, Green, Blue};
var c: Color = Color.Green;
```

或者, 全部都采用手动赋值:

```typescript
enum Color {Red = 1, Green = 2, Blue = 4};
var c: Color = Color.Green;
```

枚举类型一个便利的特点是, 你可以从枚举的值取出它的名字. 例如, 我们知道数值2, 但是不确认它映射到Color里的哪个名字, 我们可以查找相应的名字:

```typescript
enum Color {Red = 1, Green, Blue};
var colorName: string = Color[2];

alert(colorName);
```

### Any

有时, 我们可能会为暂时还不清楚的变量指定类型. 这些值可能来自于动态的内容, 比如第三方程序库. 这种情况下, 我们不希望类型检查器对这些值进行检查或者说让它们直接通过编译阶段的检查. 这时, 我们可以使用'any'类型来标记这些变量:

```typescript
var notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
```

在对现有代码进行改写的时候, 'any'类型是十分有用的, 它允许你在编译时可选择地包含或移除类型检查.

当你仅仅知道一部分数据的类型时, 'any'类型也是很有用的. 比如, 你有一个数组, 它包含了不同的数据类型:

```typescript
var list:any[] = [1, true, "free"];

list[1] = 100;
```

### Void

某种程度上来说, 'void'类型与'any'类型是相反的, 表示没有任何类型. 当一个函数不返回任何值是, 你通常会见到其返回值类型是'void':

```typescript
function warnUser(): void {
    alert("This is my warning message");
}
```

## 接口

TypeScript的核心原则之一是对值所具有的'shape'进行类型检查. 它有时被称做鸭子类型或结构性子类型. 在TypeScript里, 接口负责为这样的类型命名, 它可以为你的代码或第三方代码定义契约.

### 第一个接口例子

下面来看一个很简单的例子:

```typescript
function printLabel(labelledObj: {label: string}) {
  console.log(labelledObj.label);
}

var myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

类型检查器查看'printLabel'的调用. 'printLabel'有一个参数, 这个参数应该是一个对象, 它拥有一个名字是'label'并且类型为'string'的属性. 需要注意的是, 我们传入的对象可能有很多个属性, 但是编译器要求至少存在'label'属性, 并且其类型是'string':

```typescript
interface LabelledValue {
  label: string;
}

function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label);
}

var myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

LabelledValue接口可以用来描述上面例子里的契约: 应该具有一个类型是'string', 名字是'label'的属性. 需要注意的是, 在这里我们并不能像在其它语言里一样, 说labelledObj实现了LabelledValue接口. 我们关注的只是值的'shape'. 只要传入的对象满足上面提到的必须条件, 那么就会通过检查.

### 可选属性

接口里的属性并不是全部是必须的. 有些是在某些条件下存在, 而有的则根本不需要. 这在使用'option bags'模式时很有用, 即给函数传入的对象中仅存在一部分属性.

下面是应用了'option bags'的例子:

```typescript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  var newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

var mySquare = createSquare({color: "black"});
```

带有可选属性的接口与普通的接口定义差不多, 只是在可选属性名字定义的后面加一个'?'符号.

可选属性的好处之一是可以对可能存在的属性进行预定义, 好处之二是可以捕获使用了不存在的属性时的错误. 比如, 我们故意错误的拼写传入'createSquare'的属性名collor, 我们会得到一个错误提示.

```typescript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  var newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.collor;  // Type-checker can catch the mistyped name here
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

var mySquare = createSquare({color: "black"});
```

### 函数类型

接口可以描述大部分JavaScript中的对象类型. 除了描述带有属性的普通对象外, 接口也可以表示函数类型.

我们可以给接口定义一个*调用签名*来描述函数类型. 它就像是一个只有参数列表和返回值类型的函数定义.

```typescript
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```

这样定义后, 我们可以像使用其它接口一样使用这个函数接口. 下例展示了如何创建一个函数类型的变量, 并赋予一个同类型的函数值.

```typescript
var mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  var result = source.search(subString);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}
```

对于函数类型的类型检查来说, 函数的参数名不需要与接口里定义的名字相匹配. 比如, 我们也用下面的代码重写上面的例子:

```typescript
var mySearch: SearchFunc;
mySearch = function(src: string, sub: string) {
  var result = src.search(sub);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}
```

函数的参数是参照其位置依次的检查. 函数的返回值类型是通过其返回值推断出来的(此例是false和true). 如果让这个函数返回数字或字符串, 类型检查器会警告我们函数的返回值类型与SearchFunc接口中的定义不匹配.

### 数组类型

与使用接口描述函数类型差不多, 我们也可以描述数组类型. 数组类型具有一个'index'类型表示索引的类型与相应的返回值类型.

```typescript
interface StringArray {
  [index: number]: string;
}

var myArray: StringArray;
myArray = ["Bob", "Fred"];
```

共有两种支持的索引类型: 字符串和数字. 你可以为一个数组同时指定这两种索引类型, 但是有一个限制, 数字索引返回值的类型必须是字符串索引返回值的类型的子类型.

索引签名可以很好的描述数组和'字典'模式, 它们也强制所有属性都与索引返回值类型相匹配. 下面的例子里, length属性不匹配索引返回值类型, 所以类型检查器给出一个错误提示:

```typescript
interface Dictionary {
  [index: string]: string;
  length: number;    // error, the type of 'length' is not a subtype of the indexer
}
```

### 类类型

#### 实现接口

与在C#或Java里接口的基本作用一样, 在TypeScript里它可以明确的强制一个类去符合某种契约.

```typescript
interface ClockInterface {
    currentTime: Date;
}

class Clock implements ClockInterface  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
```

你也可以在接口中描述一个方法, 在类里实现它, 如同下面的'setTime'方法一样:

```typescript
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface  {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```

接口描述了类的公共部分, 而不是公共和私有两部分. 它不会帮你检查类是否具有某些私有成员.

#### 静态成员与类实例的差别

当你操作类和接口的时候, 你要知道类是具有两个类型的: 静态部分的类型和实例的类型. 你会注意到, 当你用构造器签名去定义一个接口并试图定义一个类去实现这个接口时会得到一个错误:

```typescript
interface ClockInterface {
    new (hour: number, minute: number);
}

class Clock implements ClockInterface  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
```

这里因为当一个类实现了一个接口时, 只对其实例部分进行类型检查. 'constructor'存在于类的静态部分, 所以不在检查的范围内.

取而代之, 我们应该直接操作类的静态部分. 看下面的例子:

```typescript
interface ClockStatic {
    new (hour: number, minute: number);
}

class Clock  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}

var cs: ClockStatic = Clock;
var newClock = new cs(7, 30);
```

### 扩展接口

和类一样, 接口也可以相互扩展. 扩展接口时会将其它接口里的属性拷贝到这个接口里, 因此允许你把接口拆分成单独的可重用的组件.

```typescript
interface Shape {
    color: string;
}

interface Square extends Shape {
    sideLength: number;
}

var square = <Square>{};
square.color = "blue";
square.sideLength = 10;
```

一个接口可以继承多个接口, 创建出多个接口的组合接口.

```typescript
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

var square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

### 混合类型

先前我们提过, 接口可以描述在JavaScript里存在的丰富的类型. 因为JavaScript其动态灵活的特点, 有时你会希望一个对象可以同时具有上面提到的多种类型.

一个例子就是, 一个对象可以同时做为函数和对象使用, 并带有额外的属性.

```typescript
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

var c: Counter;
c(10);
c.reset();
c.interval = 5.0;
```

使用第三方库的时候, 你可能会上面那样去详细的定义类型.

## 类

传统的JavaScript使用函数和基于原型的继承来创建可重用的组件, 但是这对于熟悉使用面向对象方式的程序员来说有些棘手, 因为他们使用的是类继承并且对象是从这些类构建出来的. 从ECMAScript6开始, JavaScript的下个版本, JavaScript程序将可以使用这种基于类的面向对象方法. 在TypeScript里, 我们允许开发者现在就使用这些特性, 并且编译后的JavaScript可以在所有主流浏览器和平台上运行, 而不需要等到下个JavaScript版本.

### Classes

下面看一个类的例子:

```typescript
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

var greeter = new Greeter("world");
```

如果你使用过C#或Java, 你会对这种语法非常熟悉. 我们声明一个类'Greeter'. 这个类有3个成员, 叫做'greeting'的属性, 一个构造函数和一个'greet'方法.

你会注意到, 我们在引用任何一个类成员的时候都用了'this'. 这表示我们访问的是类的成员.

最后一行, 我们使用'new'构造了Greeter类的一个实例. 它会调用之前定义的构造器, 创建一个新的Greeter类型的对象, 用执行构造函数初始化它.

### 继承

在TypeScript里, 我们可以使用常用的面向对象模式. 当然, 基于类的程序设计中最基本的模式是可以使用继承来扩展一个类.

看下面的例子:

```typescript
class Animal {
    name:string;
    constructor(theName: string) { this.name = theName; }
    move(meters: number = 0) {
        alert(this.name + " moved " + meters + "m.");
    }
}

class Snake extends Animal {
    constructor(name: string) { super(name); }
    move(meters = 5) {
        alert("Slithering...");
        super.move(meters);
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(meters = 45) {
        alert("Galloping...");
        super.move(meters);
    }
}

var sam = new Snake("Sammy the Python");
var tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);
```

这个例子展示了TypeScript中继承的一些特征, 它与其它语言中类似. 我们使用'extends'来创建子类. 你可以看到'Horse'和'Snake'类是基类'Animal'的子类, 并且可以访问其属性和方法.

这个例子也说明了, 在子类里可以重写父类的方法. 'Snake'类和'Horse'类都创建了'move'方法, 重写了从'Animal'继承来的'move'方法, 使得'move'方法根据不同的类具有不同的功能.

### 公共与私有的修饰语

#### 默认为public

你会注意到, 上面的例子中我们没有用'public'去修饰任何类成员的可见性. 在C#里需要使用'public'明确地指定成员的可见性. 在TypeScript里, 所有成员都默认为public.

你也可以把成员标记为'private'来控制什么是可以在类外部访问的. 我们重写一下上面的'Animal'类:

```typescript
class Animal {
    private name:string;
    constructor(theName: string) { this.name = theName; }
    move(meters: number) {
        alert(this.name + " moved " + meters + "m.");
    }
}
```

#### 理解private

TypeScript是一个结构化的类型系统. 我们比较两种不同的类型时, 不在乎它们从哪儿来的, 如果它们每个成员的类型是兼容的, 我们就说这两个类型是兼容的.

当我们比较具有'private'成员的类型的时候, 稍有不同. 如果一个类型中有个私有成员, 那么只有另外一个类型中也存在一个私有成员并且它们来自同一处定义, 我们才认为这两个类型是兼容的.

下面来看一个例子, 详细的解释了这点:

```typescript
class Animal {
    private name:string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal {
  constructor() { super("Rhino"); }
}

class Employee {
    private name:string;
    constructor(theName: string) { this.name = theName; } 
}

var animal = new Animal("Goat");
var rhino = new Rhino();
var employee = new Employee("Bob");

animal = rhino;
animal = employee; //error: Animal and Employee are not compatible
```

这个例子中有'Animal'和'Rhino'两个类, 'Rhino'是'Animal'类的子类. 还有一个'Employee'类, 其类型看上去与'Animal'是相同的. 我们创建了几个这些类的实例, 并相互赋值来看看会发生什么. 因为'Animal'和'Rhino'共享了来自'Animal'里的私有定义'private name: string', 因此它们是兼容的. 然而'Employee'却不是这样. 当把'Employee'赋值给'Animal'的时候, 得到一个错误, 说它们的类型不兼容. 尽管'Employee'里也有一个私有成员'name', 但它明显不是'Animal'里面定义的那个.

#### 参数属性

'public'和'private'关键字也提供一种简便的方法通过参数属性来创建和初始化类成员. 参数属性让你一步就可以创建并初始化一个类成员. 下面的代码改写了上面的例子. 注意, 我们删除了'name'并直接使用'private name: string'参数在构造函数里创建并初始化'name'成员.

```typescript
class Animal {
    constructor(private name: string) { }
    move(meters: number) {
        alert(this.name + " moved " + meters + "m.");
    }
}
```

### 存取器

TypeScript支持getters/setters来截取对对象成员的访问. 它能帮助你有效的控制对对象成员的访问.

下面来看如何把一类改写成使用'get'和'set'. 首先是没用使用存取器的例子:

```typescript
class Employee {
    fullName: string;
}

var employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
```

我们可以随意的设置fullName是非常方便的, 但是这可能会带来麻烦.

下面这个版本里, 我们先检查用户是否具有有效的密码, 然后再允许修改employee. 我们把对fullName的直接访问改成了可以检查密码的'set'方法. 我们也加了一个get方法, 让上面的例子仍然可以工作.

```typescript
var passcode = "secret passcode";

class Employee {
    private _fullName: string;

    get fullName(): string {
        return this._fullName;
    }
  
    set fullName(newName: string) {
        if (passcode && passcode == "secret passcode") {
            this._fullName = newName;
        }
        else {
            alert("Error: Unauthorized update of employee!");
        }
    }
}

var employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
```

可以修改一下密码, 来验证一下存取器是否是工作的. 当密码不一致时, 会提示我们没有权限去修改employee.

注意: 使用存取器, 要求设置编译器输出为ECMAScript 5.