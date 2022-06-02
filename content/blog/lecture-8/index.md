---
title: ООП в прототипном стиле
date: "2021-12-10"
description: "Рассмотрение написания ООП в прототипном стиле. Синтаксис классов в ES6."
---

## Прототип объекта

В JavaScript объекты можно организовать в цепочки, связанные через специальную ссылку
\_\_proto\_\_. При чтении свойства объекта, если оно отсутствует у него, то будет искаться в его
объекте \_\_proto\_\_, то есть в его прототипе

Прототип используется только при чтении свойства, запись и удаление совершаются над самим
объектом

Свойство \_\_proto__ не
поддерживается в браузерах IE10-

```javascript
let user = { isActive: false };
let admin = { name: "John Doe" };

admin.__proto__ = user; // admin прототипно наследует user

console.log(admin); // { name: "John" }
console.log(admin.isActive); //false

admin.isActive = true;

console.log(admin.isActive); // true
console.log(user.isActive); // false
```

## Object.create(null)

При обычном, литеральном способе создания объект "нагружается" встроенными функциями из
глобального объекта Object, доступными по ссылке \_\_proto__

Иногда необходимо использовать объекты как коллекции произвольных пар ключ-значение, без
методов и свойств его прототипа

Метод Object.create(proto[, descriptors]) позволяет создать объект с конкретным
прототипом. Объектом \_\_proto__ при этом является первый аргумент метода

Способ Object.create(null) позволяет создать объект без прототипа, что означает, он не будет
наследоваться от базового объекта Object и не будет содержать его методов

```javascript
let user = {};
console.log(user.toString); // function toString()

let user = Object.create(null);
console.log(user.toString); // undefined
```

Чтобы дать прототип объекту, его нужно передать в метод create

```javascript
let user = { name: "Anonymous" };
let admin = Object.create(user);

console.log(admin.name); //"Anonymous"
```

## Метод hasOwnProperty

При переборе свойств объекта в цикле for..in нет разницы между собственными свойствами
объекта и свойствами его прототипа

Вызов obj.hasOwnProperty(key) возвращает true, если свойство key принадлежит объекту
obj, в противном случае – false

```javascript
let user = { isActive: false };

let admin = Object.create(user);
admin.name = "John Doe";
/*
let admin = {
  name: "John Doe",
  __proto__: user
}
 */

for (let key in admin) {
  console.log("property", key);
  
  if (admin.hasOwnProperty(key)) {
    console.log("own property", key);
  }
}
```

## Встроенные классы

Встроенные классы (конструкторы) Array, String, Date и др. используют прототипы для
обеспечения наследования базового функционала. При этом все они наследуют от
Object.prototype

[ ] → Array.prototype → Object.prototype

Примитивы не являются объектами, но наследуют от соответствующих классов методы из
прототипа: String.prototype , Number.prototype , Boolean.prototype ... Свойство
constructor указывает на класс-родитель

```javascript
let str = "xa";

alert(str.constructor === String); // true

str = str.repeat(3);
alert(str); // "хахаха"
```

Примитивные типы null и undefined не имеют соответствующего им конструктора,
следовательно, никакие методы к ним не применимы

```javascript
let x = null;
alert(x.constructor); // Uncaught TypeError: Cannot read property "constructor" of null
```

## Расширение глобальных объектов

Свойство prototype глобальных объектов (классов) позволяет добавлять новые методы к ним

```javascript
String.prototype.sprintf = function () {
  let string = this.valueOf(), key;
  
  for (key in arguments) {
    string = string.replace("%s", arguments[key]);
  }
  
  return string;
}

alert("%s, greetings!".sprintf("John Doe")); // "John Doe, greetings!"
alert("You have earned $%s, %s!".sprintf(100, "John Doe")); // You have earned $100, John Doe!
```

Характеристики
- Метод прототипа автоматически доступен всем экземплярам класса
- Изменение встроенных прототипов глобально влияет на все скрипты
- Возможен конфликт методов/свойств, определенных в других библиотеках

## Проверка принадлежности классу

Оператор instanceof позволяет проверить, принадлежит ли объект определенному классу, с
учетом прототипного наследования

```javascript
[] instanceof Array // true
[] instanceof Object // true
[] instanceof String // false
```

## Прототипное ООП

В объектно-ориентированных программах объекты создаются посредством вызова функции-
конструктора через оператор new

Чтобы указать прототип объектам класса, нужно задать его в свойстве prototype конструктора

Прототипом может быть только объект

```javascript
let figure = {
  color: "black"
};

function Circle(x, y, r) {
  this.x = x;
  this.y = y;
  this.r = r;
}

Circle.prototype = figure;

let sun = new Circle(0, 0, 10); // Circle { x: 0, y: 0, r: 10 } 
// => __proto__ { color: "black" }

console.log(sun.color); // "black"
```

### Инкапсуляция в прототипном ООП

Понятия приватных и защищенных свойств в прототипном подходе отсутствуют. Все методы и
свойства фактически являются публичными, поскольку записываются в this или prototype

Если необходимо обозначить свойства как private, их названия маркируют двойным
подчеркиванием; protected свойства маркируются одним подчеркиванием

```javascript
function Figure(x, y) {
  this.__x = x || 0;
  this.__y = y || 0;
}
```

### Наследование в прототипном ООП

**Наследование** класса реализуется через свойство prototype. Экземпляр класса использует
свойства и методы из своего прототипа, а если их нет, тогда свойства и методы родителя

sun → Circle.prototype → Figure.prototype

Прототипный подход, в отличие от функционального, позволяет не дублировать методы в каждом
объекте и опционально использовать конструктор родителя

В prototype по умолчанию находится свойство constructor , указывающее на функцию-конструктор

Circle.prototype.constructor === Circle

Если переопределяем свой prototype, но хотим сохранить свой конструктор, ссылку на него
необходимо явно указать

```javascript
function Figure(x, y) {
  this.__x = x || 0;
  this.__y = y || 0;
}

Figure.prototype.draw = function() {
  console.log("Draw the figure");
}

function Circle(x, y, r) {
  Figure.call(this, x, y); // Выполняем конструктор "у себя"
}

Circle.prototype = Object.create(Figure.prototype); // Наследуем методы из прототипа родителя
Circle.prototype.constructor = Circle; // Возвращаем себе свой конструктор

let sun = new Circle(0, 0, 10);
console.log(sun);
```

### Полиморфизм в прототипном ООП

В классе-наследнике можно переопределять или расширять методы родительского класса,
реализуя идею **полиморфизма**

```javascript
...

Circle.prototype.draw = function() {
  Figure.prototype.draw.call(this); // Выполняем метод из прототипа родителя в своем контексте
  console.log("circle"); // Расширяем функционал родительского метода
}

let sun = new Circle(0, 0, 10);
sun.draw();
```

### Классы

Синтаксис для классов выглядит так:

```javascript
class Название [extends Родитель] {
  constructor
  методы
}
```

Функция конструктор запускается при создании new "Название Класса", остальные методы
записываются в "Название Класса".prototype.

```javascript
class User {
  constructor(name) {
    this.name = name;
  }
  
  sayHello() {
    alert("Hi " + this.name);
  }
}

let user = new User("John");
user.sayHello();
```

```javascript
function User(name) {
  this.name = name;
}

User.prototype.sayHello = function() {
  alert(this.name);
}
```

В обоих случаях new User будет создавать объекты. Метод sayHello также в обоих случаях находится в прототипе.

Но при объявлении через class есть ряд отличий:

- User нельзя вызывать без new, будет ошибка
- Объявление класса с точки зрения области видимости ведёт себя как let. В частности, оно видно только в текущем блоке и только в коде,
который находится ниже объявления (Function Declaration видно и до объявления).

Методы, объявленные внутри class, также имеют ряд особенностей:

- Метод sayHello является именно методом, значит имеет доступ к super.
- Все методы класса работают в строгом режиме use strict, даже если он не указан
- Все методы класса не перечислимы. То есть в цикле for...in по объекту их не будет


### Наследование и super

Синтаксис:

```javascript
class Child extends Parent {
  ....
}
```

```javascript
class Person {}
class User extends Person {}

alert( User.prototype.__proto__ == Person.prototype ); // true
```

Как видим, в new User доступны как свои методы, так и (через super) методы родителя.

```javascript
class Person {
  constructor(name) {
    this.name = name;
  }
  
  say() {
    alert("Hi " + this.name);
  }
}

class User extends Person {
  say() {
    super.say();
    alert("...and Jane!");
  }
}

new User("John").say();
// Hi John
// ... and Jane!
```

Как видно из примера выше, методы родителя (say) можно переопределить в наследнике. При этом для обращения к родительскому методу используют super.say().

Конструктор constructor родителя наследуется автоматически. То есть, если в потомке не указан свой constructor, то используется родительский. В примере User, таким образом, использует constructor от Person.

Если у потомка свой constructor, то, чтобы в нём вызвать конструктор родителя – используется синтаксис super() с аргументами для родителя.

```javascript
class Person {
  constructor(name) {
    this.name = name;
  }

  say() {
    alert("Hi " + this.name);
  }
}

class User extends Person {
  constructor() {
    // вызвать конструктор Person с аргументом "John"
    super("John"); // то же, что и Person.call(this, "John")
  }
}

new User ().say(); // Hi John

```

Для такого вызова есть небольшие ограничения:
- Вызвать конструктор родителя можно только изнутри конструктора потомка. В частности, super() нельзя вызвать из произвольного метода.
- В конструкторе потомка мы обязаны вызвать super() до обращения к this. До вызова super не существует this, так как по спецификации в этом случае именно super инициализирует this

### super

Оно предназначено только для использования в методах объекта.

Вызов super.parentProperty() позволяет из метода объекта получить свойство его прототипа.

Свойство super работает через прототип, на уровне методов объекта.

При обращении через super используется [[HomeObject]] текущего метода, и от него берётся \_\_proto__. Поэтому super работает только внутри методов.

### Материалы для прочтения 

- ООП в прототипном стиле https://learn.javascript.ru/prototypes
- Классы https://learn.javascript.ru/class
