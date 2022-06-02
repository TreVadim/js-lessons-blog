---
title: Контекст вызова. Рекурсия
date: "2021-12-08"
description: "Рассмотрение работы this и работы рекурсии"
---

## Контекст вызова this, call, apply...

### Доступ к свойствам объекта

Свойства-функции объектов называют методами. Доступ к свойствам объекта изнутри его метода
осуществляется через ключевое слово this, в котором хранится ссылка на текущий объект. Таким
образом можно объединять действия и данные в одной структуре.

```javascript
let user = {
  firstName: "John",
  lastName: "Doe",
  getName: function() {
    return this.firstName;
  }
}

alert( user.getName() ); // John
```

```javascript
let user = {
  firstName: "John",
  lastName: "Doe",
}

user.getFullName = function() {
  return this.firstName + " " + this.lastName;
}

alert( user.getFullName() ); // "John Doe"
```

ECMAScript 2015 позволяет определять методы объекта в сокращенном формате

```javascript
let user = {
  firstName: "John",
  lastName: "Doe",
  getFullName() {
    return `${this.firstName} ${this.lastName}`;
  }
}

alert( user.getFullName() ); // "John Doe"
```

### Указатель this

Значение this является контекстом вызова и определяется в момент вызова функции. Указатель
this содержит ссылку на объект, являющийся текущим контекстом (объект, чья функция
вызывается)

При вызове функции без контекста в non-strict режиме this получает значение window, в строгом
режиме – undefined

```javascript
function f() {
  "use strict";
  
  return this.name;
}

let user1 = { name: "John Doe", getName: f };
let user2 = { name: "Harry Potter", getName: f };

alert( user1.getName() ); // "John Doe"
alert( user1.getName() ); // "Harry Potter"
```

### Передача контекста

#### неявно, через вызов метода

При обращении к методу объекта значением this является сам объект:

```javascript
let user = {
  name: "John",
  getName: function() {
    console.log(this);
    alert(this.name);
  }
};

user.getName();
```

#### явно, через функцию call

Синтаксис метода: func.call(context, arg1, arg2, ...) , где

- func – вызываемый метод
- context – контекст вызова, то есть значение, которое принимает this
- arg1, arg2 и т.д. – список параметров функции func

```javascript
let getFullName = function() {
  alert(this.firstName + " " + this.lastName);
};

let user = {
  firstName: "John",
  lastName: "Doe"
};

getFullName.call(user); // "John Doe"
```

Вариант с добавлением аргументов

```javascript
let getFullName = function(param1, param2) {
  alert(this[param1] + " " + this[param2]);
};

let user = {
  firstName: "John",
  lastName: "Doe"
};

getFullName.call(user, "firstName", "lastName"); // "John Doe"
```

С помощью метода call можно вызывать методы одного объекта в контексте другого (method
borrowing)

```javascript
let user = {
  firstName: "John",
  sayHello() {
    return "Hi " + this.firstName;
  }
};

let user2 = {
  firstName: "Kate",
};

console.log(user.sayHello.call(user2));
```

```javascript
function joinArgs() {
  // вызов [].slice() скопирует все элементы из this в новый массив
  let args = [].slice.call(arguments);
  
  return args.join(" "); // args - полноценный массив из аргументов
}

alert( joinArgs("What's up", "Mr.", "President") ); // "What's up Mr.President"
```

#### явно, через функцию apply

Вызов функции при помощи func.apply работает аналогично func.call, но принимает массив
аргументов вместо списка

Синтаксис метода: func.apply(context, [args, arg2, ...]) , где

- func – вызываемый метод
- context – контекст вызова, то есть значение, которое принимает this
- [arg1, arg2 и т.д.] – массив параметров функции func

```javascript
let getFullName = function(param1, param2) {
  alert(this[param1] + " " + this[param2]);
};

let user = {
  firstName: "John",
  lastName: "Doe"
};

getFullName.apply(user, ["firstName", "lastName"]); // "John Doe"
```

Использовать более мощный метод apply имеет смысл, когда аргументы формируются
динамически

```javascript
let arr = [1, 5, 2];

let max = arr[0];

for ( let i = 1; i < arr.length; i++ ) {
  if (arr[i] > max) max = arr[i]; 
}

alert(max); // 5
```

```javascript
let arr = [1, 5, 2];

alert( Math.max(1, 5, 2) ); // 5

alert( Math.max(arr) ); // NaN

alert( Math.max.apply(Math, arr) ); // 5
alert( Math.max.apply(null, arr) ); // 5
```

При указании первым аргументом методов call/apply значений null или undefined контекстом
будет глобальный объект

#### неявно, через конструктор

Если функция вызывается через оператор new как конструктор объекта, то this указывает на
создаваемый объект:

```javascript
function User(name) {
  // this = {};
  
  // модификация this, добавление свойств или методов
  this.name = name;
  
  // return this;
}

let john = new User("John");
console.log(john); // { name: "John" }
```

### Потеря контекста

Контекст this не привязан к функции, его значение определяется только при вызове функции как
метода объекта. Как следствие, в некоторых случаях может произойти потеря контекста

```javascript
let bomb = {
  delay: 1000,
  sound: "BOOOM!",
  explode: function() {
    // this === bomb
    
   setTimeout(function() {
     // this === window
     
     alert(this.sound);
   }, this.delay);
  }
}

bomb.explode();
```

Стрелочные функции не имеют собственного this, они исполняются в том контексте, в котором были
созданы. Таким образом, ситуация потери контекста не происходит, что иногда удобно

```javascript
let bomb = {
  delay: 1000,
  sound: "BOOOM!",
  explode: function() {
   setTimeout(() => alert(this.sound), this.delay);
  }
}

bomb.explode();
```

### Привязка контекста методом bind

Встроенный метод bind создает новую функцию (обертку) с зафиксированным контекстом,
переданным в качестве параметра функции

Синтаксис метода: func.bind(context, args, arg2, ...) , где

- func – произвольная функция
- context – контекст, который должен быть привязан к
функции func
- arg1, arg2 и т.д. – список параметров функции func,
которые будут зафиксированы в созданном методе

```javascript
let bomb = {
  delay: 1000,
  sound: "BOOOM!",
  explode: function() {
    // this === bomb
    
   setTimeout(function() {
     // this === window
     
     alert(this.sound);
   }.bind(this), this.delay);
  }
}

bomb.explode();
```

#### Карринг

С помощью метода bind можно зафиксировать не только контекст, но и параметры функции –
такое преобразование называется карринг (currying), или каррирование

Фиксировать можно любое количество параметров исходной функции, новая функция (partial
function) будет использовать оставшиеся

```javascript
function multi(x, y) {
  return x * y;
}

let byTwo = multi.bind(null, 2);

for ( let i = 1; i <= 10; i++ ) {
  console.log( i + " x 2 = " + byTwo(i));
}
```

### Формирование цепочки вызовов (chaining)

Контекст this, содержащий ссылку на текущий объект, позволяет реализовать концепцию цепочки
методов для последовательного выполнения множества команд

```javascript
let counter = {
  count: 0,
  up: function() {
    this.count++;
    return this;
  }
}

counter.up().up().up();
alert(counter.count);
```

## Рекурсия

![rekursiy](./rekursiy.png)

В теле функции могут быть вызваны другие функции для выполнения подзадач.

Частный случай подвызова - когда функция вызывает сама себя. Это называется рекурсией.

```javascript
let arr = [0, 1];

function fibo(count) {
  arr[arr.length] = arr[arr.length - 2] + arr[arr.length - 1];
  count -= 1;
  
  if (count !== 0) {
    return fibo(count);
  }
  
  return arr;
}

console.log(fibo(20));
```

Базис рекурсии - значение на котором рекурсия заканчивается.

Глубина рекурсии - общее кол-во вложенных вызовов.

У каждого вызова функции есть свой "контекст выполнения" (execution context).

Контекст выполнения - это служебная информация, которая соответствует текущему запуску функции. Она включает в себя локальные переменные
функции и конкретное место в коде, на котором находится интерпретатор.

При любом вложенном вызове JavaScript запоминает текущий контекст выполнения в специальной внутренней структуре данных - "стеке контекстов".

Для нового вызова создается свой контекст выполнения, и управление переходит в него, а когда он завершен - старый контекст достается из стека
и выполнение внешней функции возобновляется.

Любая рекурсия может быть переделана в цикл. Как правило, вариант с циклом будет быстрее и эффективнее.

### Материалы для прочтения 

- Методы объектов, this https://learn.javascript.ru/object-methods
- Call, Apply https://learn.javascript.ru/call-apply-decorators
- Bind https://learn.javascript.ru/bind
- setTimeout/setInterval https://learn.javascript.ru/settimeout-setinterval
- Рекурсия https://learn.javascript.ru/recursion
