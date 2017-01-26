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
  
## **Введение**
![Humorous image of software quality estimation as a count of how many expletives
you shout when reading code](http://www.osnews.com/images/comics/wtfm.jpg)

Инженерные принципы программного обеспечения, из книги Роберта С. Мартина [*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882), приспособленные для JavaScript.

Это не стайл гайд. Это руководство по написанию читаемого, переиспользуемого и поддерживаемого кода на javascript. Не каждый принцип здесь, должен строго соблюдаться. Это принципы и ничего больше, но они сформированы в течение многих лет коллективного опыта авторами clean code. 

Нашему ремеслу инженерии программного обеспечения чуть больше 50 лет и все ещё много чему учимся. Когда программная архитектура станет стара как сама архитектура, может быть, тогда мы будем иметь более жесткие правила, которым необходимо будет следовать. На данный момент, пусть эти принципы служат камнем преткновения, с помощью которого вы будете оценивать качество кода JavaScript, который пишете вы и ваша команда. 

Еще одна вещь: знание этих принципов не сделает вас  сразу лучшим разработчиком программного обеспечения, и если вы будете придерживаться этих принципов много лет не означает, что вы не будете делать ошибки. Каждый фрагмент кода начинается как черновик, как и кусок мокрой глины который приобретает свою окончатильную форму. Наконец, мы исправляем недостатки, когда рассматриваем код со своими коллегами. Не корите себя при первых набросках кода, которые нуждаются в улучшении. Улучшайте свой код вместо этого.

## **Переменные**

### Используйте значимые и произносимые имена переменных

**Плохо:**

```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD');
```

**Хорошо:**
```javascript
const currentDate = moment().format('YYYY/MM/DD');
```
**[⬆ вернуться](#Оглавление)**

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
**[⬆ вернуться](#Оглавление)**

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
**[⬆ вернуться](#Оглавление)**

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
**[⬆ вернуться](#Оглавление)**

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
**[⬆ вернуться](#Оглавление)**

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
**[⬆ вернуться](#Оглавление)**

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
**[⬆ вернуться](#Оглавление)**

## **Функции**

### Аргументы функции (идеально 2 или менее)
Ограничение количества параметров функции невероятно важно, поскольку оно упрощает тестирование функции. Наличие более чем трёх аргументов приводит к комбинаторному взрыву, когда вам приходится перебирать массу различных случаев с каждым отдельным аргументом. 

Идеальная ситуация — отсутствие аргументов. Один или два аргумента — хорошо, а трех уже следует избегать. 

Большее количество аргументов необходимо консолидировать. Как правило, если передается более двух аргументов, ваша функция пытается сделать слишком многое. В тех случаях, когда это все же не так, лучше использовать объект в качестве аргумента. Поскольку JavaScript позволяет создавать объекты на лету, без специального описания классов, их вполне можно применять, когда требуется передать множество аргументов.

Для того, чтобы сделать свойства функции очевидными используйте синтаксис ES6 деструкции. Это имеет несколько преимуществ:
1. Когда просматривают обьявление функции, то сразу понятно, какие свойства используются.
2. Деструкция также клонирует указанные простые значения из аргумента функции. Это может помочь предотвратить сайд эффекты. Заметка: Объекты и массивы  НЕ КЛОНИРУЮТСЯ.
3. Линтеры могут предупредить вас о неиспользуемых свойствах, что было бы невозможно
без деструктуризации.

**Плохо:**
```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}
```

**Хорошо:**
```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
});
```
**[⬆ вернуться](#Оглавление)**

### Функция должна решать одну задачу
Это, безусловно, самое важное правило в разработке программного обеспечения. Когда функции решают более одной задачи, их труднее сочетать, тестировать и понимать. Как только вы сможете свести каждую функцию к выполнению только одного действия, их станет значительно проще рефакторить, а ваш код станет гораздо более читаемым. Даже если приведенное правило будет единственным вынесенным вами из этого руководства, вы все равно будете круче многих разработчиков. 

**Плохо:**
```javascript
function emailClients(clients) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Хорошо:**
```javascript
function emailClients(clients) {
  clients
    .filter(isClientActive)
    .forEach(email);
}

function isClientActive(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```
**[⬆ вернуться](#Оглавление)**

### Названия функций должны описывать их назначение

**Плохо:**
```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// It's hard to to tell from the function name what is added
addToDate(date, 1);
```

**Хорошо:**
```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```
**[⬆ вернуться](#Оглавление)**

### Функции должны представлять только один уровень абстракции
Если в функции представлено более одного уровня абстракции, то она, как правило, делает слишком многое. Разделение таких функций приведет к возможности повторного использования и облегчению тестирования.

**Плохо:**
```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach((token) => {
    // правило...
  });

  ast.forEach((node) => {
    // парсинг...
  });
}
```

**Хорошо:**
```javascript
function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      tokens.push( /* ... */ );
    });
  });

  return tokens;
}

function lexer(tokens) {
  const ast = [];
  tokens.forEach((token) => {
    ast.push( /* ... */ );
  });

  return ast;
}

function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const ast = lexer(tokens);
  ast.forEach((node) => {
    // парсинг...
  });
}
```
**[⬆ вернуться](#Оглавление)**

### Избавляйтесь от дублированного кода
Изо всех сил старайтесь избегать дублированного кода. Дублированный код вреден тем, что подразумевает наличие более чем одного места, в которое придется вносить правки, если логика действий изменится.

Представьте, что управляете рестораном и ведете учет всех продуктов — помидоров, лука, чеснока, специй и т.д. Если их учет ведется в разных списках, то подача любого блюда с помидорами потребует внесения изменений в каждый список. Если же список только один, то и правка будет всего одна! 

Зачастую дублированный код возникает в тех случаях, когда требуется реализовать два или более незначительно различающихся действия, которые в целом очень схожи, но их различия вынуждают вас завести две или более функции, делающих практически одно и то же. В этом случае избавление от дублированного кода будет означать создание абстракции, которая сможет представить все различия в виде одной функции, класса или модуля. 

Создание правильной абстракции — вопрос невероятной важности, и именно поэтому вы должны следовать принципам SOLID. Плохие абстракции могут оказаться хуже дублированного кода, так что будьте осторожны!

Подводя итог: если можете обернуть код хорошей абстракцией — так и сделайте! Не дублируйте код, иначе вам придется вносить множество правок на каждое небольшое изменение.

**Плохо:**
```javascript
function showDeveloperList(developers) {
  developers.forEach((developer) => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach((manager) => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Хорошо:**
```javascript
function showList(employees) {
  employees.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    let portfolio = employee.getGithubLink();

    if (employee.type === 'manager') {
      portfolio = employee.getMBAProjects();
    }

    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```
**[⬆ вернуться](#Оглавление)**

### Устанавливайте объекты по умолчанию с помощию Object.assign

**Плохо:**
```javascript
const menuConfig = {
  title: null,
  body: 'Bar',
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || 'Foo';
  config.body = config.body || 'Bar';
  config.buttonText = config.buttonText || 'Baz';
  config.cancellable = config.cancellable === undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Хорошо:**
```javascript
const menuConfig = {
  title: 'Order',
  // User did not include 'body' key
  buttonText: 'Send',
  cancellable: true
};

function createMenu(config) {
  config = Object.assign({
    title: 'Foo',
    body: 'Bar',
    buttonText: 'Baz',
    cancellable: true
  }, config);

  // теперь config = {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```
**[⬆ вернуться](#Оглавление)**

### Не используйте флаги в качестве параметров функции
Флаги говорят пользователю, что функция совершает более одного действия. Функция должна решать одну задачу. Разделяйте функции, если они исполняют различные варианты кода на основе логического значения.

**Плохо:**
```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Хорошо:**
```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```
**[⬆ вернуться](#Оглавление)**

### Избегайте побочных эффектов (Часть 1)
Функция производит побочный эффект, если она совершает какое-либо действие помимо получения значения и возврата другого значения или значений. Побочный эффект может быть записью в файл, изменением каких-то глобальных переменных или случайным переводом всех ваших денег неизвестным лицам. 

Впрочем, побочные эффекты в программе необходимы. Пусть, как и в предыдущем примере, вам требуется запись в файл. Опишите то, что вы хотите сделать, строго в одном месте. 

Не создавайте несколько функций и классов, которые пишут что-то в конкретный файл. Создайте один сервис, который всем этим занимается. Один и только один.

Суть в том, чтобы избегать распространенных ошибок, таких как, например, передача состояния между объектами без какой-либо структуры, с помощью изменяемых данных, которые может перезаписывать кто угодно, в обход централизованного места применения побочных эффектов.
 
Если научитесь так делать, вы станете счастливее, чем подавляющее большинство других программистов.

**Плохо:**
```javascript
// Глобальная переменная, на которую ссылается последующая функция.
// Если бы у нас была еще одна функция, которая бы работала с именем name как со строкой,
// то обнаружив массив, он непременно бы поломалась
let name = 'Ryan McDermott';

function splitIntoFirstAndLastName() {
  name = name.split(' ');
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Хорошо:**
```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(' ');
}

const name = 'Ryan McDermott';
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```
**[⬆ вернуться](#Оглавление)**

### Избегайте побочных эффектов (Часть 2)
В JavaScript примитивы передаются по значению, а объекты и массивы передаются по
ссылке. В случае объектов и массивов, если наша функция вносит изменения
в корзину (массив), например, путем добавления элемента в массив,
то любая другая функция, которая использует эту корзину (массив) будет зависеть от этого
добавления. Это может быть и хорошо и плохо в разных случаях. Давайте представим себе плохую ситуация:

Пользователь нажимает на кнопку "Покупка", которая вызывает функцию `purchase`, что отправляет данные из корзины (массив) на сервер. В случае плохого подключения к сети `функция purchase` должена отправить повторный запрос. Теперь, что, если в то же время пользователь случайно нажимает кнопку "Добавить в корзину", но пока не хочет покупать товар?
Если это произойдет, и начинается запрос сети, то функция `purchase`
пошлет случайно добавленный элемент, поскольку он имеет ссылку на предыдущую корзину (массив), модифицированую функцией `addItemToCart`. Отличное решение было бы для `addItemToCart` всегда клонировать корзину, отредактировать и вернуть клон. Это гарантирует, что никакие другие функции, которые зависят от корзины не будут зависеть от каких-либо изменений.

Два предостережения по-поводу такого подхода:
1. Возможны случаи, когда вы на самом деле хотите изменить объект по ссылке, но такие случаи крайне редки. Большинство функций могут быть объявлены без сайд эффектов!
2. Клонирование больших объектов может быть очень нагрузочным и влиять на производительность. К счастью, это не является большой проблемой на практике, потому что есть [отличные библиотеки](https://facebook.github.io/immutable-js/), которые позволяют клонировать объекты с меньшей нагрузкой на память в отличии от клонирования вручную.

**Плохо:**
```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**Хороше:**
```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date : Date.now() }];
};
```
**[⬆ вернуться](#Оглавление)**

### Не переопределяйте глобальные функции
Загрязнение глобальных переменных — плохая практика в JavaScript, так как может породить конфликты с другой библиотекой, и пользователь вашего API не увидит ошибок, пока не получит исключение в продакшене. Давайте рассмотрим пример: что делать, если вы хотите расширить стандартный функционал Array из JavaScript, добавив метод diff, который бы вычислял различие между двумя массивами? Вы должны были бы записать новую функцию в Array.prototype, но тогда она может войти в конфликт с другой библиотекой, которая пыталась сделать то же самое. А если другая библиотека использовала метод diff, чтобы найти разницу между первым и последним элементами массива? Именно поэтому гораздо лучше использовать классы ES2015/ES6 и просто унаследовать нашу реализацию от класса Array.

**Плохо:**
```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Хорошо:**
```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```
**[⬆ вернуться](#Оглавление)**

### Отдавайте предпочтение фунциональному программированию над императивным
JavaScript не настолько функциональный язык, как Haskell, но определенной доли функциональности он не лишен. Функциональные языки чище и их проще тестировать. Применяйте функциональный стиль программирования при возможности.

**Плохо:**
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Хорошо:**
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

const INITIAL_VALUE = 0;

const totalOutput = programmerOutput
  .map((programmer) => programmer.linesOfCode)
  .reduce((acc, linesOfCode) => acc + linesOfCode, INITIAL_VALUE);
```
**[⬆ вернуться](#Оглавление)**

### Инкапсулируйте условия

**Плохо:**
```javascript
if (fsm.state === 'fetching' && isEmpty(listNode)) {
  // ...
}
```

**Хорошо:**
```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === 'fetching' && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```
**[⬆ вернуться](#Оглавление)**

### Избегайте негативных условий

**Плохо:**
```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Хорошо:**
```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```
**[⬆ вернуться](#Оглавление)**

### Избегайте условных конструкций
Такая задача кажется невозможной. Услышав подобное, большинство людей говорят: "Как я должен делать что-либо без выражения if?". Ответ заключается в том, что во многих случаях для достижения тех же целей можно использовать полиморфизм. Второй вопрос, как правило, звучит так: "Хорошо, замечательно, но почему я должен их избегать?". Ответ — предыдущая концепция чистого кода, которую мы узнали: функция должна выполнять только одну задачу. Если у вас есть классы и функции, содержащие конструкцию 'if', вы словно говорите своему пользователю, что ваша функция выполняет больше одной задачи. Помните: одна функция — одна задача.

**Плохо:**
```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case '777':
        return this.getMaxAltitude() - this.getPassengerCount();
      case 'Air Force One':
        return this.getMaxAltitude();
      case 'Cessna':
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Хорошо:**
```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```
**[⬆ вернуться](#Оглавление)**

### Избегайте проверки типов (часть 1)
JavaScript является нетипизированным языком, а это значит, что ваши функции могут принимать аргументы любого типа. Порой вы обжигались этой свободой, что побуждало вас производить проверку типов в ваших функциях. Есть множество способов ее избежать. В первую очередь стоит подумать над согласованным API.

**Плохо:**
```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.peddle(this.currentLocation, new Location('texas'));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location('texas'));
  }
}
```

**Хорошо:**
```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location('texas'));
}
```
**[⬆ вернуться](#Оглавление)**

### Избегайте проверки типов (часть 2)
Если вы работаете с базовыми примитивами, такими как строки, целые числа и массивы, и не можете использовать полиморфизм, хотя все еще чувствуете необходимость в проверках типа, вам стоит рассмотреть возможность применения TypeScript. Это отличная альтернатива обычному JavaScript, предоставляющая возможность статической типизации поверх стандартного синтаксиса JavaScript. Проблема с ручной проверкой типов в обычном JavaScript в том, что иллюзия безопасности, которую она создает, никак не компенсируется потерей читабельности из-за многословности кода. Держите ваш код в чистоте, пишите хорошие тесты и делайте эффективные ревизии кода. Или делайте все то же самое, но с помощью TypeScript (который, как я уже сказал, является прекрасной альтернативой!). 

**Плохо:**
```javascript
function combine(val1, val2) {
  if (typeof val1 === 'number' && typeof val2 === 'number' ||
      typeof val1 === 'string' && typeof val2 === 'string') {
    return val1 + val2;
  }

  throw new Error('Must be of type String or Number');
}
```

**Хорошо:**
```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```
**[⬆ вернуться](#Оглавление)**

### Не оптимизируйте сверх меры
Современные браузеры производят множество оптимизаций под капотом во время исполнения кода. Оптимизируя код вручную, вы, зачастую, просто тратите свое время. <a href="https://github.com/petkaantonov/bluebird/wiki/Optimization-killers">Есть прекрасные ресурсы</a> с описанием ситуаций, когда оптимизация действительно хромает. Поглядывайте на них в свободное время, пока эти проблемы не будут исправлены, если вообще будут, конечно.

**Плохо:**
```javascript

// On old browsers, each iteration with uncached `list.length` would be costly
// because of `list.length` recomputation. In modern browsers, this is optimized.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Хорошо:**
```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```
**[⬆ вернуться](#Оглавление)**

### Удаляйте мертвый код
Мертвый код так же плох, как повторяющийся код. Нет никаких причин, чтобы держать его в репозитории. Если код не вызывается, избавьтесь от него! 

Он по-прежнему будет в системе контроля версий, если когда-нибудь он все-таки вам понадобится.

**Плохо:**
```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');

```

**Хорошо:**
```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');
```
**[⬆ вернуться](#Оглавление)**

## Объекты и структуры данных
### Используйте геттеры и сеттеры
В javascript отсутствуют ключевые слова private и public, что усложняет реализацию классов. Лучше использовать геттеры и сеттеры для доступа к свойствам объекта, чем напрямую к ним обращаться. Вы спросите «Зачем?». Вот несколько причин:
* Если вы хотите реализовать больше, чем просто доступ к свойству, вам нужно поменять реализацию в одном месте, а не по всему коду.
* Валидацию легко реализовать на уровне реализации сеттера
* Инкапсуляция внутреннего состояния объекта
* Легко добавить логирование и обработку ошибок на уровне геттеров и сеттеров
* Наследуя этот класс, вы можете переопределить функциональность по умолчанию
* Вы можете лениво подгружать свойства вашего объекта, например, с сервера.

**Плохо:**
```javascript
class BankAccount {
  constructor() {
    this.balance = 1000;
  }
}

const bankAccount = new BankAccount();

// Покупаем, например, обувь...
bankAccount.balance -= 100;
```

**Хорохо:**
```javascript
class BankAccount {
  constructor(balance = 1000) {
    this._balance = balance;
  }

  set balance(amount) {
    if (this.verifyIfAmountCanBeSetted(amount)) {
      this._balance = amount;
    }
  }

  get balance() {
    return this._balance;
  }

  verifyIfAmountCanBeSetted(val) {
    // ...
  }
}

const bankAccount = new BankAccount();

// Покупаем, например, обувь...
bankAccount.balance -= shoesPrice;

// получаем баланс
let balance = bankAccount.balance;

```
**[⬆ вернуться](#Оглавление)**

### Реализуйте приватные свойства ваших объектов
Это возможно с помощью замыканий.

**Плохо:**
```javascript

const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Хорошо:**
```javascript
const Employee = function (name) {
  this.getName = function getName() {
    return name;
  };
};

const employee = new Employee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```
**[⬆ вернуться](#Оглавление)**

## **Классы**
### Принцип единственной ответственности (SRP)
Как написано в clean code, «Должна быть лишь одна причина для изменения класса» (There should never be more than one reason for a class to change). Заманчиво всё засунуть в один класс, как в дорожный чемодан. Проблема в том, что ваш класс не будет концптуально связан, и вы будете часто измененять его на каждый чих. Очень важно минимизировать изменения в классе. Когда вы вносите изменения в класс с огромным функционалом, тяжело отследить последствия ваших измений.

**Плохо:**
```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Хорошо:**
```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}


class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```
**[⬆ вернуться](#Оглавление)**

### Принцип открытости/закрытости (OCP)
Как заявил Бертран Мейер, «сущности (классы, модули, функции и т.д.) должны быть открыты для расширения, но закрыты для модификации» (software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification). Что это значит? Это значит что вы должны давать возможность расширить функциональность сущности не изменяя существующий код.

**Плохо:**
```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'ajaxAdapter';
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'nodeAdapter';
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === 'ajaxAdapter') {
      return makeAjaxCall(url).then((response) => {
        // transform response and return
      });
    } else if (this.adapter.name === 'httpNodeAdapter') {
      return makeHttpCall(url).then((response) => {
        // transform response and return
      });
    }
  }
}

function makeAjaxCall(url) {
  // request and return promise
}

function makeHttpCall(url) {
  // request and return promise
}
```

**Хорошо:**
```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'ajaxAdapter';
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'nodeAdapter';
  }

  request(url) {
    // request and return promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then((response) => {
      // transform response and return
    });
  }
}
```
**[⬆ вернуться](#Оглавление)**

### Принцип подстановки Барбары Лисков
Это страшный термин для очень простой концепции. 

Определение:

 «Пусть q(x) является свойством верным относительно объектов x некоторого типа T. Тогда q(y) также должно быть верным для объектов y типа S, где S является подтипом типа T.»  [Wikipedia](https://ru.wikipedia.org/wiki/%D0%9F%D1%80%D0%B8%D0%BD%D1%86%D0%B8%D0%BF_%D0%BF%D0%BE%D0%B4%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B8_%D0%91%D0%B0%D1%80%D0%B1%D0%B0%D1%80%D1%8B_%D0%9B%D0%B8%D1%81%D0%BA%D0%BE%D0%B2) Определение ещё хуже, чем название. 

Суть заключается в том, что если у вас есть родительский и дочерний классы, то они могут взаимозаменятся без ошибок. Это по-прежнему может сбивать с толку, так что давайте посмотрим на классический пример площади прямоугольника. Математически квадрат это прямоугольник, но если вы решите эту задачу с помощию наследования, то у вас будут проблемы. Более детально про принцип можно почитать [здесь](https://habrahabr.ru/post/83269/").

**Плохо:**
```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach((rectangle) => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // BAD: Will return 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Хорошо:**
```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor() {
    super();
    this.width = 0;
    this.height = 0;
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor() {
    super();
    this.length = 0;
  }

  setLength(length) {
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach((shape) => {
    switch (shape.constructor.name) {
      case 'Square':
        shape.setLength(5);
        break;
      case 'Rectangle':
        shape.setWidth(4);
        shape.setHeight(5);
    }

    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(), new Rectangle(), new Square()];
renderLargeShapes(shapes);
```
**[⬆ вернуться](#Оглавление)**

### Принцип разделения интерфейса (ISP)
В javascript отсутсвуют интенфейсы, так что этот принцип не получится использовать в полной мере. Тем не менее важно его использовать, даже при отсутствии системы типов javascript. 

ISP утверждает, что «Пользователи не должны зависеть от классов, которые они не используют» (Clients should not be forced to depend upon interfaces that they do not use). Интерфейсы это условные соглашения в JavaScript из-за неявной типизации. Хорошим примером в javascript могут быть классы с большыми конфигами. Не заставляйте пользователей вашего класса вводить кучу конфигов. Они, как правило, не будут использовать их все. У вас не будет "жирного интерфейса", если вы их сделаете опциональными.

**Плохо:**
```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  animationModule() {} // Чаще вам не нужна анимация при движении.
  // ...
});

```

**Хорошо:**
```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  options: {
    animationModule() {}
  }
});
```
**[⬆ вернуться](#Оглавление)**

### Принцип инверсии зависимости (DIP)
Этот принцип гласит две важные вещи: 

1. Модули высшего уровня не должны зависеть от модулей низшего уровня. Оба должны зависеть от абстракций.
2. В абстракциях не должно быть деталей. Детали должны быть в дочерних классах.

Сначала трудно понять этот принцип. Но если вы работали с Angular.js, вы видели реализацию этого принципа в виде Dependency Injection (DI). Несмотря на то, что они не являются идентичными понятиями, DIP даёт возможность отграничить модули высокого уровня от деталей модулей низкого уровня и установки их. Он может сделать это через DI. Этот принцип уменьшает связь между модулями. Если ваши модули тесно связаны, их тяжело рефакторить. 

Абстракции и есть неявными соглашениями, которые представляют интерфейсы в JavaScript. То есть методы и свойства, что объект/класс предоставляет другому объекту/классу. В приведенном ниже примере каждый экземпляр класса InventoryTracker будет иметь метод requestItems.

**Плохо:**
```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // Плохо то, что мы создали зависимость от конкретной реализации запроса.
    // теперь наш метод requestItems не абстрактный и зависит от этой реализации
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(['apples', 'bananas']);
inventoryTracker.requestItems();
```

**Хорошо:**
```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ['WS'];
  }

  requestItem(item) {
    // ...
  }
}

// Сформировав зависимости извне и их подмешиванию, ми можем легко
// заменить наш модуль запросов на другой, который использует веб сокеты
const inventoryTracker = new InventoryTracker(['apples', 'bananas'], new InventoryRequesterV2());
inventoryTracker.requestItems();
```
**[⬆ вернуться](#Оглавление)**

### отдавайте предпочтение классам (ES2015 / ES6) над простыми функциями (ES5)
C помощью классических (ES5) классов тяжело реализовать читаемые наследование, конструкцию и определение методов. Если вам нужно наследование, не задумываясь используйте (ES2015 / ES6) классы. Тем не менее, отдавайте предпочтение маленьким функциям, а не классам, пока не будет необходимости в более крупных и сложных объектах. 

**Плохо:**
```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error('Instantiate Animal with `new`');
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error('Instantiate Mammal with `new`');
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error('Instantiate Human with `new`');
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Хорошо:**
```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() { /* ... */ }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() { /* ... */ }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() { /* ... */ }
}
```
**[⬆ вернуться](#Оглавление)**

### Используйте метод цепочки
Этот паттерн очень полезнен в JavaScript. Его используют многие библиотеки, такие как JQuery и Lodash. Это делает ваш код выразительным и не многословным. Используя этот паттерн, вы увидите насколько ваш код станет чище. Просто возвращайте this, в конце ваших методов и вы сможете вызывать их по цепочке.

**Плохо:**
```javascript
class Car {
  constructor() {
    this.make = 'Honda';
    this.model = 'Accord';
    this.color = 'white';
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car();
car.setColor('pink');
car.setMake('Ford');
car.setModel('F-150');
car.save();
```

**Хорошо:**
```javascript
class Car {
  constructor() {
    this.make = 'Honda';
    this.model = 'Accord';
    this.color = 'white';
  }

  setMake(make) {
    this.make = make;
    // возвращаем this для вызова по цепочке
    return this;
  }

  setModel(model) {
    this.model = model;
    // возвращаем this для вызова по цепочке
    return this;
  }

  setColor(color) {
    this.color = color;
    // возвращаем this для вызова по цепочке
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // возвращаем this для вызова по цепочке
    return this;
  }
}

const car = new Car()
  .setColor('pink')
  .setMake('Ford')
  .setModel('F-150')
  .save();
```
**[⬆ вернуться](#Оглавление)**

### Отдавайте предочтение композиции над наследованием
Как было сказано в книге Design Patterns от Банды четырех, следует отдавать предпочтение композиции над наследованием, где вы только можете. Есть много причин, чтобы использовать наследование и много причин использовать композицию. Если ваш мозг инстиктивно видит наследование, попробуйте представить решение вашей проблемы с помощью композиции. 

Когда же  использовать наследование? Это зависит от конкретной проблемы. Вот список случаев, когда наследование имеет больше смысла, чем композиция:

1. Когда наследование представляет собой зависимость «есть», а не «имеет» (Human->Animal vs. User->UserDetails)
2. Вы можете повторно использовать класс (Люди могут двигаться как и все животные).
3. Вы хотите, сделав изменения родительского класса, изменить дочерние классы
(Изменение расхода калорий всех животных, когда они двигаются).

**Плохо:**
```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// У сотрудников есть налоговые данные. Налоговые данные не могут быть сотрудником.
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Хорошо:**
```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```
**[⬆ вернуться](#Оглавление)**

## **Тестирование**
Тестирование очень важная часть разработки. Если у вас нет тестов или их недостаточно, как вы можете быть уверены, что вы ничего не сломаете? Ваша команда должна сама принимать решение по объёму кода покрытого тестами, но чем больше покрыто тестами, тем спокойнее спит разработчик. Это означает, что в дополнение к наличию хорошего инструмента для тестирования, необходимо также использовать [хороший инструмент для проверки покрытия кода тестами](https://github.com/gotwarlost/istanbul). Нет причин не писать тесты. [Вот подборка хороших инструментов для тестирования.](http://jstherightway.org/#testing-tools) Подобрав удобный для вашей команды, пишите тесты для каждого нового модуля или фичи. Если вы выбрали разработку через тестирование (Test Driven Development TDD) -это прекрасно, но главное заключается в том, чтобы убедиться, что тесты покрывают все ваши цели перед разработкой нового кода, или рефакторингом существующего кода.

## Один тест - одно описание.

**Плохо:**
```javascript
const assert = require('assert');

describe('MakeMomentJSGreatAgain', () => {
  it('handles date boundaries', () => {
    let date;

    date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    date.shouldEqual('1/31/2015');

    date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);

    date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```

**Хорошо:**
```javascript
const assert = require('assert');

describe('MakeMomentJSGreatAgain', () => {
  it('handles 30-day months', () => {
    const date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    date.shouldEqual('1/31/2015');
  });

  it('handles leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);
  });

  it('handles non-leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```
**[⬆ вернуться](#Оглавление)**

## **Асинхронность**

### Используйте промисы вместо колбеков
Колбеки приводят к чрезмерной вложенности и плохой читаемости кода. 

**Плохо**
```javascript
const request = require('request');
const fs = require('fs');

const url = 'https://en.wikipedia.org/wiki/Robert_Cecil_Martin';

request.get(url, (requestErr, response) => {
  if (requestErr) {
    console.error(requestErr);
  } else {
    fs.writeFile('article.html', response.body, (writeErr) => {
      if (writeErr) {
        console.error(writeErr);
      } else {
        console.log('File written');
      }
    });
  }
});
```

**Хорошо**
```javascript
const requestPromise = require('request-promise');
const fsPromise = require('fs-promise');

const url = 'https://en.wikipedia.org/wiki/Robert_Cecil_Martin';

requestPromise.get(url)
  .then((response) => {
    return fsPromise.writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });
```
**[⬆ вернуться](#Оглавление)**

### Async/Await делает код чище, чем промисы
Промисы очень хорошая альтернатива колбекам, но в ES2017 / ES8 спецификации появился аsync/аwait, который предлагает ещё лучшее решение. Все, что вам нужно, это написать функцию с префиксом async, внутри которой вы можете писать вашу асинхронную логику императивно. аsync/аwait можно использовать прямо сейчас при помощи babel.

**Плохо**
```javascript
const requestPromise = require('request-promise');
const fsPromise = require('fs-promise');

const url = 'https://en.wikipedia.org/wiki/Robert_Cecil_Martin';

requestPromise.get(url)
  .then((response) => {
    return fsPromise.writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });
```

**Хорошо**
```javascript
const requestPromise = require('request-promise');
const fsPromise = require('fs-promise');

async function getCleanCodeArticle() {
  try {
    const url = 'https://en.wikipedia.org/wiki/Robert_Cecil_Martin';
    const response = await requestPromise.get(url);
    await fsPromise.writeFile('article.html', response);
    console.log('File written');
  } catch(err) {
    console.error(err);
  }
}
```
**[⬆ вернуться](#Оглавление)**

## **Обработка ошибок**
Бросать ошибки — хорошее решение! Это означает, что во время выполнения вы будете знать, если что-то пошло не так. Вы сможете остановить выполнение вашего приложения в нужный момент и видеть место ошибки с помощью стек трейса в консоли.

### Не игнорируйте отловленные ошибки
Ничего не делая с пойманной ошибкой, вы теряете возможность исправить ошибку или отреагировать на неё когда-либо. Вывод ошибки в консоль(console.log(error)) не дает лучшего результата, потому что ошибка может потеряться среди выводимых записей в консоль. Если вы заворачиваете кусок кода в try / catch, значит вы предполагаете возникновение ошибки. В таком случае вы должны иметь запасной план.

**Плохо**
```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Хорошо**
```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // Один из вариантов (более заметный, чем console.log):
  console.error(error);
  // Другой вариант - известить пользователя про ошибку:
  notifyUserOfError(error);
  // И еще вариант - отправить ошибку на сервер :
  reportErrorToService(error);
  // Или используйте все три варианта!
}
```
**[⬆ вернуться](#Оглавление)**

### Не игнорируйте ошибки, возникшие в промисах
Вы не должны игнорировать ошибки, возникшие в промисе, по той же причине, что отловленные ошибки в try / catch.

**Плохо**
```javascript
getdata()
.then((data) => {
  functionThatMightThrow(data);
})
.catch((error) => {
  console.log(error);
});
```

**Хорошо**
```javascript
getdata()
.then((data) => {
  functionThatMightThrow(data);
})
.catch((error) => {
  // Один из вариантов (более заметный, чем console.log):
  console.error(error);
  // Другой вариант - известить пользователя про ошибку:
  notifyUserOfError(error);
  // И еще вариант - отправить ошибку на сервер :
  reportErrorToService(error);
  // Или используйте все три варианта!
});
```
**[⬆ вернуться](#Оглавление)**

## **Форматирование**
Форматирование носит субъективный характер. Нет жестких правил, которые вы должны соблюдать. Главное правило — не спорить по поводу форматирования. Есть куча инструментов для автоматизации этого. Используйте только одну! 
Спор по поводу форматирования — это пустая трата времени и денег для разработчиков. 

Для вещей, которые не подпадают под сферу действия автоматического форматирования (отступы, табуляция или пробелы, двойные или одинарные кавычки и т.д.) смотрите какое-то руководство.

### Используйте один вариант именования
JavaScript является нетипизированным, поэтому именование ваших переменных, функции и т.д говорит вам много чего о них. Эти правила носят субъективный характер, так что ваша команда может выбрать тот вариант, который хочет. Неважно какой вариант вы выберите, главное придерживайтесь вашего выбора. 

**Плохо**
```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const Artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Хорошо**
```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```
**[⬆ вернуться](#Оглавление)**

### Связанные функции должны находится рядом
Если функция вызывает другую, сохраните эти функции вертикально близко в исходном файле. В идеале, функция, которая использует другую функцию, должна быть прямо над ней. Мы склонны читать код сверху-вниз, как газету. Из-за этого удобно размещать код таким образом.

**Плохо**
```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers');
  }

  lookupManager() {
    return db.lookup(this.employee, 'manager');
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(user);
review.perfReview();
```

**Хорошо**
```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers');
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, 'manager');
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```
**[⬆ вернуться](#Оглавление)**

## **Комментарии**

### Комментируйте только тот код, который описывает сложную бизнес-логику
Комментарии не обязательны. Хороший код описывает себя сам.

**Плохо**
```javascript
function hashIt(data) {
  // хеш
  let hash = 0;

  // длина строки
  const length = data.length;

  // Проход по каждому символу данных
  for (let i = 0; i < length; i++) {
    // Берём символ.
    const char = data.charCodeAt(i);
    // Делаем хеш
    hash = ((hash << 5) - hash) + char;
    // Преобразовуем в 32-битное число
    hash &= hash;
  }
}
```

**Хорошо**
```javascript
function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = ((hash << 5) - hash) + char;

    // Преобразовуем в 32-битное число
    hash &= hash;
  }
}
```
**[⬆ вернуться](#Оглавление)**

### Не комментируйте ненужный код
Для этого существуют системы контроля версий. Оставьте старый код в истории системы контроля версий.

**Плохо**
```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Хорошо**
```javascript
doStuff();
```
**[⬆ вернуться](#Оглавление)**

### Не ведите журнал комментариев
Помните: нужно использовать систему контроля версий! Нет необходимости в неисполняемом коде, закомментированном коде и особенно в журнале комментариев. 
Используйте git log, чтобы получить историю!

**Плохо**
```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Хорошо**
```javascript
function combine(a, b) {
  return a + b;
}
```
**[⬆ вернуться](#Оглавление)**

### Избегайте позиционных маркеров
Они, как правило, просто мешают. Пусть функции и имена переменных вместе с соответствующим углублением и форматированием дают визуальную структуру кода.

**Плохо**
```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Хорошо**
```javascript
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

const actions = function() {
  // ...
};
```
**[⬆ вернуться](#Оглавление)**

## **Переводы**

Также доступна на других языках:
  - ![en](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags-iso/shiny/24/US.png) **Englisch**: [ryanmcdermott/clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript)
  - ![br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Brazilian Portuguese**: [fesnt/clean-code-javascript](https://github.com/fesnt/clean-code-javascript)
  - ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Chinese**: [alivebao/clean-code-js](https://github.com/alivebao/clean-code-js)
  - ![de](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Germany.png) **German**: [marcbruederlin/clean-code-javascript](https://github.com/marcbruederlin/clean-code-javascript)
  - ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Korean**: [qkraudghgh/clean-code-javascript-ko](https://github.com/qkraudghgh/clean-code-javascript-ko)
 
**[⬆ вернуться](#Оглавление)**
