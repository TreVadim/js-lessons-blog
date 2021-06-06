---
title: Синтаксис, переменные, типы
date: "2021-06-06"
description: "Обзор синтаксиса базовых конструкций. Переменные. Типы"
---

## Пример синтаксиса

```javascript
// Two slashes start single-line comments

var y;
let x; // declaring a variable
const z = 5;

x = 3 + y; // assigning a value to the variable `x`

foo(x, y); // calling function `foo` with parameters `x` and `y`

obj.bar(3); // calling method `bar` of object `obj`

// A conditional statement
if (x === 0) { // Is `x` equal to zero?
   x = 123;
}

// Defining function `baz` with parameters `a` and `b`
function baz(a, b) {
   return a + b;
}
```

## Инструкции и Выражения (statements and expressions)

```javascript
var x // инструкция

32*7 // выражение

// инструкция
if (y >= 0) {
  x = y;
} else {
  x = -y;
}
var x = y >= 0 ? y : -y; // выражение

myFunction(y >= 0 ? y : -y) // выражение

```

## Идентификаторы и имена переменных

- Идентификаторы это имена которые могут выполнять различные роли в JS. Одно из них роль названий переменных. 
- Идентификаторы регистрозависимы. 
- Именем переменной может служить любой правильный идентификатор кроме некоторых зарезервированных.

### Список зарезервированных слов в JS

abstract | arguments | await | boolean |
break | byte | case | catch |
char | class | const | continue |
debugger | default | delete | do |
double | else | enum | eval |
export | extends | false | final |
finally | float | for | function |
goto | if | implements | import |
in | instanceof | int | interface |
let | long | native | new |
null | package | private | protected |
public | return | short | static |
super | switch | synchronized | this |
throw | throws | transient | true |
try | typeof | var | void |
volatile | while | with | yield |


### Правильный идентификатор образуется по следующим правилам:

- Первый знак - Unicode знак, $, _. 
- Следующие знаки - такие же, а также цифры

### Способы объявления переменных

- ES5
    - var
- ES6
    - let 
    - const


### Значения (Values)

В JavaScript переменные не имеют типов. Значения имеют типы. Переменные могут содержать любое значение в любой момент времени

## Значения и типы

- Примитивы: 
    - null
    - undefined
    - boolean
    - number
    - string
    - symbol
    - BigInt
- И Объект:
    - object
    
### Typeof оператор

```javascript
typeof undefined     === "undefined"; // true
typeof true          === "boolean";   // true
typeof 14            === "number";    // true
typeof "14"          === "string";    // true
typeof { test: 14 }  === "object";    // true
typeof 1n            === "bigint";    // true

// added in ES6
typeof Symbol()      === "symbol";    // true

// Strange
typeof null === "object"; // true
```

### Boolean

Возможные значения - true и false

Логические Операторы:
- && - Логическое И
- || - Логическое Или
- ! - Логическое Отрицание

#### Операторы сравнения

Операторы равенства
- ==
- !=
- ===
- !==

Операторы сравнения
- \>
- <
- <=
- \>=

### Number

Один тип для всех чисел. Все числа расцениваются как float.
5.000 === 5

Численный литерал может быть представлен в нескольких видах
- 35 - integer
- 35.24 - float
- 0xFF - hexadecimal

Exponent abbreviation - 5e2 значит 5* 10^2

#### Специальные значения числа

- NaN
- Infinity

#### Арифметические операторы

- Сложение (addition) +
- Вычитание (subtraction) -                     
- Умножение (multiplication) *               
- Деление (division) /                               
- Остаток от деления (remainder) %    
- -Number
- +Number
- --number и number--
- ++number и number++

### String

Строковые литералы - ‘ ’ или “ ” или \` \`

Экранирование \

Строки неизменяемы как и любой примитив

#### Операции со строками

Сравнения с помощью операторов >, <, >=, <= ===  регистрозависимы

Конкатенация строк с помощью оператора +

Получение символа  с помощью [ ]

Длина строки - .length

А также методы прототипа...

#### Методы строк

- .concat 
- .slice 
- .indexOf 
- .includes
- .replace  
- .split

### null

В JavaScript null не является «ссылкой на несуществующий объект» или «нулевым указателем», как в некоторых других языках.

Это просто специальное значение, которое представляет собой «ничего», «пусто» или «значение неизвестно».

### undefined

Специальное значение undefined также стоит особняком. Оно формирует тип из самого себя так же, как и null.

Оно означает, что «значение не было присвоено».

Если переменная объявлена, но ей не присвоено никакого значения, то её значением будет undefined

### Object

Object литерал - {}

Доступ к полям с помощью оператор . или [ ]

Доступен по ссылке - не immutable


## Дополнительно

### Array
Array литерал - [ ]

Доступ к элементу через [ ]

Доступен по ссылке - не immutable

### Constructors (wrappers)

- Number
- String
- Boolean
- Object
- Array

### Function

Function declaration -  function myFunc () {}

Function expression -   var myFunc = function () {} or let myFunc = () => {}

Function constructor  var myFunc = new Function(‘a’, ‘b’, ‘return a+b’)

### Инструкции

- Объявление переменных и присваивание значений (declaring and assigning)
- Циклы (Loops)
- Условия (Conditionals)

### Условия

- if-else statement
- switch statement

### Циклы

Виды циклов
- for
- while
- do while
- for in

Механизмы работы с циклами
- break
- continue
- labels


## Взаимодействие с пользователем

Модальное окно для отображения сообщения - alert
```javascript
alert("Hello world");
```

Модальное окно для получения данных - prompt
```javascript
let x = prompt("Вопрос", "дефолтный ответ");
// возвращает в строке, то что ввел пользователь, или null если отменил
```

Модальное окно для получения ответа ДА\НЕТ - confirm
```javascript
let isOk = confirm("Вопрос");
```

#### Пример код стайла
```javascript
const a = 5;
let b = "test", c = 45, d;

2 + 5 === 7

// loops
do {
  let g = 10;
} while (a < 10)

while (a < 10 ) {
  let g = 10;
}

for (let i = 0; i < 10; i++) {
  let g = 10;
}

// if..else, ?, switch

if (a < 5) {
  let g = 10;
} else if (c < 10) {
  if (!c) {
    let r = 11;
  }
}

// or

if (a > 10) alert("!");
else alert("?");

a > 10 ? alert("!") : alert("?");

switch (a) {
  case 5: {
    alert("5");
    break;
  }
  case 10: {
    alert("10");
    break;
  }
  default: {
    alert("default");
  }
}

// functions

function myFunc(a, b) {
  return a + b;
}

let myFunc = (a, b) => {
  return a + b;
}

let myFunc = (a, b) => a + b;

let myFunc = a => a * 5;
```

### Материалы для прочтения 

- Инструкции и выражения https://puzzleweb.ru/javascript/2_syntax3.php
- "use strict" https://learn.javascript.ru/strict-mode
- переменные https://learn.javascript.ru/variables
- типы данных https://learn.javascript.ru/types
- числа https://learn.javascript.ru/number
- строки https://learn.javascript.ru/string
- alert, prompt, confirm https://learn.javascript.ru/alert-prompt-confirm
