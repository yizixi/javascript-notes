### JS中的call()方法和apply()方法用法总结

1. JS中，每个函数都包含两个非继承而来的方法：call()方法和apply()方法。

2. 相同点：这两个方法的作用是一样的。都是在特定的作用域中调用函数，以此设置函数体内this对象的值，以扩充函数赖以运行的`作用域`。

3. 一般来说，`this`总是指向调用某个方法的`对象`，但是使用`call()`和`apply()`方法时，就会改变this的`指向`。

4. 总结一句话：call（） 就是用来让括号里的对象 来集成括号外的函数的属性！可以称之为继承！

5. [简单大白话的 call 讲解](https://blog.csdn.net/xllily_11/article/details/51480723)

> Demo1 for call.

```js
window.color = 'red';
document.color = 'yellow';

var s1 = {color: 'blue' };
function changeColor(){
    console.log(this.color);
}

//Tip: call()可以改变 changeColor 函数 this 的指向，也就是改变了函数的作用域。
changeColor.call();   //red (默认传递参数)
changeColor.call(window);   //red
changeColor.call(document); //yellow
changeColor.call(this);     //red
changeColor.call(s1);       //blue
```

> Demo2 for call.

```js
var Pet = {
    words : '...',
    speak : function (say) {
        console.log(say + ''+ this.words)
    }
}
Pet.speak('Speak'); // 结果：Speak...

var Dog = {
    words:'Wang'
}

//将this的指向改变成了Dog
Pet.speak.call(Dog, 'Speak'); //结果： SpeakWang
```

> Demo1 for apply.

```js
window.number = 'one';
document.number = 'two';

var s1 = {number: 'three' };
function changeColor(){
    console.log(this.number);
}

changeColor.apply();         //one (默认传参)
changeColor.apply(window);   //one
changeColor.apply(document); //two
changeColor.apply(this);     //one
changeColor.apply(s1);       //three
```

> Demo2 for apply.

```js
function Pet(words){
    this.words = words;
    this.speak = function () {
        console.log( this.words)
    }
}

function Dog(words){
    //Pet.call(this, words); //结果： Wang
    Pet.apply(this, arguments); //结果： Wang
}

var dog = new Dog('Wang');
dog.speak();
```


#### 不同点：接收参数的方式不同。
- apply()方法 接收两个参数，一个是函数运行的作用域（this），另一个是参数数组。
语法：`apply([thisObj [,argArray] ]);`，调用一个对象的一个方法，2另一个对象替换当前对象。

- 说明：如果argArray不是一个有效数组或不是arguments对象，那么将导致一个 
TypeError，如果没有提供argArray和thisObj任何一个参数，那么Global对象将用作thisObj。

- call()方法, 第一个参数和 apply() 方法的一样，但是传递给函数的参数必须列举出来。
语法：`call([thisObject[,arg1 [,arg2 [,...,argn]]]]);`，应用某一对象的一个方法，用另一个对象替换当前对象。

- 说明：call 方法可以用来代替另一个对象调用一个方法，call方法可以将一个函数的对象上下文从初始的上下文改变为 thisObj 指定的新对象，如果没有提供 thisObj 参数，那么 Global 对象被用于 thisObj。


```js
function add(c,d){
    return this.a + this.b + c + d;
}

var s = {a:1, b:2};
console.log(add.call(s,3,4)); // 1+2+3+4 = 10
console.log(add.apply(s,[5,6])); // 1+2+5+6 = 14 
```

```js
window.firstName = "Cynthia"; 
window.lastName = "_xie";

var myObject = {firstName:'my', lastName:'Object'};

function getName(){
    console.log(this.firstName + this.lastName);
}

function getMessage(sex,age){
    console.log(this.firstName + this.lastName + " 性别: " + sex + " age: " + age );
}

getName.call(window); // Cynthia_xie
getName.call(myObject); // myObject

getName.apply(window); // Cynthia_xie
getName.apply(myObject);// myObject

getMessage.call(window,"女",21); //Cynthia_xie 性别: 女 age: 21
getMessage.apply(window,["女",21]); // Cynthia_xie 性别: 女 age: 21

getMessage.call(myObject,"未知",22); //myObject 性别: 未知 age: 22
getMessage.apply(myObject,["未知",22]); // myObject 性别: 未知 age: 22
```