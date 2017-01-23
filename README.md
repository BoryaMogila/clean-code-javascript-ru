Перевод книги Райана Макдермота <a href="https://github.com/ryanmcdermott/clean-code-javascript">clean-code-javascript</a>.
## Оглавление
  1. [Введение](#Введение)
  2. [Переменные](#Переменные)
  3. [Функции](#Функции)
  4. [Объекты и структуры данных](#Объекты-и-структуры-данных)
  5. [Классы](#Классы)
  6. [Тестирование](#Тестирование)
  7. [Асинхронность](#Асинхронность)
  8. [Обработка ошибок](#Обработка-ошибок)
  9. [Форматирование](#Форматирование)
  10. [Комментарии](#Комментарии)
  11. [Перевод](#Перевод)
  
## Введение
![Humorous image of software quality estimation as a count of how many expletives
you shout when reading code](http://www.osnews.com/images/comics/wtfm.jpg)

Инженерные принципы программного обеспечения, из книги Роберта С. Мартина [*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882), приспособленные для JavaScript.

Это не стайл гайд. Это руководство по написанию читаемого, переиспользуемого и поддерживаемого кода на javascript. Не каждый принцип здесь, должен строго соблюдаться. Это принципы и ничего больше, но они сформированы в течение многих лет коллективного опыта авторами clean code. 

Нашему ремеслу инженерии программного обеспечения чуть больше 50 лет и все ещё много чему учимся. Когда программная архитектура станет стара как сама архитектура, может быть, тогда мы будем иметь более жесткие правила, которым необходимо будет следовать. На данный момент, пусть эти принципы служат камнем преткновения, с помощью которого вы будете оценивать качество кода JavaScript, который пишете вы и ваша команда. 

Еще одна вещь: знание этих принципов не сделает вас  сразу лучшим разработчиком программного обеспечения, и если вы будете придерживаться этих принципов много лет не означает, что вы не будете делать ошибки. Каждый фрагмент кода начинается как черновик, как и кусок мокрой глины который приобретает свою окончатильную форму. Наконец, мы исправляем недостатки, когда рассматриваем код с своими коллегами. Не корите себя при первых набросках кода, которые нуждаются в улучшении. Улучшайте свой код вместо этого.

## Переменные

### Используйте значимые и произносимые имена переменных

**Плохо:**

```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD');
```

**Хорошо:**
```javascript
const currentDate = moment().format('YYYY/MM/DD');
```
**[⬆ венутся](#Оглавление)**

### Используйте один и тот же метод для того же типа переменной

**Плохо:**
```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Хрошо:**
```javascript
getUser();
```
**[⬆ венутся](#Оглавление)**

### Используйте именованные значения
Мы будем читать код чаще, чем мы когда-нибудь напишем. Важно писать читаемый код, который легко искать. Делайте ваши имена для поиска. Такие инструменты, как 
[buddy.js](https://github.com/danielstjules/buddy.js)
и 
[ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md)
могут помочь идентифицировать неназванные константы.

**Плохо:**
```javascript
// What the heck is 86400000 for?
setTimeout(blastOff, 86400000);

```

**Хорошо:**
```javascript
// Объявляйте их как глобальные переменные.
const MILLISECONDS_IN_A_DAY = 86400000;

setTimeout(blastOff, MILLISECONDS_IN_A_DAY);

```
**[⬆ венутся](#Оглавление)**

### Используйте объясняющие переменные

**Плохо:**
```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(address.match(cityZipCodeRegex)[1], address.match(cityZipCodeRegex)[2]);
```

**Хорошо:**
```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```
**[⬆ венутся](#Оглавление)**

### Используйте очеловеченные названия
Явное лучше, чем неявное.

**Плохо:**
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((l) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Что значит `l`?
  dispatch(l);
});
```

**Хорошо:**
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((location) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```
**[⬆ венутся](#Оглавление)**

### Не добавляйте ненужный контекст
Если ваше имя класса / объекта говорит вам что это, не повторяйте тоже при именовании его свойств и методов.

**Хорошо:**
```javascript
const Car = {
  carMake: 'Honda',
  carModel: 'Accord',
  carColor: 'Blue'
};

function paintCar(car) {
  car.carColor = 'Red';
}
```

**Плохо:**
```javascript
const Car = {
  make: 'Honda',
  model: 'Accord',
  color: 'Blue'
};

function paintCar(car) {
  car.color = 'Red';
}
```
**[⬆ венутся](#Оглавление)**

### Используйте условия по умолчанию вместо коротких замыканий или условных выражений

**Плохо:**
```javascript
function createMicrobrewery(name) {
  const breweryName = name || 'Hipster Brew Co.';
  // ...
}

```

**Хорошо:**
```javascript
function createMicrobrewery(breweryName = 'Hipster Brew Co.') {
  // ...
}

```
**[⬆ венутся](#Оглавление)**

## Функции

### Аргументы функции (идеально 2 или менее)
