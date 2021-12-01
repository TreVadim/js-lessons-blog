---
title: DOM манипуляции
date: "2021-12-01"
description: "Работа с DOM"
---

## Работа с DOM

С помощью JS мы можем манипулировать элементами, создавать их, менять, удалять и т.д.

### Создание элементов

Создать элемент можно воспользовавшись методом объекта document.createElement();

```javascript
let el = document.createElement('div');
```

Создать текстовый узел можно воспользовавшись методом объекта document.createTextNode();

```javascript
let textNode = document.createTextNode('hello');
```

Можно также создать элементы передавая в уже существующий элемент разметку

Вопрос только в производительности!!

```javascript
el.innerHTML = '<p>Hello World!</p>';
```

### Замена элементов

Замена одного элемента другим с помощью метода DOM replaceChild():

```javascript
// выбираем элемент который будет заменен
let el = document.querySelector('div');

// создаем новый элемент
let newEl = document.createElement('p');
newEl.innerHTML = '<b>Hello World!</b>';

// заменяем
el.parentNode.replaceChild(newEl, el);
```

### Удаление элемента

#### Удаление всех дочерних узлов элемента из DOM.

Используя свойство innerHTML элемента для удаления всех дочерних элементов и, таким образом, для очистки его содержимого

```javascript
let el = document.querySelector('div');
el.innerHTML = '';
```

Обработчики событий, связанные с любой частью содержимого элемента также уничтожаются

#### Удалить элемент из дерева DOM.

Кросс-браузерное безопасное удаление элемента из дерева DOM

```javascript
let el = document.querySelector('div');
el.parentNode.removeChild(el);
```

### Клонирование элемента

Создание глубокой копии элемента DOM.

```javascript
let el = document.querySelector('div');

let foo = el.cloneNode(true);
```

cloneNode(true) создает глубокую копию выбранного элемента DOM, включая атрибуты и дочерние элементы. 
Чтобы клонировать только узел и его атрибуты, передайте false в качестве аргумента cloneNode().

### Добавление элемента

Используя свойство innerHTML элемента можно получить или изменить его содержимое

```javascript
let el = document.querySelector('div');

// добавление контента в элемент
el.innerHTML += '<p>Hello World!</p>';

el.innerHTML = '<p>Hello World!</p>' + el.innerHTML;
```

*innerHTML* - это, по сути, содержимое элемента в виде строки. Однако будьте осторожны: изменение innerHTML уничтожит и 
перестроит все элементы-потомки контейнера. Обработчики событий, связанные с любым из уничтоженных элементов, теряются в процессе, и при необходимости их необходимо подключать заново.

Кроме того, можно использовать методы DOM для создания, вставки и перемещения элементов:

```javascript
let el = document.querySelector('div');

// создаем p элемент для в вставки
let newEl = document.createElement('p');

// добавляем текстовый узел для p
newEl.appendChild(document.createTextNode('Hello World!'));

// добавление p как новый дочерний элемент el
el.appendChild(newEl);

// тот же результат с insertBefore()
el.insertBefore(newEl, null);
```

### Получение

#### Получение текстового содержимого элемента

Получение текстового содержимого элемента, или установка текстового содержимого элемента.

```javascript
let el = document.querySelector('div');

text = el.textContent || el.innerText;

console.log(text);
```

#### Получение родителя элемента

```javascript
let el = document.querySelector('div');
let parent = el.parentNode;
```

#### Получение определенного родителя

```javascript
let el = document.querySelector('span');
console.log(el.closest('div'));
```

#### Получение дочернего элемента

```javascript
let firstChild = el.firstChild;
let lastChild = el.lastChild;
```

### Классы

#### Получение списка классов

```javascript
let el = document.querySelector("div");

el.classList;
```

#### Работа с классами

```javascript
let el = document.querySelector("div");

// Добавление
el.classList.add("test-class");

// Удаление
el.classList.remove("test-class");

// Замена класса
el.classList.replace("test-class", "new-class");

// Перебор классов
el.classList.forEach((item, i, arr) => {});

// Проверка наличия класса
el.classList.contains("new");
```

### Атрибуты

getAttribute, setAttribute, removeAttribute - изменяют атрибуты, такие как id, alt, title и другие.

```javascript
// получение атрибута title
let el = document.querySelector('img');
console.log(el.getAttribute('title'));

// установка атрибута
let el = document.querySelector('img');
el.setAttribute('alt', 'Hello World!');

// удаление атрибута
el.removeAttribute('alt');
```

### Стили

Элементы содержат объект style, обращаясь к ключам которого, можно добавлять, удалять, изменять стили

```javascript
let el = document.querySelector('div');

el.style.backgroundColor = 'green';
el.style.display = 'none';
el.style['border-radius'] = '5px';
```

### Материалы для прочтения 

- Document https://learn.javascript.ru/document
