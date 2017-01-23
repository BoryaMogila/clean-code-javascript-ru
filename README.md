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
**[⬆ венутся](#Оглавление)**

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
**[⬆ венутся](#Оглавление)**

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
**[⬆ венутся](#Оглавление)**

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

**Good:**
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
**[⬆ венутся](#Оглавление)**

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
**[⬆ венутся](#Оглавление)**

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
**[⬆ венутся](#Оглавление)**

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
**[⬆ венутся](#Оглавление)**

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
**[⬆ венутся](#Оглавление)**

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
**[⬆ венутся](#Оглавление)**

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
**[⬆ венутся](#Оглавление)**

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
**[⬆ венутся](#Оглавление)**

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
**[⬆ венутся](#Оглавление)**

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
**[⬆ венутся](#Оглавление)**

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
**[⬆ венутся](#Оглавление)**

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
**[⬆ венутся](#Оглавление)**

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
**[⬆ венутся](#Оглавление)**

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
**[⬆ венутся](#Оглавление)**

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
**[⬆ венутся](#Оглавление)**
