---
title: React. Redux-thunk. Routing
date: "2021-11-16"
description: Асинхронные экшены и роутинг
---

## Поток данных.

В целом в React-Redux поток данных несложен:
- Компонент через connect присоединяется через props к redux, получая actionCreator-ы для отправки в redux, и данные из хранилища.
- При выполнении диспетчеризации action меняется состояние хранилища.
- Новое состояние автоматически обновляет все подписанные через connect компоненты. В этом цикле не совсем ясно, куда воткнуть 
обращения к бэку, при условии, что:
  - Компонент должен остаться независимым и связанным с внешним миром только через connect,
  - Redux - хранилище синхронное, а получение информации с бэка - процесс многоэтапный и асинхронный.
  - Компоненты должны знать о состоянии процесса общения с бэком (состоянии промиса, как минимум).

В таком случае слой отправки-получения данных можно встроить в два места:
- В actionCreator, перед диспетчеризацией.
- В том или ином слушателе состояния redux (используя subscribe). Внутри хранилища создается специальная ветвь для хранения 
информации о желании компонентов получить что-то с back-end.

Первый подход более прост и распространен.

### actionCreator

В коде actionCreator должны быть реализованы асинхронные обращения. Если разобрать жизненный путь Promise, то обычный actionCreator 
будет представлять что-то вроде:

```javascript
//три состояния
const actionPending = () => ({ type: 'SET_STATUS', status: 'PENDING', payload: null, error: null })
const actionResolved = payload => ({ type: 'SET_STATUS', status: 'RESOLVED', payload, error: null })
const actionRejected = error => ({ type: 'SET_STATUS', status: 'REJECTED', payload: null, error })

//асинхронный многоэтапный экшен
const actionFetch   = () => {
    let promise = fetch("...")
    promise.then(
        data => store.dispatch(actionResolved(data)),
        error => store.dispatch(actionRejected(error))
    )

    return actionPending()
}
```

Таким образом синхронная функция **actionFetch** обращается куда надо на сервер, навешивает обработчики результатов промиса в then, 
и возвращает изначальное состояние **actionPending**. Когда промис поменяет свое состояние, это будет обработано redux через действия 
**actionResolved** и **actionRejected**.

### Редьюсер

Где-то в недрах редьюсеров:

```javascript
...   
    if (action.type === 'SET_STATUS'){
        return {status: action.status, payload: action.payload, error: action.error}
    }
...
```

Что в этом подходе неудобно, так это привязка к store.

Встречайте, redux-thunk

### Redux-thunk

*Redux-thunk* предоставляет возможность передавать действие-функцию вместо действия-объекта. Теперь **actionCreator** может 
возвращать функции, и **dispatch** их запустит. Сама же функция может реализовывать любой длительный асинхронный процесс, 
запуская **dispatch** для изменения состояния Redux.

```javascript
import thunk from 'redux-thunk';
import {createStore, combineReducers, applyMiddleware} from 'redux';

const store = createStore(msgStatusReducer, applyMiddleware(thunk)) //вторым параметром идет миддлвар
```

Теперь функция actionFetch выглядит немного по-другому:

```javascript
const actionFetch   = () => {
    return dispatch => { //возвращаем функцию. 
        let promise = fetch("...")
        dispatch(actionPending())
        promise.then(
            data => dispatch(actionResolved(data)),
            error => dispatch(actionRejected(error))
        )
    }
}
```

- actionCreator возвращает не объект, а функцию;
- Redux её запускает, передавая свой dispatch;
- функция инициирует асинхронный процесс и использует простые объектные actionCreator для оповещения хранилища;

async function тоже можно:
```javascript
function actionFetch(){
    return async function (dispatch){
        dispatch(actionPending())
        try {
            dispatch(actionResolved(await fetch(...)))
        }
        catch (e) {
            dispatch(actionRejected(e))
        }
    }
}
```

Подход с использованием async function предпочтителен, так как он позволяет линейно описать асинхронный процесс любой сложности. 
Подобным образом работает Redux-Saga. И если на простых примерах синхронный подход с then мало отличается от асинхронной функции; 
то при нескольких обращениях зависящих друг от друга, код асинхронной функции будет намного прозрачнее.

## SPA && ROUTING

### Предыстория

Со времен зарождения современного фронтенда были попытки сделать продвинутый фронт с многостраничным интерфейсом. Технически 
это не представляет особой сложности, однако нет особого смысла в многостраничном интерфейсе без ссылок на эти страницы. 
С этим же были проблемы - при любой попытке смены адресной строки браузер упорно переходил на другую страницу, т.е. обращался 
к серверу и сбрасывал JS, подгружая новые скрипты. Это если страница реально существовала на сервере. Обычно 404.

### # - ссылки

При переходе по ссылкам внутри страницы браузер, очевидно, страницу не перезагружает. Этим длительное время пользовались первые 
SPA типа gmail. На таких сервисах роутинг, аналогичный роутингу в express реализуется после символа # в адресе, таким образом 
с точки зрения браузера все эти "переходы" по ссылкам происходят внутри одной страницы, чем и пользуется JS, прослушивая события 
изменения адреса и перерисовывая содержимое страницы.

HTML5 позволяет отлавливать любое изменение адреса, а также управлять историей браузера. Таким образом можно полностью просимулировать 
работу страниц внутри SPA, как будто бы они загружаются с сервера.

#### Почему это называется SPA ?

Потому что физически это одна страница, которая просто показывает пользователю фейковые адреса и разный DOM.

**Как это устроено**

По образу и подобию роутинга в express, только адрес, который сопоставляется, с шаблоном приходит не из браузера на бэк, а 
обрабатывается в самом браузере.

**React**

С точки зрения React каждая страница - это компонент. Роутинг прописывается в декларативной форме в JSX.

Пример:
```javascript
import { Router, Route, Link } from 'react-router-dom';
import createHistory from "history/createBrowserHistory";

<Router history = {createHistory()}>
    <div>
        <Route path="/chat/:param1/:param2" component = {ChatPage} />
        <Route path="/" component = { MainPage } exact />
    </div>
</Router>
```

- Router обрамляет все маршруты и обеспечивает интеграцию с Browser History API
- Route принимает props:
  - path - шаблон адреса, как в express (обратите внимание на param1 и param2)
  - component - класс компонента.
  - exact - нужен чтобы маршрут срабатывал только при полном совпадении.

#### Компоненты

Принимают параметры шаблона адреса в props

```javascript
class ChatPage extends Component {
  render() {
    console.log(this.props)
    return (
        <div className="App">
            {this.props.match.params.param1} <br/>
            {this.props.match.params.param2}
        </div>
    )
  }
}
```

**Link**

Компонент Link предназначен для создания ссылок.

```javascript
<Link to='/'>...go to main</Link>
```

**Switch**

Компонент Switch выбирает первый подходящий маршрут, остальные игнорирует:

```javascript
<Router history = {createHistory()}>
    <div>
        <Switch>
            <Route path="/chat/" component = { ChatPage } />
            <Route path="/" component = { MainPage } exact />
            <Route component = { NotFound } />
        </Switch>
    </div>
</Router>
```

**Redirect**

Иногда нужно назначить перенаправление с одного адреса на другой. В таком случае поможет компонент Redirect:

```javascript
<Router history = {createHistory()}>
    <div>
        <Switch>
            <Route path="/chat/" component = {ChatPage} />
            <Route path="/" component = { MainPage } exact />
            <Redirect from="/main" to="/" />
            <Route component = { NotFound } />
        </Switch>
    </div>
</Router>
```

При посещении адреса /main будет автоматически происходить переход на / и работать компонент MainPage

#### Переход на другую страницу из кода

Зачастую после сохранения тех или иных форм нужно перейти на другую страницу, т. е. сменить адрес в адресной строке таким 
образом, чтобы React Router это обработал согласно конфигурации маршрутов. Это можно сделать с помощью рендеринга компонента 
Redirect без from. Для этого отправьте в state тот или иной маркер перехода чтобы спровоцировать перерисовку.

#### history.push

Этот способ проще - используйте history, который передан в Router (создается функцией createHistory)

```javascript
let history = createHistory()

///...

<Router history={history}>
...
</Router>

...

history.push('/') //переход на главную.
```

#### Вложенные маршруты

В относительно крупных проектах возникает надобность разделить разные части сайта на разные наборы маршрутов. 

Например:
- / Главная страница
- /dashboard Личный кабинет
- ...
- /admin Админка
  - /admin/
  - /admin/dashboard Админский кабинет
  
В такой структуре логично отделить маршруты общей и административной части сайта.

```javascript
//App
<Router history = {createHistory()}>
    <div>
        <Switch>
            <Route path="/" component = { MainPage } exact />
            <Route path="/dashboard" component = { DashboardPage } />
            <Route path="/admin" component = { AdminPage } />
            <Route component = { NotFound } />
        </Switch>
    </div>
</Router>
```

```javascript
class AdminPage extends Component {
    render(){
        let root = this.props.match.url
        return(
            <div>
                <Route path={`${root}/`} 
                        component={ AdminMainPage }
                    />
                <Route path={`${root}/dashboard`} 
                        component={ AdminDashboardPage }
                    />
            </div>
        )
    }
}
```

