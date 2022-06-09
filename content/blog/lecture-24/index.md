---
title: Classes & Interface
date: "2021-11-10"
description: Classes and interfaces in TypeScript
---

## Classes

Немного вспомним как работать с классами и посмотрим работу с ними в TypeScript.

В ООП мы организовываем свою программу на взаимодействии объектов между собой. Классы служат нам словно как чертеж будущих 
объектов, где мы можем прописать что объект будет хранить и что может делать.

Чтобы создать класс, нам необходимо использовать ключевое слово class.

```typescript
class Department {
  name: string;

  constructor(n: string) {
    this.name = n;
  }
}

const accounting = new Department('Accounting');
```

Тут мы создали класс Department у которого есть свойство name. Значение свойству мы устанавливаем в конструкторе. Хочу 
отметить, что в TypeScript, мы должны прописать заранее свойство и указать ему тип. Иначе будет ошибка.

Давайте посмотрим что мы получим, если скомпилируем этот код

```javascript
var Department = /** @class */ (function () {
  function Department(n) {
    this.name = n;
  }
  return Department;
}());

var accounting = new Department('Accounting');
```
Мы тут увидели функцию-конструктор. Почему так? Дело в том, что мы не настраивали наш компилятор и не указывали в какую 
версию JavaScript нам нужно сделать перевод. Как видите по умолчанию мы получаем es5. Стандартный подход с применением 
функций-конструкторов. Если бы мы поставили в настройках компилятора, что нам нужен es6, то получили бы вот такой результат 

```javascript
class Department {
  constructor(n) {
    this.name = n;
  }
}

const accounting = new Department('Accounting');
```

Уже что-то больше знакомое для нас. Есть тут маленькое отличие - тут нет заранее созданного свойства name. Опять почему так? 
Дело в том, что эта возможность доступна в более современных версиях JS, а в es6 её еще нет.

Далее давайте немного расширим наш класс и вспомним как создавать методы

```typescript
class Department {
  name: string;

  constructor(n: string) {
    this.name = n;
  }
  
  describe() {
    console.log(`Department ${this.name}`)
  }
}

const accounting = new Department('Accounting');
```

Для обращения к свойствам внутри методов, нам нужно использовать this. Не забывайте про это!

### Access Modifiers

Для ограничения доступа к некоторым свойствам и методам мы можем указывать маркеры доступа. По умолчанию все свойства и 
методы публичные, а значит мы можем их использовать без каких либо ограничений. В нашем примере мы так можем использовать 
свойство name и метод describe. Давайте добавим еще несколько методов.

```typescript
class Department {
  name: string;
  employees: string[] = [];

  constructor(n: string) {
    this.name = n;
  }
  
  describe() {
    console.log(`Department ${this.name}`)
  }
  
  addEmployee(employee: string) {
    this.employees.push(employee);
  }
  
  printEmployeeInformation() {
    console.log(this.employees.length);
    console.log(this.employees);
  }
}

const accounting = new Department('Accounting');

accounting.addEmployee('Max');
accounting.addEmployee('Trevis');
accounting.printEmployeeInformation();
```

В таком виде у нас есть специальный метод для добавления работников. Но у нас все свойства и методы публичные, а значит 
мы можем напрямую изменить массив работников

```typescript
accounting.employees[2] = 'John';
```

Когда мы разрабатываем комплексное приложение, мы должны стараться не допускать такого прямого изменения данных. Например, 
вы работаете в большой команде и получите такую картину, что один коллега добавляет работников через специальный метод, а 
другой напрямую, и в итоге получите бардак и возможную ошибку при работе кода. Идеальный вариант иметь один 
унифицированный метод. И предотвратить возможность добавления работников напрямую в свойство. Для ограничения мы добавим 
маркер доступа **private**.

```typescript
class Department {
  name: string;
  private employees: string[] = [];

  constructor(n: string) {
    this.name = n;
  }
  
  describe() {
    console.log(`Department ${this.name}`)
  }
  
  addEmployee(employee: string) {
    this.employees.push(employee);
  }
  
  printEmployeeInformation() {
    console.log(this.employees.length);
    console.log(this.employees);
  }
}

const accounting = new Department('Accounting');

accounting.addEmployee('Max');
accounting.addEmployee('Trevis');
accounting.employees[2] = 'John';

accounting.printEmployeeInformation();
```
В данном коде мы получим ошибку из-за того что пытаемся поменять приватное свойство напрямую

```text
Property 'employees' is private and only accessible within class 'Department'.
```

Теперь мы можем добавлять работником только через метод addEmployee.

#### readonly

Еще одна возможность повлиять на доступ к свойству это ключевое слово **readonly**. Этот маркер позволяет нам получить 
такое поведение: при инициализации свойства ему задается значение и мы его не можем изменить в течении работы нашего кода. 
Получаем нечто наподобие константы. Для получения данного поведения достаточно указать перед свойством ключевое слово 
readonly.

Давайте добавим такое свойство и назовем его id.

```typescript
class Department {
  name: string;
  readonly id: string;
  private employees: string[] = [];

  constructor(name: string, id: string) {
    this.name = name;
    this.id = id;
  }
  
  describe() {
    console.log(`Department ${this.name}`)
  }
  
  addEmployee(employee: string) {
    this.employees.push(employee);
  }
  
  printEmployeeInformation() {
    console.log(this.employees.length);
    console.log(this.employees);
  }
}

const accounting = new Department('Accounting', '12312424');
```

В связке с маркером доступа мы можем записать его так **private readonly id: string**. Если мы попробуем его поменять, то 
получим соответствующую ошибку:

```text
addEmployee(employee: string) {
    this.id = "445";
    this.employees.push(employee);
}

...

Cannot assign to 'id' because it is a read-only property.
```

В нативном JavaScript нет этих маркеров, поэтому при компиляции мы не увидим их.

### Shorthand Initialization

Если мы посмотрим на наш класс, то увидим, что при добавлении свойств в класс, нам нужно делать две вещи: 
1. Заранее создать свойство.
2. В конструкторе присвоить значение.

Не очень удобно получается. Было бы намного удобнее чтобы мы сразу имели возможность создать свойство и сразу же в 
конструкторе назначить значение в это свойство. TypeScript предоставляет нам эту возможность. Для этого в аргументах 
конструктора нам нужно сделать специальную запись.

```typescript
class Department {
  private employees: string[] = [];

  constructor(private name: string, private readonly id: string) {}
  
  describe() {
    console.log(`Department ${this.name}`)
  }
  
  addEmployee(employee: string) {
    this.employees.push(employee);
  }
  
  printEmployeeInformation() {
    console.log(this.employees.length);
    console.log(this.employees);
  }
}

const accounting = new Department('Accounting', '12312424');
```

## Inheritance

Наследование позволяет нам создавать новые классы на основе уже существующих. Тему самого наследования мы проходили ранее. 
Единственное, что хочу напомнить, что наследование в классах мы осуществляем с помощью ключевого слова extends.

Для примера сделаем так, что класс Department будет у нас базовый, и создадим на его основе класс ITDepartment и 
AccountDepartment

```typescript
class Department {
  private employees: string[] = [];

  constructor(private name: string, private readonly id: string) {
  }

  describe() {
    console.log(`Department ${this.name}`)
  }

  addEmployee(employee: string) {
    this.employees.push(employee);
  }

  printEmployeeInformation() {
    console.log(this.employees.length);
    console.log(this.employees);
  }
}

class ITDepartment extends Department {
  constructor(id: string, public admins: string[]) {
    super('IT', id);
  }
}

class AccountDepartment extends Department {
  constructor(id: string, private reports: string[]) {
    super('Accounting', id);
  }
  
  addReport(text: string) {
    this.reports.push(text);
  }
  
  printReports() {
    console.log(this.reports);
  }
}

const it = new ITDepartment('1', ['John']);
const account = new AccountDepartment('2', ['Best report']);
```

Напоминаю также, что если мы реализовали конструктор в дочернем классе, нам нужно вызвать super, чтобы инициировать вызов 
конструктора родителя.

### protected

Давайте попробуем переназначить метод addEmployee в классе AccountDepartment и будем иметь там логику работы с массивом 
employee.

```typescript
class Department {
  private employees: string[] = [];

  constructor(private name: string, private readonly id: string) {
  }

  describe() {
    console.log(`Department ${this.name}`)
  }

  addEmployee(employee: string) {
    this.employees.push(employee);
  }

  printEmployeeInformation() {
    console.log(this.employees.length);
    console.log(this.employees);
  }
}

class ITDepartment extends Department {
  constructor(id: string, public admins: string[]) {
    super('IT', id);
  }
}

class AccountDepartment extends Department {
  constructor(id: string, private reports: string[]) {
    super('Accounting', id);
  }

  addEmployee(name: string) {
    if (name === 'John') {
      return;
    }
    this.employees.push(name);
  }

  addReport(text: string) {
    this.reports.push(text);
  }

  printReports() {
    console.log(this.reports);
  }
}

const it = new ITDepartment('1', ['John']);
const account = new AccountDepartment('2', ['Best report']);
```

тут мы получаем ошибку:
```text
Property 'employees' is private and only accessible within class 'Department'.
```
Почему мы тут получили ошибку? Ведь мы унаследовали все свойства и методы базового класса. Дело в том, что если свойство 
приватное, то оно доступно для использования только внутри класса где оно было создано. Поэтому свойство employee доступно 
только внутри класса Department.

Если мы хотим предоставить доступ для работы с этим свойством всем дочерним классам, но оставить ограничение на работу с ним 
извне, то нам нужно изменить маркер доступа с private на protected.

```typescript
class Department {
  protected employees: string[] = [];

  constructor(private name: string, private readonly id: string) {
  }

  describe() {
    console.log(`Department ${this.name}`)
  }

  addEmployee(employee: string) {
    this.employees.push(employee);
  }

  printEmployeeInformation() {
    console.log(this.employees.length);
    console.log(this.employees);
  }
}

class ITDepartment extends Department {
  constructor(id: string, public admins: string[]) {
    super('IT', id);
  }
}

class AccountDepartment extends Department {
  constructor(id: string, private reports: string[]) {
    super('Accounting', id);
  }

  addEmployee(name: string) {
    if (name === 'John') {
      return;
    }
    this.employees.push(name);
  }

  addReport(text: string) {
    this.reports.push(text);
  }

  printReports() {
    console.log(this.reports);
  }
}

const it = new ITDepartment('1', ['John']);
const account = new AccountDepartment('2', ['Best report']);
```

### getters and setters

Так же нам предоставляется удобная работа с геттерами и сеттерами. Для работы с приватными свойствами зачастую создают 
специальные методы для получения и установки значение. Это делается для того, чтобы добавить свою логику валидации или логику 
конвертации данных.

Давайте добавим приватное свойство lastReport и напишем геттер и сеттер к ниму.

```typescript
class Department {
  protected employees: string[] = [];

  constructor(private name: string, private readonly id: string) {
  }

  describe() {
    console.log(`Department ${this.name}`)
  }

  addEmployee(employee: string) {
    this.employees.push(employee);
  }

  printEmployeeInformation() {
    console.log(this.employees.length);
    console.log(this.employees);
  }
}

class AccountDepartment extends Department {
  private lastReport: string;
  
  constructor(id: string, private reports: string[]) {
    super('Accounting', id);
    this.lastReport = reports[0];
  }
  
  get mostRecentReport() {
    if (this.lastReport) {
      return this.lastReport;
    }
    throw new Error('No report found.');
  }
  
  set mostRecentReport(value: string) {
    if (!value) {
      throw new Error('Please pass in a valid value!');
    }
    
    this.addReport(value);
  }

  addEmployee(name: string) {
    if (name === 'John') {
      return;
    }
    this.employees.push(name);
  }

  addReport(text: string) {
    this.reports.push(text);
    this.lastReport = text;
  }

  printReports() {
    console.log(this.reports);
  }
}

const account = new AccountDepartment('2', ['Best report']);

console.log(account.mostRecentReport); // getter output 'Best report'
account.mostRecentReport = 'Report #2'; // setter
console.log(account.mostRecentReport); // output 'Report #2'
```

### static

Указатель статичного метода есть как в JavaScript, так и в TypeScript. Порой он удобен для того, чтобы какое-то значение 
хранилось в классе и было легко его получить. К примеру, у нас есть Math.PI. Очень важно знать, что в статичном методе 
мы не можем работать с this. Это потому что при работе со статичным методом/свойством не нужен заранее вызванный класс через ключевое 
слово new.

```typescript
class Department {
  protected employees: string[] = [];
  static year: string = '2022';

  constructor(private name: string, private readonly id: string) {}
  
  static createEmployee(name: string) {
    return { name };
  }

  describe() {
    console.log(`Department ${this.name}`)
  }

  addEmployee(employee: string) {
    this.employees.push(employee);
  }

  printEmployeeInformation() {
    console.log(this.employees.length);
    console.log(this.employees);
    console.log(Department.year);
  }
}

const user = Department.createEmployee('John');
console.log(Department.year);
```

Обратите внимание на использование метода/свойства32, мы его делаем без ключевого слова **new**.

### abstract

TypeScript позволяет нам создавать абстрактные классы. Они бывают полезны для того, чтобы мы могли обозначить какие методы 
должны реализовать дочерние классы. К примеру в задаче по рисованию фигур, мы должны были реализовать метод draw для отрисовки.
Давайте на этом же примере рассмотрим эту тему.

```typescript
abstract class Figure {
  constructor(protected x: number, protected y: number, protected color: string) {
  }

  abstract draw(ctx: any): void;
}

class Rect extends Figure {
  log() {
    console.log('This is a Rect');
  }
}

class Circle extends Figure {
  log() {
    console.log('This is a Circle!');
  }

  draw(ctx: any) {
    console.log('We are drawing a circle');
  }
}

const rect = new Rect(3, 45, 'red');
const circle = new Circle(421, 555, 'blue');
```

В этом примере мы создали абстрактный класс Figure и наследовали его классами Rect и Circle. Каковы особенности при работе 
с абстрактным классом:
- мы ОБЯЗАНЫ реализовать все абстрактные методы/свойства в дочерних классах, иначе мы получим ошибку
```text
Non-abstract class 'Rect' does not implement inherited abstract member 'draw' from class 'Figure'.
```
- абстрактный класс не может быть вызван через ключевое слово new 
```text
Cannot create an instance of an abstract class.
```

## Interfaces

Если просто сказать что такое интерфейс, то это описание структуры объекта. 

Для создания интерфейса нам необходимо использовать ключевое interface и указание название его.

```typescript
interface Person {
  name: string;
  age: number;
  greet(phrase: string): void;
}

let user1: Person;

user1 = {
  name: 'John',
  age: 43,
  greet(phrase: string) {
    console.log(`${phrase} ${this.name}`);
  }
}
```

Как вы видите, интерфейс работает как тип для объекта. Но интерфейс также может работать и с классами. Давайте посмотрим 
на это.

С помощью интерфейса мы можем указать какой вид должен есть объект созданный классом. Для указания того, что класс должен 
следовать интерфейсу нужно воспользоваться ключевым словом implements и указать интерфейс (если их несколько, то перечислить 
через запятую)

```typescript
interface Greetable {
  name: string;

  greet(phrase: string): void;
}

class Person implements Greetable {}
```

В данном случае мы пока имеем ошибку

```text
Class 'Person' incorrectly implements interface 'Greetable'.
  Type 'Person' is missing the following properties from type 'Greetable': name, greet
```

давайте исправим это

```typescript
interface Greetable {
  name: string;

  greet(phrase: string): void;
}

class Person implements Greetable {
  age = 30;
  constructor(public name: string) {}

  greet(phrase: string) {
    console.log(`${phrase} ${this.name}`);
  }

  hello() {
    console.log("1", 1);
  }
}

let user1: Greetable;
user1 = new Person('John');
```

Получается, что интерфейсом мы можем не только указать тип, но и указать какой минимальный набор методов и свойств должен 
реализовать класс. При использовании интерфейса, нам не важно что еще есть в объекте или что содержит класс. Нам важно, 
то что если мы реализовали интерфейс в классе, то там есть точно, то что прописано в интерфейсе и можем этим пользоваться.

### readonly properties in interface

Для обозначения свойства доступного только для чтения, мы можем воспользоваться все тем же ключевым словом readonly. В 
таком случае нам уже не нужно прописывать этот маркер в классе (если класс имплементирует этот интерфейс). И все будет 
работать так же как и мы рассматривали в теме об readonly

```typescript
interface Greetable {
  readonly name: string;

  greet(phrase: string): void;
}
```

### Extending interface

Еще одна возможность которая есть в интрефейсах - это наследование. Да! Мы можем наследовать одни интерфейсы от других. 
Тем самым очень удобно организовывать разные структуры данных. Для наследования интрефейса мы можем воспользоваться ключевым 
словом extends. Наследуя интерфейс мы добавляем свойства и методы родительского интерфейса в дочерний.

```typescript
interface Named {
  name: string;
}

interface Greetable extends Named {
  greet(phrase: string): void;
}

class Person implements Greetable {
  age = 30;
  constructor(public name: string) {}

  greet(phrase: string) {
    console.log(`${phrase} ${this.name}`);
  }
}

let user1: Greetable;
user1 = new Person('John');
```

В отличие от классов которые могут наследовать только от одного класса, интерфейсы могут наследовать сразу от нескольких 
интерфейсов

```typescript
interface Named {
  name: string;
}

interface Aged {
  age: number;
}

interface Greetable extends Named, Aged {
  greet(phrase: string): void;
}
```


### Optional properties

Для обозначения свойства или метода который может опциональный достаточно просто поставить знак вопроса после названия 
свойства/метода

```typescript
interface Greetable {
  name: string;
  age?: number;
  greet(phrase: string): void;
}
```
