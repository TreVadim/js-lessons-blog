---
title: Объекты. Массивы
date: "2021-11-30"
description: "Рассмотрение объектов и массивов."
---

## Объекты

Объект (хэш-таблица, словарь, ассоциативный массив) - структура для хранения данных в виде
набора неупорядоченных именованных значений, представляющих собой пары формата

{ ключ: значение }

Новый объект создается через оператор { } или путем вызова конструктора new Object()

В литеральной нотации пары ключ: значение разделяются запятой.
Значения ключей объекта могут быть любого типа, включая другие ассоциативные массивы

```javascript
let obj = new Object();
let person = {
  firstName: "John",
  "lastName": "Doe",
  age: 22,
  job: {
    position: "Developer",
    company: "Google"
  }
};
```

Для обращения к свойствам используется запись через “.” вида объект.свойство или через [ ]
вида объект['свойство']

```javascript
console.log( person.firstName ); // "John"
console.log( person["lastName"] ); // "Doe"
console.log( person["age"]); // 22
console.log( person.job.position ); // "Developer"
```

Обращение к ключу через переменную осуществляется только в квадратных скобках

```javascript
let key = "age";
console.log(person[key]); // 22
console.log(person.key); // undefined
```

Для добавления новых полей могут использоваться оба вида записи: через точку и через
квадратные скобки

```javascript
person.married = true;
person["children"] = [];
```

Для удаления полей объекта используется оператор delete

```javascript
delete person.married;
delete person["children"]
```

Пустой объект {} равен true в условном операторе

```javascript
let obj = {};
obj || "empty object is false"; // {}
```

Каждое свойство объекта характеризуется свойствами, описывающими его поведение. По
умолчанию собственные свойства объекта доступны для записи, удаления и для просмотра при
переборе в цикле. Эти настройки можно изменить с помощью метода Object.defineProperty()

ECMAScript 2015 позволяет в качестве ключей объекта использовать выражения

```javascript
let user = {
  [prompt("Enter your name")]: "name",
  [1 + 1]: 2
}
```

### Поиск и перебор свойств

Для проверки наличия свойства в объекте существует оператор in

```javascript
if ("name" in person) alert(person.name);
```

Если свойство объекта не существует, то при обращении к нему возвращается специальное
значение undefined. Поэтому для проверки существования свойства можно получить его и
сравнить с undefined

```javascript
if (person.name !== undefined) alert(person.name);
```

Оператор in также позволяет перебрать объект в цикле

```javascript
for (let key in person) {
  console.log(key, person[key]);
}
```

Получить все перечисляемые свойства объекта в одном массиве можно с помощью функции
Object.keys()

```javascript
console.log( Object.keys(person) ); // ["firstName, "lastName", "age", "job"]
```

Согласно стандарту, если ключ объекта – число или числовая строка, то в целях внутренней
оптимизации браузер сортирует поля объекта; порядок их добавления в ассоциативный массив не
имеет значения, поэтому не стоит рассчитывать на его соблюдение

```javascript
let codes = {
  7: "Россия",
  38: "Украина",
  1: "США"
}

for (let key in codes) {
  console.log(key, codes[key]);
}
```

### Копирование объектов
В отличие от примитивных типов данных все объекты являются мутабельными, взаимодействие с
ними осуществляется по ссылке

```javascript
console.log( {} === {} ); // false

let obj = {};
console.log( obj === obj ); // true
```
```javascript
let user1 = { name: "John", age: 20 };
let user2 = user1;

delete user1.age;
user2.name = "Tom";

console.log(user1); // { name: "Tom" }
console.log(user2); // { name: "Tom" }
```

Для того, чтобы создать независимую копию объекта, можно перебрать его поля в цикле, записав
значения полей в новый объект под соответствующими ключами

```javascript
let user1 = { name: "John", age: 20 };
let user2 = {};

for (let key in user1) {
  user2[key] = user1[key];
}

delete user1.age;
user2.name = "Tom";

console.log(user1); // { name: "John" }
console.log(user2); // { name: "Tom", age: 20 }
```

Метод Object.assign(target, ...sources) переписывает из одного или нескольких исходных объектов ...sourses собственные 
перечисляемые свойства в целевой объект target и возвращает новый объект

```javascript
let user1 = { name: "John", age: 20 };
let user2 = Object.assign({}, user1);

delete user1.age;
user2.name = "Tom";

console.log(user1); // { name: "John" }
console.log(user2); // { name: "Tom", age: 20 }
```

C помощью оператора spread(...) можно создать независимую копию объекта

```javascript
let user1 = { name: "John", age: 20 };
let user2 = {...user1};

delete user1.age;
user2.name = "Tom";

console.log(user1); // { name: "John" }
console.log(user2); // { name: "Tom", age: 20 }
```

### Деструктуризация объекта

Деструктуризация (destructuring assignment) - синтаксис ES6, позволяющий записать в
переменные значения свойств объекта

```javascript
let user = { 
  firstName: "John",
  lastName: "Doe", 
  age: 20 
};

let { firstName, age } = user;
console.log(firstName, age); // "John", 20
console.log(lastName); // lastName is not defined
```

По желанию можно менять имя переменной, в которую будет записано соответствующее свойство
объекта

```javascript
let user = { 
  firstName: "John",
  lastName: "Doe", 
  age: 20 
};

let { firstName: name, age } = user;
console.log(name, age); // "John", 20
console.log(firstName); // lastName is not defined
```

Если искомых свойств в объекте нет, в переменную попадет значение undefined, или же можно
указать значение по умолчанию, которое примет переменная в таком случае

```javascript
let user = {};

let { firstName = "Anonymous", lastName: family = "Guest" } = user;
console.log(firstName, family); // "Anonymous", "Guest"
```

## JSON

JSON (JavaScript Object Notation) – универсальный формат описания и хранения древовидных
структур данных

JSON-формат не зависит от языка программирования и используется для обмена данными, в
частности, между клиентом и сервером

```javascript
let user = {
  "firstName": "John",
  "last name": "Doe",
  "age": 20
};
```

Для отправки данных на сервер осуществляется сериализация объекта – преобразование объекта в
пригодный для хранения формат, например, в строку

```javascript
JSON.stringify(user); // '{"firstName": "John", "last name": "Doe", "age": 20}'
```

Недопустимо – подвергать сериализации структуры, ссылающиеся на самих себя

```javascript
let a = {}, b = {};

a.b = b;
b.a = a;

let json = JSON.stringify(a); // TypeError: Converting circular structure of JSON
```

Обратный процесс – преобразования json-строки к объекту – называется десериализацией

```javascript
JSON.parse('{"firstName": "John", "last name": "Doe", "age": 20}');
// { firstName: "John", last name: "Doe", "age": 20 };
```

Синтаксис JSON очень схож с литеральной записью объекта, однако требования к формату более
строгие:

- ключи обязательно должны быть обернуты в кавычки
- одинарные кавычки не допускаются
- после последней пары ключ-значения не должно быть запятой
- не должно быть циклических ссылок
- поля-методы не сериализуются

## Массивы

Массив (таблица, матрица, очередь) – вид объекта, представляющий собой упорядоченный набор
пронумерованных данных.

```javascript
let data = []; // Пустой массив
let frameworks = ["Angular", "ReactJS", "Vue"]; // массив строк
let list = new Array("jQuery", "Prototype"); // альтернативный способ создания массива
let emptySlots = new Array(4); // массив с 4мя пустыми элементами
```

Узнать количество элементов в массиве можно с помощью свойства length:

```javascript
console.log(data.length); // 0
console.log(frameworks.length); // 3
```

Элементы массива нумеруются от 0 до length - 1. Обращение к элементу массива осуществляется
по индексу:

```javascript
console.log(frameworks[0]); // "Angular"
console.log(frameworks[2]); // "Vue"
```

В JavaScript массив может содержать значения разного типа, включая другие массивы или
объекты:

[0, '', false, NaN, null, undefined, {}, [], new Date()]

Пустой массив [] равен true в условном операторе:

```javascript
let arr = [];
arr || "empty array is false"; // []
```

### Работа с массивом

```javascript
let food = ["potato", "tomato", "banana"];

// Добавление в конец массива
food.push("cucumber"); // 4
// или так
food[food.length] = "carrot";

// Добавление элемента в начало массива со смещением индексов
food.unshift("cabbage"); // 5

// Удаление элемента
food.pop(); // последний элемент, возвращает удаленный элемент
food.shift(); // первый элемент, возвращает удаленный элемент
food.splice(3, 1); // один элемент с позиции 3, возвращает массив удаленных элементов
```

### Перебор массива

Для работы с массивом зачастую используются циклы

```javascript
let food = ["potato", "tomato", "banana"];

for (let i = 0; i < food.length; i++) {
  console.log(food[i]);
}
```

В новой спецификации появился новый тип цикла for..of, который перебирает значения массива
в виде ключей

```javascript
let food = ["potato", "tomato", "banana"];

for (let key of food) {
  console.log(key); // "potato", "tomato", "banana"
}
```

Для обхода многомерного массива (массива массивов) используются вложенные циклы

```javascript
let matrix = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
];

for (let i = 0; i < matrix.length; i++) {
  console.log(matrix[i]);
  
  for (let j = 0; j < matrix[i].length; j++) {
    console.log(matrix[i][j]);
  } 
}
```

Копирование и сравнение массивов

Массивы так же, как объекты, копируются по ссылке. То есть если два массива ссылаются на одну
и ту же сущность, то изменение структуры данных одного из массивов приведет к изменению
другого массива.

Для того, чтобы создать независимую копию массива, можно скопировать все его значения…

```javascript
let a = [1, 2, 3];
let b = [];

for (let i = 0; i < a.length; i++) {
  b[i] = a[i];
}

a.push(4);

console.log(a); // [1, 2, 3, 4];
console.log(b); // [1, 2, 3];
```

... или воспользоваться функцией объекта Array для получения среза массива:

```javascript
let a = [1, 2, 3];
let b = a.slice();

a.push(4);

console.log(a); // [1, 2, 3, 4];
console.log(b); // [1, 2, 3];
```

... или воспользоваться spread оператором (...):

```javascript
let a = [1, 2, 3];
let b = [...a];

a.push(4);

console.log(a); // [1, 2, 3, 4];
console.log(b); // [1, 2, 3];
```

## Преобразование объектов

### Логическое преобразование

Любой объект в логическом контексте - true, даже если это пустой массив - [ ] или объект - { }

```javascript
if ([] && {}) {
  alert("Все объекты true!!");
}
```

### Строковое преобразование toString()
#### Объекты

Стандартным строковым представлением объекта является строка "[Object object]".
Если в объекте присутствует метод toString(), который возвращает примитив, то он используется для преобразования

#### Массивы

Стандартным строковым представлением массива является строковое 
представление всех его элементов, перечисленных через запятую

### Численное преобразование valueOf() || toString()

Для численного преобразования объекта используется метод valueOf, а если его нет – то
toString

### Нюанс преобразования

```javascript
{} + [] // ?
[] + {} // ?
```

### Материалы для прочтения 

- Объекты https://learn.javascript.ru/object
- Преобразование объектов https://learn.javascript.ru/object-toprimitive
- Дескрипторы https://learn.javascript.ru/property-descriptors
- Массивы https://learn.javascript.ru/array
- Методы работы с массивом https://learn.javascript.ru/array-methods
- Деструктуризация https://learn.javascript.ru/destructuring-assignment
- JSON https://learn.javascript.ru/json
