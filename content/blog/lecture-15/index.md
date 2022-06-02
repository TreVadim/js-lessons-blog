---
title: React. JSX
date: "2021-12-17"
description: "JSX разметка"
---

## JSX

Фундаментально, *JSX* является синтаксическим сахаром для функции *React.createElement(component, props, ...children)*. 

JSX код:
```javascript
<MyButton color="blue" className="btn">
  	Click Me
</MyButton>
```

компилируется в:
```javascript
React.createElement(
    MyButton,
    {color: 'blue', className: 'btn'},
    'Click Me'
)
```

Также можно использовать самозакрывающую форму для тегов, у которых нет потомков. 

Например:
```javascript
<div className="sidebar" />
```

компилируется в:
```javascript
React.createElement(
    'div',
    {className: 'sidebar'},
    null
);
```

### Указание типа React-элемента

Начальная часть JSX тега определяет тип элемента React. Типы, определенные с Прописной буквы, указывают на то, что тег 
ссылается на компонент React. Эти теги в процессе компиляции ссылаются на именованную переменную, содержащую компонент 
React. Поэтому, обратите внимание, — эта переменная должна находится в области видимости. Например: Если вы используете 
выражение **JSX — \<Foo />**, то переменная Foo должна находится в области видимости.


### React должен находиться в области видимости

Т.к. JSX компилируется в вызовы функции React.createElement, библиотека React всегда должна находиться в области 
видимости вашего кода JSX. 

Например: обе строки import необходимы в данном коде, т.к. React и CustomButton не включены непосредственно в JavaScript:
```javascript
import React from 'react';
import CustomButton from './CustomButton';

export default () => {
    // return React.createElement(CustomButton, {color: 'red'}, null);
    return <CustomButton color="red" />;
}
```

### Использование записи через точку "." в JSX

На компонент React можно ссылаться используя нотацию через точку в JSX. Это удобно, если у вас есть модуль, который 
экспортирует несколько компонентов React. 

Например, если MyComponents.DatePicker — это компонент, то вы можете использовать эту нотацию непосредственно в JSX:
```javascript
import React from 'react';

const MyComponents = {
    DatePicker: (props) => {
        return <div>Imagine a {props.color} datepicker here.</div>;
    }
}

const BlueDatePicker = () => {
    return <MyComponents.DatePicker color="blue" />;
}
```

### Названия пользовательских компонентов должны начинаться с большой буквы

Когда название типа элемента начинается с маленькой буквы, он ссылается на встроенный компонент, такой как \<div> или 
\<span>, обуславливая передачу строк “div” или “span” в вызов React.createElement. Названия типов, которые начинаются с 
большой буквы, такие как \<MyComponent/> компонент, компилируется в React.createElement(MyComponent) и соответствует 
компоненту, определенному или импортированному в ваш JavaScript файл. Рекомендуется именовать компоненты с большой буквы. 
Если у вас есть компонент, названный с маленькой буквы, присвойте его переменной, названной с большой буквы, перед тем 
как использовать его в JSX. 

К примеру, этот код не будет работать как ожидается:
```javascript
import React from 'react';

// Неправильно! Это компонент и должен именоваться с Прописной буквы:
function hello(props) {
    // Правильно! Использование <div> верно, т.к. div это существующий HTML тег:
    return <div>Hello {props.toWhat}</div>;
}

function HelloWorld() {
    // Неправильно! React принимает <hello /> за HTML тег, т.к. он начинается со строчной буквы:
    return <hello toWhat="World" />;
}
```

Для исправления ошибки, мы переименуем hello в Hello и будем использовать \<Hello /> в JSX:
```javascript
import React from 'react';

// Правильно! Это компонент и именуется с Прописной буквы:
function Hello(props) {
    // Правильно! Использование <div> верно, т.к. div это существующий HTML тег:
    return <div>Hello {props.toWhat}</div>;
}

function HelloWorld() {
    // Правильно! React знает, что <Hello /> это компонент, т.к. он именован с Прописной буквы:
    return <Hello toWhat="World" />;
}
```

### Выбор типа во время выполнения

Нельзя использовать выражение как тип React-элемента в JSX. Если вы всё же хотите использовать выражение, чтобы указать 
тип React-элемента, сперва назначьте его переменной, названной с большой буквы. Часто это подходит, когда вам необходимо 
отрисовать различные компоненты, в зависимости от свойств prop:

```javascript
import React from 'react';
import { Image, Video } from './media';

const components = {
    image: Image,
    video: Video
};

function Media(props) {
    // Неправильно! JSX-тип не может являться выражением
    return <components[props.mediaType] url={props.url} />;
}
```

Для того, чтобы это исправить, мы должны предварительно присвоить тип переменной, названной с большой буквы:

```javascript
import React from 'react';
import { Image, Video } from './media';

const components = {
    image: Image,
    video: Video
};

function Media(props) {
    // Правильно! JSX-тип может являться переменной, названной с большой буквы
    const MediaObject = components[props.storyType];
    return <MediaObject url={props.url} />;
}
```

### Свойства props в JSX

Существует несколько способов указать свойства в JSX.

#### JavaScript выражения как свойства:

Вы можете передавать любые JavaScript-выражения как свойства, заключая их в {}. К примеру, в этом JSX:

```javascript
<MyComponent foo={1 + 2 + 3 + 4} />
```

Инструкции if или циклы for не являются выражениями в JavaScript, поэтому они не могут использоваться непосредственно в 
JSX. Поэтому их необходимо использовать только в окружающем коде. 

Например:
```javascript
function NumberDescriber(props) {
    let description;
    if (props.number % 2 == 0) {
        description = <strong>even</strong>;
    } else {
        description = <i>odd</i>;
    }

    return <div>{props.number} - это {description} число</div>;
}
```

#### Строковые литералы:

Вы можете передавать строковый литерал как свойство. Эти два JSX-выражения эквивалентны:

```javascript
<MyComponent message="Привет мир!" />

<MyComponent message={'Привет мир!'} />
```

### Установка свойств по умолчанию в true

Если вы не передаете значение в свойство, оно устанавливается по умолчанию в true. Следующие два JSX-выражения эквивалентны:

```javascript
<MyTextBox autocomplete />

<MyTextBox autocomplete={true} />
```

В большинстве случаев мы не рекомендуем использовать это, так как это можно спутать с объектным ES6 сокращением **{foo}**, 
который является сокращенной формой записи **{foo: foo}**, а не **{foo: true}**. Такое поведение существует просто для того, 
чтобы соответствовать поведению HTML.

### Spread – атрибуты

Если у вас есть объект, содержащий свойства, и вы хотите передать его в JSX, вы можете использовать ... в качестве 
"разворачивающего" spread оператора для передачи всех свойств, содержащихся в объекте. Эти два компонента эквивалентны:

```javascript
function SuccessMessage() {
    return <Message type="success" header="Поздравляем!" text="Вы успешно     зарегистрированы"/>;
}

function SuccessMessage() {
    const props = {type: 'success', header: 'Поздравляем!', text: 'Вы успешно зарегистрированы'};
    return <Message {...props} />;
}
```

Вы также можете выбрать определенные свойства, которые ваш компонент будет использовать, передавая все остальные свойства 
с помощью оператора ... .

В приведенном примере свойство *type* безопасно используется и не передается элементу \<button> в DOM. Все остальные 
свойства передаются через *...other* объект, делающий данный компонент очень гибким. Вы можете видеть, что он передает 
свойства onClick и children. Spread-оператор может быть полезен, когда вы строите контейнеры общего назначения. Тем не менее, 
данный оператор также может сделать ваш код и более грязным, делая простым передачу множества необязательных свойств в 
компоненты, которые о них совсем не заботятся. Также он позволяет передавать недопустимые HTML-атрибуты в DOM. Рекомендуется 
использовать данный синтаксис разумно.

```javascript
const Button = props => {
    const { type, ...other } = props;
    const className = type === "primary" ? "PrimaryButton" : "SecondaryButton";
    return <button className={className} {...other} />;
};
  
const App = () => {
    return (
        <div>
            <Button type="primary" onClick={() => console.log("Clicked!")}>
                Hello World!
            </Button>
        </div>
    );
};
```

### Потомки в JSX

В JSX-выражениях, которые содержат и открывающий, и закрывающий теги, содержание между этими тегами передается как специальное 
свойство: props.children. Существует несколько различных способов передать потомков:

#### Строковые литералы

Вы можете заключить строку между открывающим и закрывающим тегами, тогда свойство props.children будет равно этой строке. 
Это полезно для многих встроенных HTML-элементов. К примеру:

```javascript
<MyComponent>Привет, мир!</MyComponent>
```

Это валидный JSX, и свойство props.children в MyComponent будет просто строкой «Привет, мир!». HTML будет не экранирован, 
поэтому вы можете писать на JSX также, как если бы вы писали на обычном HTML:

```javascript
<div>Это валидный HTML & JSX одновременно.</div>
```

JSX удаляет пробелы в начале и конце строки. Он также удаляет пустые строки. Новая строка, прилегающая к тегу будет удалена. 
Новые строки, находящиеся по середине строковых литералов, сжимаются в единичный пробел. Все это отрисуется в то же самое:

```javascript
<div>Привет, мир!</div>

<div>
    Привет, мир!
</div>

<div>
    Привет,
    мир!
</div>

<div>

    Привет, мир!
</div>
```

### JSX-потомки

Вы можете предоставить больше JSX-элементов в качестве потомков. Это полезно для отображения вложенных компонентов:

```javascript
<MyContainer>
    <MyFirstComponent />
    <MySecondComponent />
</MyContainer>
```

Вы можете смешивать различные типы потомков, то есть можете использовать строковые литералы вместе с JSX-потомками. 
Это другой способ, в котором JSX такой же, как HTML, поэтому данный код является и валидным JSX, и валидным HTML:

```javascript
<div>
    Список:
    <ol>
        <li>Элемент 1</li>
        <li>Элемент 2</li>
        <li>Элемент 3</li>
    </ol>
</div>
```

### JavaScript выражения как потомки

Вы можете передавать любое JavaScript выражение как потомок, заключая его в {}. Например, эти выражения эквивалентны:

```javascript
<MyComponent>Привет!</MyComponent>

<MyComponent>{'Привет!'}</MyComponent>
```

Это часто бывает полезным для отрисовки списка JSX-выражений произвольной длины. Например, здесь отрисовывается HTML-список:

```javascript
function User(props) {
    return <li>{props.user.name}</li>;
}
    
function UserList() {
    const users = [{id: 1, name: 'Вася'}, {id: 2, name: 'Петя'}];
    return (
        <ul>
             {users.map((user) => <User key={user.id} user={user} />)}
        </ul>
    );
}
```

### Функции как потомки

Как правило JavaScript выражения, вставленные в JSX, будут приведены к строке, элементу React или списку этих вещей. 
Тем не менее, props.children работает точно также, как и любое другое свойство, так как в него можно передать любой вид данных, 
а не только тот вид, который React знает как отрисовать. Например, если у вас есть пользовательский компонент, вы могли 
бы передать функцию обратного вызова как props.children.

```javascript
// Вызывает коллбэк потомка, чтобы создать повторяемый компонент
function UserList(props) {
    return (
      <ul>
          {props.users.map((user) => props.children(user))}
      </ul>
    )
}

 function UserPage() {
    const users = [{id: 1, name: 'Вася'}, {id: 2, name: 'Петя'}];
    return (
        <UserList users={users}>
            {(user) => <li key={user.id}>Пользователь: {user.name}</li>}
        </UserList>
    );
}
```

Потомки, переданные в пользовательский компонент, могут быть чем угодно, до тех пор пока компонент трансформирует их во 
что-нибудь, что React может понимать перед отрисовкой. Это не типичный случай использования и представлен только для того, 
чтобы вы познали, на что способен JSX.

### Booleans, Null и Undefined игнорируются

false, null, undefined, и true – валидные потомки, но они не отрисовываются. Эти JSX-выражения будут отрисованы одинаково:

```javascript
<div />

<div></div>

<div>{false}</div>

<div>{null}</div>

<div>{undefined}</div>

<div>{true}</div>
```

Это может оказаться полезным, чтобы отрисовать элементы React по условию. Этот JSX отрисовывает \<Modal /> только если 
isModalShowed имеет значение true:

```javascript
<div>
     {isModalShowed && <Modal content={content}/>}
</div>
```

Один нюанс заключается в том, что “ложные” значения, такие как число 0, будут по-прежнему отрисовываться React. К примеру, 
данный код будет вести себя не так, как вы могли ожидать, так как будет отрисован 0, когда props.users является пустым массивом:

```javascript
<div>
    {props.users.length &&
        <UserList users={props.users} />
    }
</div>
```

Чтобы это исправить, убедитесь, что выражение перед && всегда является boolean:

```javascript
<div>
    {props.users.length > 0 &&
        <UserList users={props.users} />
    }
</div>
```

И напротив, если вам нужно значение, такое как false, true, null, или undefined, чтобы вывести его, то тогда вы сперва 
должны конвертировать его в строку:

```javascript
<div>
   Моя JavaScript переменная имеет значение:  {String(myVar)}.
</div>
```

