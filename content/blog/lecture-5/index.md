---
title: Функции
date: "2021-11-29"
description: "Рассмотрение функций."
---

## Функции

В программировании используются два ключевых понятия: данные и алгоритмы. Для хранения
данных используются переменные, для обработки данных по определенному алгоритму
предназначены функции. По сути функции являются подпрограммами, которые позволяют повторно
использовать определенные блоки кода в разных местах программы и, таким образом, избавляют от
дублирования кода и помогают лучше структурировать программу

Пример встроенных функций JavaScript - alert('Message') , confirm('Question') ,
prompt('Title', 'Default')

Синтаксис объявления функции:

```javascript
function имяФункции() {
  // тело функции
}

let имяФункции = () => {
  // тело функции
}
```

Обращение к функции осуществляется по имени, круглые скобки инициируют выполнение функции

```javascript
function sayHi() {
  alert("Hi");
}

sayHi();
```

### Выбор имени

Выбор имени функции подчиняется тем же правилам, что и выбор имени переменной. Названия
переменных и функций должны соответствовать тому, что они хранят и делают. Из этого следует, что
название функции должно быть глаголом и обозначать общий характер действия. То есть из
названия должно быть понятно назначение функции

Еще немного об именовании...

Для обозначения характера действия зачастую используются глагольные префиксы. К примеру,
функции, которые что-либо возвращают, начинаются со слова get(getUser()), которые что-либо
создают - со слова create (createUser()), которые что-то удаляют – со слова delete
(deleteUser()) и т.д.

Функция должна выполнять только то действие, которое явно подразумевается в ее названии. К
примеру, функция, которая показывает сообщение, не должна ничего возвращать, и наоборот

### Параметры функции

В функцию можно передать параметры, от которых будет зависеть ее поведение

```javascript
function sayHi(name) {
  alert(`Hello, ${name}`);
}

sayHi("John"); // Hello, John
```

Аргументы, указанные при определении функции, называются формальными параметрами.

Аргументы, передаваемые в функцию при ее вызове, называются фактическими параметрами.

Параметры интерпретируются как локальные переменные, то есть доступны только внутри этой
функции:

```javascript
let name = "John";

function createUser(name) {
  alert(`Hello ${name}`);
}

createUser("Jane"); // Hello Jane
```

В спецификации ES6 добавлена возможность деструктуризации входящих параметров: если передается объект, то его поля можно
разложить в соответствующие переменные

```javascript
let user = {
  firstName: "John",
  lastName: "Doe"
};

function createUser({ firstName, lastName }) {
  alert(firstName + " " + lastName);
}

createUser(user); // John Doe
```

### Значения по умолчанию

Если параметр не был передан при вызове функции, то его значение равно undefined. В
спецификации ECMAScript 5 нет возможности определить значения по умолчанию в списке
параметров функции. Для этих целей используются два способа:

1. условный оператор:

```javascript
function createUser(name) {
  if (name === undefined) name = "Anonymous";
  alert("Hello " + name);
}

createUser(); // Hello Anonymous
```

2. логический оператор ИЛИ:

```javascript
function createUser(name) {
  name = name || "Anonymous";
  alert("Hello " + name);
}

createUser(); // Hello Anonymous
```

Необязательные параметры функции, как правило, располагают в конце списка аргументов

В стандарте ES6 можно указывать значения по умолчанию в перечне формальных параметров

```javascript
function createUser(name = 'Anonymous') {
  alert("Hello " + name);
}

createUser(); // Hello Anonymous
```

### Псевдомассив arguments

Функцию можно вызывать с любым количеством аргументов. Если аргументов передано больше, чем
объявлено в списке параметров функции, то ошибки не будет, а получить к ним доступ можно через
специальный объект arguments - псевдомассив аргументов функции, который имеет свойство
length (как и массив) и список значений по номерам

```javascript
function doFunction(name) {
  console.log(name); // Ivan
  console.log(arguments[0], arguments[1]); // Ivan, Maria
}

doFunction("Ivan", "Maria");
```

#### Arguments как массив

Как и обычный массив, arguments можно перебрать в цикле for:

```javascript
function doFunction(name) {
  for (let i = 0; i < arguments.length; i++) {
    console.log(arguments[i]);
  }
}

doFunction("Ivan", "Maria");
```

Однако ничто не мешает превратить arguments в настоящий массив:

```javascript
function doFunction(name) {
  let args = [];
  for (let i = 0; i < arguments.length; i++) {
    args[i] = arguments[i];
  }
  
  alert(args.join(" + "));
}

doFunction("Ivan", "Maria");
```

В стандарте ES6 для получения списка аргументов функции можно использовать оператор ...
(rest), который включает фактические параметры, не описанные в списке формальных параметров
функции. В отличие от arguments объект rest является настоящим массивом, то есть ему
доступны все методы массивов

```javascript
function showMessage(text, ...persons) {
  alert(`${text} ${persons.join(", ")}`);
}

showMessage("Hello to", "Maria", "John", "Jack");
```

### Директива return

#### Возврат значения

В некоторых языках программирования есть два понятия для определения методов - процедуры и
функции. Процедурами являются блоки кода, которые выполняют какие-либо действия, а
функциями являются методы, которые обрабатывают данные и возвращают значение. В JavaScript
функции могут выполнять обе эти роли, в частности, возвращать значение - при помощи директивы
return

```javascript
function checkAge(age) {
  if (age < 18) return "Forbidden";
  else return "Allow";
}

alert( checkAge(10) );
```

Если функция не возвращает значение явно с помощью return, то есть результат ее работы
неопределён, функция возвращает undefined

#### Принудительный выход из функции

Директив return внутри функции может быть несколько, и размещаться они могут в любом месте
кода, но как только интерпретатор доходит до первой, выполнение функции завершается

Директива return может возвращать значение или не возвращать - второй способ используется
для принудительного выхода из функции:

```javascript
function checkAge(age) {
  if (age < 18) return;
  
  alert("Hello!");
}

alert( checkAge(20) );
```

### Способы объявления функции

#### Function Declaration

В спецификации языка функция, объявленная в основном потоке кода, называется Function
Declaration и имеет синтаксис: function имя(параметры) {...} . Это классическое
представление функции

```javascript
function sayHello(userName) {
  alert("Hello " + userName);
}

sayHello("John");
sayHello("Jane");
```

#### Function Expression

Для объявления функции существует альтернативный способ, который называется Function
Expression (функциональное выражение). Функция объявляется в контексте присваивания.

Синтаксис: var имя = function() {...}

```javascript
let sum = function(a, b) {
  alert(a + b);
}

sum(1, 2);
```

#### Function Declaration или Function Expression

Основное отличие между способами объявления функций в том, что Function Declaration создаются
интерпретатором до выполнения кода (hoisting), а Function Expression создается в момент
выполнения, то есть, когда интерпретатор до нее добирается. Фактически это значит, что функции,
объявленные в основном потоке, можно вызывать до их определения, но функциональные
выражения – строго после

Function Declaration
```javascript
sum(1, 2); // 3

function sum(a, b) {
  alert(a + b);
}
```

Function Expression
```javascript
sum(1, 2); // TypeError: sum is not a function

let sum = function(a, b) {
  alert(a + b);
}
```

Спецификация ES6 добавляет новый синтаксис определения функциональных выражений – через
стрелку, т.н. стрелочные функции

```javascript
let square = x => x * x;

alert( square(25) );
```

```javascript
let sum = (a, b) => {
  return a + b;
}

alert( sum(2 + 5) );
```

*Стрелочные функции не имеют собственного arguments, для получения списка фактических
параметров следует использовать оператор ...

### Функции как объекты первого класса

Объектами первого класса (англ. first-class object, first-class entity, first-class citizen) в контексте конкретного языка 
программирования называются элементы, которые могут быть переданы как параметр, возвращены из функции, присвоены переменной.

#### Функция как значение

В JavaScript функция является таким же значением, как число или строка. Это значит, что функцию
можно присвоить переменной (функциональное выражение) или скопировать в другую функцию

```javascript
function doWhat() {
  console.log("Nothing");
}

let func = doWhat;
doWhat = null;
func(); // Nothing
doWhat(); // TypeError: doWhat is not a function
```

#### Функция как параметр

Функции также могут передаваться в другую функцию в качестве параметра. Такие функции не
имеют имени (anonymous functions) и выполняют роль функций обратного вызова (callback)

```javascript
function getConfirmation(question, ok, cancel) {
  if ( confirm(question) ) ok();
  else cancel();
}

getConfirmation(
  "Are you sure?", 
  () => { console.log("You sure"); },
  () => { console.log("You're not sure"); }
);
```

#### Функция как результат

Анонимная функция может возвращаться как результат работы другой функции

```javascript
function outer() {
  return function() {
    alert("I'm the inner function, hello!")
  }
}

let inner = outer();
console.log(inner);
inner();
```

### Область видимости переменных

#### Глобальная область видимости

Переменные, объявленные вне функции, считаются глобальными, и доступны внутри функции как
для чтения, так и для записи. Область видимости переменных, объявленных вне функции, -
глобальный объект window

```javascript
var number = 1;

function doFunction() {
  number = 5;
}

doFunction();
alert(number); // 5
```

#### Область видимости на уровне функции

Переменные, объявленные внутри функции, называются локальными, и доступны только внутри
функции

```javascript
var number = 1;

function doFunction() {
  number = 5;
  
  var b = 0;
}

doFunction();
alert(b); // ReferenceError: b is not defined
```

### Лексическое окружение

Все локальные переменные функции при ее вызове попадают в специальный внутренний объект
LexicalEnvironment (лексическое окружение, объект переменных). Во время выполнения функции
переменные в объекте LexicalEnvironment получают значения. После выполнения функции объект
уничтожается, и память очищается.

Код внутри функции имеет доступ как к локальным переменным, так и к переменным из внешней по
отношению к функции области видимости. Ссылка на внешний объект переменных хранится в
специальном скрытом свойстве функции [[Scopes]], которое ссылается на лексическое
окружение, в котором функция была создана. При обращении к переменной интерпретатор
начинает искать ее в текущем LexicalEnvironment, а если не находит, то в лексическом окружении
на уровень выше, вплоть до глобального объекта window

```javascript
let user = "John Doe";

function hello() {
  alert("Hello, " + user);
}

hello();
```

Функция берет то значение переменной из внешнего лексического окружения, которое там есть на
текущий момент времени

```javascript
let user = "John Doe";

function hello() {
  alert("Hello, " + user);
}

hello();

user = "Harry Potter";

hello();
```

Поиск в области видимости прекращается, как только найдено первое совпадение. Несколько
переменных могут иметь одно имя идентификатора и располагаться в разных слоях вложенных
областей видимости. В таком случае внутренний идентификатор имеет приоритет по отношению ко
внешнему – это называется "затенение"(shadowing)

```javascript
let user = "John Doe";

function hello(user) {
  alert("Hello, " + user);
}

hello();
```

### Замыкания (closures)

Внутри функции можно определять не только переменные, но и другие функции. Вложенные
функции получают доступ ко внешнему лексическому окружению, то есть к локальным переменным
родительской функции. Эти переменные продолжают существовать в LexicalEnvironment внешней
функции и доступны изнутри вложенной функции даже после того, когда обе функции отработали.
Замыкание – это лексическое окружение, переменные которого доступны всем внутренним
объектам переменных LexicalEnvironment

```javascript
function createCounter() {
  let count = 0;
  
  return function() {
    return ++count;
  }
}

let counter = createCounter();

alert(counter()); // 1
alert(counter()); // 2
alert(counter()); // 3
```

### Материалы для прочтения 

- Функции https://learn.javascript.ru/function-basics
- Функциональные выражения https://learn.javascript.ru/function-expressions
- Именованные функциональные выражения https://learn.javascript.ru/function-object
- О замыкании, области видимости https://learn.javascript.ru/closure
- Псевдомассив arguments https://learn.javascript.ru/rest-parameters-spread-operator
- Перебирающие методы массива https://learn.javascript.ru/array-methods
