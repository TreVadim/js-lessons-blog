---
title: React. Работа с формами
date: "2020-03-26"
description: Рассмотрение работы с формами
---

## Работа с формами

Работа элементов HTML-форм в React немного отличается от работы других DOM-элементов, потому что элементы форм по своей 
природе обладают некоторым внутренним состоянием. К примеру, данная форма в нативном HTML принимает только имя:

```javascript
<form>
   <label>
      Name: <input type="text" name="name" />
   </label>
   <input type="submit" value="Submit" />
</form>
```

Эта форма имеет поведение HTML-формы по умолчанию: просмотр новой страницы, когда пользователь посылает форму. Если вам 
необходимо это поведение в React, оно работает как обычно. Но в большинстве случаев удобно иметь JavaScript-функцию, которая 
обрабатывает отправку формы и имеет доступ к данным, которые пользователь ввел в форму. Стандартным способом достижения этой 
цели является использование подхода «контролируемые компоненты».

### Контролируемые компоненты

В HTML-элементы форм, такие как \<input>, \<textarea> и \<select> как правило хранят свое собственное состояние и обновляют 
его на основании пользовательского ввода. В React модифицируемое состояние как правило является собственностью компонентов и 
обновляется только с помощью setState(). Мы можем скомбинировать эти обе особенности, делая состояние React “единственным 
источником достоверной информации (истины)”. В свою очередь React-компонент, который отрисовывает форму, также контролирует, 
что происходит на этой форме в ответ на последующий ввод пользователя. Элемент ввода формы (например, input), значение которого 
контролируется React, в этом случае называется «контролируемый компонент». В большинстве случаев рекомендуется использовать 
контролируемые компоненты для реализации форм. В контролируемом компоненте данные формы обрабатываются компонентом React.

### Основы работы с формами

Пусть у нас в this.state.value хранится текст 'hello':

```javascript
class App extends React.Component {
	constructor(props) {
		super(props);
		this.state = { 
		  value: 'hello'
		};
	}
}
```

Теперь в методе render сделаем инпут, и таже же сделаем так, чтобы в value инпута записалось значение из this.state.value:

```javascript
class App extends React.Component {
	constructor(props) {
		super(props);
		this.state = {
		  value: 'hello'
		};
	}

	render() {
		return <div>
			<input value={this.state.value} />
		</div>;
	}
}
```

Запустите этот код - на экране вы увидите инпут с текстом 'hello'. Однако, вас ждет сюрприз: вы не сможете поменять текст нашего 
инпута. Почему так? Потому что мы четко сказали, что в value инпута должно быть значение из this.state.value. Это значение 
не меняется - и значит value инпута тоже не будет меняться, даже если вы вручную что-то попытаетесь туда написать. Понятно, 
что такое поведение не очень удобно и нам нужно что-то с этим сделать. Что именно: нужно организовать двухстороннее связывание 
- this.state.value и value инпута должны зависеть друг от друга: при изменении одного должен меняться и другой. Первый шаг 
для этого следующий: нужно к нашему инпуту добавить событие onChange и привязать к нему какой-нибудь метод, назовем его, к 
примеру, handleChange.

```javascript
class App extends React.Component {
	constructor() {
		super();
		this.state = {
		  value: 'hello'
		};
	}

	//Срабатывает при любом изменении инпута:
	handleChange = () => {
		//тут какой-то код
	}

	render() {
		return <div>
			<input 
                value={this.state.value} 
                onChange={this.handleChange} 
			/>
		</div>;
	}
}
```

Как работает событие onChange? - оно срабатывает при попытке любого изменения инпута. Например, если мы пытаемся ввести в 
него какой-то текст, то onChange будет срабатывать при каждом вводе символа. И, даже если текст инпута не будет меняться из-за 
привязанного this.state.value, событие onChange будет срабатывать и каждый раз вызывать метод handleChange. Чтобы содержимое 
инпута могло изменяться, для этого в методе handleChange нужно в свойство this.state.value записывать содержимое атрибута 
value нашего инпута.

### Работа с textarea

Работа с текстареа осуществляется очень похожим на работу с инпутом образом - также добавляется атрибут value и событие onChange. 
Следующий код - выведет на экран textarea, в который можно будет вводить текст. Все изменения будут отображаться в абзаце над 
нашим textarea:

```javascript
class App extends React.Component {
	constructor(props) {
		super(props);
		this.state = {
		  value: 'hello'
		};
	}

	//Записываем value текстареа в this.state.value:
	handleChange = (event) => {
		this.setState({ value: event.target.value });
	}

	render() {
		return <div>
			<p>текст: {this.state.value}</p>
			<textarea 
			    value={this.state.value} 
			    onChange={this.handleChange} 
			/>
		</div>;
	}
}
```

Обратите внимание: React подход отличается подхода на чистом JavaScript. На чистом JS у текстареа нет атрибута value, кроме 
того, в чистом JS текстареа должен иметь закрывающий тег: \<textarea>\</textarea>, но в реакте мы пишем так: \<textarea /> - 
подобно работе с инпутом.

### Чекбоксы

Работа с чекбоксами также осуществляется по схожему принципу, только вместо атрибута value мы указываем атрибут checked. 
Если в этот атрибут передать true - то чекбокс будет отмечен, а если false - не будет отмечен:

```javascript
return <div>
	<input
		type="checkbox"
		checked={тут должно быть true или false}
	/>
</div>;
```

Конечно же, обычно в checked передается стейт, который может принимать только два значения: или true, или false. Так же, 
как и при работе с инпутами, если жестко задать значение атрибута checked - состояние чекбокса нельзя будет поменять. Для 
решения этой проблемы поступим так: будем по изменению чекбокса менять this.state.checked на противоположное ему значение, 
то есть на !this.state.checked.

```javascript
class App extends React.Component {
	constructor(props) {
		super(props);
		this.state = {
		  checked: true
		};
	}

	//Меняем this.state.checked на противоположный:
	handleCheckboxChange = (event) => {
		this.setState({ checked: !this.state.checked });
	}

	render() {
		return <div>
			<input
				type="checkbox"
				checked={this.state.checked}
				onChange={this.handleCheckboxChange}
			/>
		</div>;
	}
}
```

### Селекты

Давайте теперь займемся выпадающими списками select. Работа с ними также практически не отличается от работы с инпутами и 
чекбоксами.

```javascript
class App extends React.Component {
	constructor(props) {
		super(props);
		this.state = {
		  value: 'javascript'
		};
	}

	//Изменяем this.state.value при изменении селекта:
	handleSelectChange = (event) => {
		this.setState({ value: event.target.value });
	}

	render() {
		return <div>
			<p>Ваш выбор: {this.state.value}</p>
			<select
				value={this.state.value}
				onChange={this.handleSelectChange}
			>
				<option>html</option>
				<option>css</option>
				<option>javascript</option>
			</select>
		</div>;
	}
}
```

Изначально выбран будет пункт списка со значением 'javascript' и этот выбор будет отражаться в нашем абзаце. Но если выбор 
поменять - в абзаце он тоже поменяется. Давайте теперь тегам option дадим атрибут value, как это обычно и делается:

```javascript
<select>
	<option value="0">Язык HTML</option>
	<option value="1">Язык CSS</option>
	<option value="2">Язык JavaScript</option>
</select>
```

На самом деле без атрибута value, как это было сделано выше - плохо. Почему плохо: ведь текст option может меняться и это 
повлияет на работу нашего скрипта. К примеру, вместо текста 'javascript' мы сделаем текст 'Язык JavaScript' и теперь строчка 
this.state = {value: 'javascript'} просто не будет работать. А вот если мы привяжемся к value и напишем this.state = {value: 2} 
- все будет работать независимо от того, что написано в самом тексте тега option.

```javascript
class App extends React.Component {
	constructor(props) {
		super(props);
		this.state = {value: 0};
	}

	//Изменяем this.state.value при изменении селекта:
	handleSelectChange = (event) => {
		this.setState({value: event.target.value});
	}

	render() {
		return <div>
			<p>Ваш выбор: {this.state.value}</p>
			<select
				value={this.state.value}
				onChange={this.handleSelectChange}
			>
				<option value="0">Язык HTML</option>
				<option value="1">Язык CSS</option>
				<option value="2">Язык JavaScript</option>
			</select>
		</div>;
	}
}
```

Итак, если вы запустите этот код, то увидите проблему - строка \<p>Ваш выбор: {this.state.value}\</p> будет выводить value 
нашего option, а никак не значение. Для того, чтобы поправить эту проблему, вначале сделаем следующее: не будем создавать 
теги option вручную, а сделаем массив this.state.langs и из этого массива и сформируем наш селект, у нас по-прежнему есть 
строка this.state.value, которая выводит value option, а не его значение:

```javascript
<p>Ваш выбор: {this.state.value}</p>
```

Давайте поправим ее так, чтобы она подтягивала значение из массива this.state.langs по его ключу this.state.value:

```javascript
<p>Ваш выбор: {this.state.langs[this.state.value]}</p>
```

### Радиокнопки

Работа с radio несколько отличается, к примеру, от тех же чекбоксов. Проблема в том, что у нескольких радио будет один и 
тот же стейт, но разные value. Поэтому работа происходит следующем образом: в атрибут value записывают значение радио, а в 
атрибут checked - специальное условие, которое проверяет, равен ли this.state.option определенному значению. Если равен - 
радиокнопка станет отмеченной, а если не равен - будет не отмеченной. Пример: пусть this.state.option - это стейт, в котором 
будет хранится значение радио. Сделаем 2 радиокнопки и дадим одной value="option1", а второй - value="option2". Тогда для 
проверки того, будет ли первая радиокнопка отмечена, ей в атрибут checked запишем следующее: checked={this.state.option == 
'option1'}. Получится, что если в this.state.option лежит option1 первая радиокнопка будет отмеченной, а если не лежит - то 
не будет. Аналогичную строку запишем и для второй радиокнопки - checked={this.state.option == 'option2'}, только здесь мы 
сравниванием this.state.option уже с option2. Для каждой радио также напишем атрибут onChange и прикрепим к нему метод 
handleRadioChange. Ну, здесь все как обычно - так же, как для инпутов и чекбоксов. Давайте соберем все вместе и запустим:

```javascript
class App extends React.Component {
	constructor(props) {
		super(props);
		this.state = {option: 'option1'};
	}

	//Изменяет this.state.option при изменении радио:
	handleRadioChange = (event) => {
		this.setState({option: event.target.value});
	}

	render() {
		return <div>
			<p>Ваш выбор: {this.state.option}</p>
            <input 
                name="lang" 
                type="radio" 
                value="option1" 
                checked={this.state.option == 'option1'}
				onChange={this.handleRadioChange}
			/>
			<input 
                name="lang" 
                type="radio" 
                value="option2" 
                checked={this.state.option == 'option2'}
				onChange={this.handleRadioChange}
			/>
		</div>;
	}
}
```

### Значение по умолчанию

Иногда бывает следующая ситуация: вы хотите сделать так, чтобы по умолчанию в инпуте уже было какое-то значение. При этом 
вы хотите, чтобы из стейта было взято только начальное значение инпута, а сам инпут не был бы привязан к этому стейту. 
Способ сделать это есть: нужно воспользоваться атрибутом defaultValue:

```javascript
class App extends React.Component {
	constructor(props) {
		super(props);
		this.state = {value: 'hello'};
	}

	render() {
		return(
            <div>
                <input defaultValue={this.state.value} />
            </div>
		);
	}
}
```

Запустите этот код - и вы увидите на экране инпут со значением 'hello'. Но это значение можно будет сменить, при этом this.state.value 
не поменяется - двухсторонней связи нет.

Для чекбоксов существует аналогичный атрибут defaultChecked, с помощью которого можно задать начальное состояние (отмечен или нет):

```javascript
class App extends React.Component {
	constructor(props) {
		super(props);
		this.state = {checked: true};
	}

	render() {
		return <div>
			<input
				type="checkbox"
				defaultChecked={this.state.checked}
			/>
		</div>;
	}
}
```