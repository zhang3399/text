# TypeScript 

## 一、基础类型(8种)

### 概述 (2025-2-19--测试回滚)
JavaScript 语言（注意，不是 TypeScript）将值分成8种类型。

- boolean 布尔
- string 字符串 
- number 数量
- bigint 长整型数字
- symbol 象征
- object 对象
- undefined 未定义的
- null 零值  

TypeScript 继承了 JavaScript 的类型设计，以上8种类型可以看作 TypeScript 的基本类型。
注意，上面所有类型的名称都是小写字母，首字母大写的**Number、String、Boolean**等在 JavaScript 语言中都是**内置对象**，而不是类型名称。
1. 布尔值

```typescript
let isDone: boolean = false;
```
2. 字符串

（普通字符串和模板字符串都属于string类型）
```typescript
let str1: string = "bob";
let str2: string = `${str1} world`;
```

3. number类型

```typescript
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
let binaryLiteral: number = 0b1010;
let octalLiteral: number = 0o744;
```

4. bigint类型

bigint 类型是 ES2020 标准引入的。JavaScript 版本不能低于 ES2020
bigint 类型包含所有的大整数。
```typescript
const x:bigint = 123n;
const y:bigint = 0xffffn;
```
bigint 与 number 类型不兼容。
```typescript
const x:bigint = 123; // 报错
const y:bigint = 3.14; // 报错
```

5. symbol类型

symbol 类型包含所有的 Symbol 值。

```typescript
const x:symbol = Symbol();
```

6. object 类型

根据 JavaScript 的设计，object 类型包含了所有对象、数组和函数。

```typescript
const x:object = { foo: 123 };
const y:object = [1, 2, 3];
const z:object = (n:number) => n + 1;
```
上面示例中，对象、数组、函数都属于 object 类型。

关于数组，TypeScript 有专门类型定义。
```typescript
//第一种写法
let list: number[] = [1, 2, 3];
list.push(4);
list.length = 2;
list[1] = 5; 
```
采用`number[]`定义了数组成员是动态的,可以增加、减少、修改成员，但成员类型必须为number类型。

```typescript
//第二种写法
let arr:Array<number> = [1, 2, 3];
```

7. undefined 
8. null 

```typescript
let u: undefined = undefined;
let n: null = null;
``` 
注意，如果没有声明类型的变量，被赋值为undefined或null，在关闭编译设置noImplicitAny和strictNullChecks时，它们的类型会被推断为any。

```typescript
// 关闭 noImplicitAny 和 strictNullChecks

let a = undefined;   // any
const b = undefined; // any

let c = null;        // any
const d = null;      // any
``` 

如果希望避免这种情况，则需要打开编译选项strictNullChecks。
```typescript
// 打开编译设置 strictNullChecks

let a = undefined;   // undefined
const b = undefined; // undefined

let c = null;        // null
const d = null;      // null
``` 

 元组

```typescript
let x: [string, number];
x = ["hello", 10]; // OK
x = [10, "hello"]; // Error
```

 any

```typescript
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
```

## 类型推论
变量初始化类型推断，如果没有明确声明类型，TypeScript 会根据变量初始值推断类型。
```typescript
//变量
let someValue = 4;// 初始化时，根据赋值自动推断为 number类型
someValue = "maybe a string instead";//error

//数组
let list = [1, 2, 3];// 初始化时，根据赋值自动推断为 number[]类型
list.push("hello");//error
```
数组变量未声明类型，且为空数组，TypeScript 会推断为 `any[]`类型。
```typescript
let list = [];//推断为 any[]

list.push(1);//赋值后，推断为number[]类型

list.push("hello");//再次赋值，推断为 (number|string)[]类型
```

### 只读数组，const断言
Javascript 中，const 定义的变量，不能重新赋值，但可以修改其成员。
```typescript
const arr = [1, 2, 3];//number[]
arr[0] = 4;//ok
arr[0] = "hello world!";//error,
arr.push(5);//error
```
TsypeScript 允许声明只读数组，方法是在数组类型前加上redayonly关键字。
```typescript
const arr: readonly number[] = [1, 2, 3];//readonly number[]
arr[0] = 4;//error
arr.push(5);//error
delete arr[0];//error
```
TsypeScript 将`readonly number[]`视为`number[]`的父类，子类型具有父类行的特征并具有自己的特征`pop\push`,所以子类可以赋值给父类型，反过来不行。
```typescript
let arr:readonly number[] = [1, 2, 3];
let arr2:readonly number[] = arr;//ok
arr = arr2;//error

//--------------------------

function getSum(s:number[]) {
  // ...
}
const arr:readonly number[] = [1, 2, 3];
getSum(arr) // error,等价于 getSum(s:number[]=arr:readonly number[])
```
`number[]`作为子类继承`readonly number[]`父类，而父类没有`pop()`、`push()`方法,所以两者并不完全等价。
```typescript
```
TypeScript 中，const 定义的变量，不能重新赋值，也不能修改其成员。
```typescript
const arr: readonly number[] = [1, 2, 3];
arr[0] = 4;//error
```
### 只读数组，泛型写法

```typescript
// error 
const arr:readonly Array<number> = [0, 1];

// method 1
const a1:ReadonlyArray<number> = [0, 1];
// method 2
const a2:Readonly<number[]> = [0, 1];
// method 3 使用“const 断言”实现
const arr1 = [0, 1] as const;
arr1[0] = [2]; // 报错 
```

## 类型断言

对于没有类型声明的值，TypeScript 会进行类型推断，很多时候得到的结果，未必是开发者想要的，类型断言可以用来告诉编译器变量的类型，可以避免编译器进行类型推断。
```typescript
type T = 'a'|'b'|'c';

let foo = 'a';
// let bar:T = foo; // error bar为T类型，foo 推断为string类型
let bar:T = foo as T; // ok 断言foo为T类型
```
### 断言语法
 
```typescript
    // 语法一：`<类型>值`
    <Type>value
```

```typescript
    // 语法二：`值 as 类型`
    value as Type // 推荐写法
```

对象类型有严格字面量检查，如果存在额外的属性会报错.
```typescript
const p:{ x: number } = { x: 0, y: 0 };// error
```
断言可以绕过字面量检查，存在类型断言，就没有严格字面量检查了，所以不报错。
```typescript
const p0:{ x: number } =
  { x: 0, y: 0 } as { x: number };// ok断言使得等号左右两边类型一致

const p1:{ x: number } =
  { x: 0, y: 0 } as { x: number; y: number };// ok 断言使得等号左右两边类型一致
```
## 值类型
TypeScript 规定，单个值也是一种类型，称为“值类型”。
```typescript
let x:'hello';

x = 'hello'; // 正确
x = 'world'; // 报错
```
TypeScript 推断类型时，遇到`const`命令声明的变量，如果代码里面没有注明类型，就会推断该变量是值类型。
```typescript
// x 的类型是 "https"
const x = 'https';

// y 的类型是 string
const y:string = 'https';
```
这样推断是合理的，因为`const`命令声明的变量，一旦声明就不能改变，相当于常量。值类型就意味着不能赋为其他值。

注意，`const`命令声明的变量，如果赋值为对象，并不会推断为值类型。
```typescript
// x 的类型是 { foo: number }
const x = { foo: 1 };
```
变量`x`没有被推断为值类型，而是推断属性`foo`的类型是`number`。这是因为` JavaScript `里面，`const`变量赋值为对象时，属性值是可以改变的。

```typescript
const x:5 = 4 + 1; // 报错
```
上面示例中，等号左侧的类型是数值5，等号右侧4 + 1的类型，TypeScript 推测为number。由于5是number的子类型，number是5的父类型，父类型不能赋值给子类型，所以报错了.
但是，反过来是可以的，子类型可以赋值给父类型。
```typescript
let x:5 = 5;
let y:number = 4 + 1;

x = y; // 报错
y = x; // 正确
```
如果一定要让子类型可以赋值为父类型的值，就要用到类型断言
```typescript
const x:5 = (4 + 1) as 5; // 正确
```
上面示例中，在4 + 1后面加上as 5，就是告诉编译器，可以把4 + 1的类型视为值类型5，这样就不会报错了。       

只包含单个值的值类型，用处不大。实际开发中，往往将多个值结合，作为联合类型使用。

## 联合类型

联合类型（union types）指的是多个类型组成的一个新类型，使用符号|表示。

联合类型`A|B`表示，任何一个类型只要属于`A`或`B`，就属于联合类型`A|B`。
```typescript
// 基本类型的联合类型
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven'; // OK
myFavoriteNumber = 7; // OK

// 值类型的联合类型
let set : true| false;
let name : 'Tom' | 'Jerry' | 'Spike';
let color: 'red' | 'blue' | 'green';
```
打开编译选项strictNullChecks后，其他类型的变量不能赋值为undefined或null。这时，如果某个变量确实可能包含空值，就可以采用联合类型的写法。
```typescript
let name:string|null;

name = 'John';
name = null;
// 多行书写联合类型
let x:
  | 'one'
  | 'two'
  | 'three'
  | 'four';
```

如果一个变量有多种类型，读取该变量时，往往需要进行“类型缩小”（type narrowing），区分该值到底属于哪一种类型，然后再进一步处理。

```typescript
function printId(
  id:number|string
) {
    console.log(id.toUpperCase()); // 报错
}

// 类型缩小
function printId(
  id:number|string
) {
  if (typeof id === 'string') {
    console.log(id.toUpperCase());
  } else {
    console.log(id);
  }
}
```
## 交叉类型
交叉类型（intersection types）指的多个类型组成的一个新类型，使用符号&表示。

交叉类型A&B表示，任何一个类型必须同时属于A和B，才属于交叉类型A&B，即交叉类型同时满足A和B的特征。

```typescript
let x:number&string;
```
上面示例中，变量x同时是数值和字符串，这当然是不可能的，所以 TypeScript 会认为x的类型实际是never。

交叉类型的主要用途是表示对象的合成。
```typescript
let obj:
  { foo: string } &
  { bar: string };

obj = {
  foo: 'hello',
  bar: 'world'
};
```
上面示例中，变量obj同时具有属性foo和属性bar。

交叉类型常常用来为对象类型添加新属性。
```typescript
type A = { foo: number };

type B = A & { bar: number };
```
上面示例中，类型B是一个交叉类型，用来在A的基础上增加了属性bar。

## type 命令
type命令用来定义一个类型的别名。
```typescript
type Age = number;

let age:Age = 55;
```
上面示例中，type命令为number类型定义了一个别名Age。这样就能像使用number一样，使用Age作为类型。

别名的作用域是块级作用域。这意味着，代码块内部定义的别名，影响不到外部。
```typescript
type Color = 'red';

if (Math.random() < 0.5) {
  type Color = 'blue';
}
```
别名支持使用表达式，也可以在定义一个别名时，使用另一个别名，即别名允许嵌套。
```typescript
type World = "world";
type Greeting = `hello ${World}`;
```
## typeof 运算符
JavaScript 语言中，typeof 运算符是一个一元运算符，返回一个字符串，代表操作数的类型。
```typescript
typeof 'foo'; // 'string'
```
JavaScript 里面，typeof运算符只可能返回八种结果，而且都是字符串。

```typescript
typeof undefined; // "undefined"
typeof true; // "boolean"
typeof 1337; // "number"
typeof "foo"; // "string"
typeof {}; // "object"
typeof parseInt; // "function"
typeof Symbol(); // "symbol"
typeof 127n // "bigint"
```

由于编译时不会进行 JavaScript 的值运算，所以TypeScript 规定，typeof 的参数只能是标识符，不能是需要运算的表达式。

```typescript
type T = typeof Date(); // 报错
```
上面示例会报错，原因是 typeof 的参数不能是一个值的运算式，而Date()需要运算才知道结果。

另外，typeof命令的参数不能是类型。
```typescript
type T = typeof Date(); // 报错
```

## 块圾类型声明
TypeScript 支持块级类型声明，即类型可以声明在代码块（用大括号表示）里面，并且只在当前代码块有效。
```typescript
if (true) {
  type T = number;
  let v:T = 5;
} else {
  type T = string;
  let v:T = 'hello';
}
```

## 类型兼容
TypeScript 的类型存在兼容关系，某些类型可以兼容其他类型。
```typescript
type T = number|string;

let a:number = 1;
let b:T = a;
```

TypeScript 的一个规则是，凡是可以使用父类型的地方，都可以使用子类型，但是反过来不行。
```typescript
let a:'hi' = 'hi';
let b:string = 'hello';

b = a; // 正确
a = b; // 报错
```
上面示例中，hi是string的子类型，string是hi的父类型。所以，变量a可以赋值给变量b，但是反过来就会报错。
