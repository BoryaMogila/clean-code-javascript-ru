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

<h4>Аргументы функции (идеально 2 или менее)</h4>
Ограничение количества параметров функции невероятно важно, поскольку оно упрощает тестирование функции. Наличие более чем трёх аргументов приводит к комбинаторному взрыву, когда вам приходится перебирать массу различных случаев с каждым отдельным аргументом. 
Идеальная ситуация — отсутствие аргументов. Один или два аргумента — хорошо, а трех уже следует избегать. 
Большее количество аргументов необходимо консолидировать. Как правило, если передается более двух аргументов, ваша функция пытается сделать слишком многое. В тех случаях, когда это все же не так, лучше использовать объект в качестве аргумента. Поскольку JavaScript позволяет создавать объекты на лету, без специального описания классов, их вполне можно применять, когда требуется передать множество аргументов.
Плохо:
```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}
```
Хорошо:
<source lang="javascript">
const menuConfig = {
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
};

function createMenu(config) {
  // ...
}

createMenu(menuConfig);
</source>
##Объекты и структуры данных
<h4>Функция должна решать одну задачу</h4>
Это, безусловно, самое важное правило в разработке программного обеспечения. Когда функции решают более одной задачи, их труднее сочетать, тестировать и понимать. Как только вы сможете свести каждую функцию к выполнению только одного действия, их станет значительно проще рефакторить, а ваш код станет гораздо более читаемым. Даже если приведенное правило будет единственным вынесенным вами из этого руководства, вы все равно будете круче многих разработчиков. 

Плохо:
<source lang="javascript">
function emailClients(clients) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
</source>
Хорошо:
<source lang="javascript">
function emailClients(clients) {
  clients
    .filter(isClientActive)
    .forEach(email);
}

function isClientActive(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
</source>
<h4>Названия функций должны описывать их назначение</h4>
Плохо:
<source lang="javascript">
function addToDate(date, month) {
  // ...
}

const date = new Date();

// По имени функции трудно сказать, что именно добавляется
addToDate(date, 1);
</source>
Хорошо:
<source lang="javascript">
function addMonthToDate(month, date) {
  // ...
}
## **[Объекты и структуры данных**
const date = new Date();
addMonthToDate(1, date);
</source>
<h4>Функции должны представлять только один уровень абстракции</h4>
Если в функции представлено более одного уровня абстракции, то она, как правило, делает слишком многое. Разделение таких функций приведет к возможности повторного использования и облегчению тестирования.

Плохо:
<source lang="javascript">
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
</source>
Хорошо:
<source lang="javascript">
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
</source>
<h4>Не пишите дублирующий код</h4>
Никогда, никогда, никогда, ни при каких обстоятельствах не создавайте повторяющегося кода. 
Для этого не может быть никаких причин, и это, возможно, худший грех, который вы можете совершить как профессиональный разработчик. Дублированный код означает, что в коде есть более чем одно место, в которое придется вносить правки, если потребуется изменить какую-то логику. JavaScript — нетипизированный язык, что делает вынесение общих функций довольно простым. Воспользуйтесь этим! Такие инструменты как <a href="https://github.com/danielstjules/jsinspect">jsinspect</a> могут облегчить поиск подлежащих рефакторингу дубликатов кода. 
Плохо:
<source lang="javascript">
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
</source>
Хорошо:
<source lang="javascript">
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
</source>
<h4>Устанавливайте объекты по умолчанию с помощию Object.assign</h4>
Плохо:
<source lang="javascript">
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
</source>
Хорошо:
<source lang="javascript">
const menuConfig = {
  title: 'Order',
  //  Пользователь не имеет свойства 'body'
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
</source>
<h4>Не используйте флаги в качестве параметров функции</h4>
Флаги говорят пользователю, что функция совершает более одного действия. Функция должна решать одну задачу. Разделяйте функции, если они исполняют различные варианты кода на основе логического значения.
Плохо:
<source lang="javascript">
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
</source>
Хорошо:
<source lang="javascript">
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
</source>
<h4>Избегайте побочных эффектов</h4>
Функция производит побочный эффект, если она совершает какое-либо действие помимо получения значения и возврата другого значения или значений. Побочный эффект может быть записью в файл, изменением каких-то глобальных переменных или случайным переводом всех ваших денег неизвестным лицам. 
Впрочем, побочные эффекты в программе необходимы. Пусть, как и в предыдущем примере, вам требуется запись в файл. Опишите то, что вы хотите сделать, строго в одном месте. 
Не создавайте несколько функций и классов, которые пишут что-то в конкретный файл. Создайте один сервис, который всем этим занимается. Один и только один.
Суть в том, чтобы избегать распространенных ошибок, таких как, например, передача состояния между объектами без какой-либо структуры, с помощью изменяемых данных, которые может перезаписывать кто угодно, в обход централизованного места применения побочных эффектов. 
Если научитесь так делать, вы станете счастливее, чем подавляющее большинство других программистов.
Плохо:
<source lang="javascript">
// Глобальная переменная, на которую ссылается последующая функция.
// Если бы у нас была еще одна функция, которая бы работала с именем name как со строкой,
// то обнаружив массив, он непременно бы поломалась
let name = 'Ryan McDermott';

function splitIntoFirstAndLastName() {
  name = name.split(' ');
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
</source>
Хорошо:
<source lang="javascript">
function splitIntoFirstAndLastName(name) {
  return name.split(' ');
}

const name = 'Ryan McDermott';
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
</source>
<h4>Не переопределяйте глобальные функции</h4>
Загрязнение глобальных переменных — плохая практика в JavaScript, так как может породить конфликты с другой библиотекой, и пользователь вашего API не увидит ошибок, пока не получит исключение в продакшене. Давайте рассмотрим пример: что делать, если вы хотите расширить стандартный функционал Array из JavaScript, добавив метод diff, который бы вычислял различие между двумя массивами? Вы должны были бы записать новую функцию в Array.prototype, но тогда она может войти в конфликт с другой библиотекой, которая пыталась сделать то же самое. А если другая библиотека использовала метод diff, чтобы найти разницу между первым и последним элементами массива? Именно поэтому гораздо лучше использовать классы ES2015/ES6 и просто унаследовать нашу реализацию от класса Array.
Плохо:
<source lang="javascript">
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
</source>
Хорошо:
<source lang="javascript">
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
</source>
<h4>Отдавайте предпочтение фунциональному программированию над иперативным</h4>
JavaScript не настолько функциональный язык, как Haskell, но определенной доли функциональности он не лишен. Функциональные языки чище и их проще тестировать. Применяйте функциональный стиль программирования при возможности.
Плохо:
<source lang="javascript">
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
</source>
Хорошо:
<source lang="javascript">
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

const totalOutput = programmerOutput
  .map((programmer) => programmer.linesOfCode)
  .reduce((acc, linesOfCode) => acc + linesOfCode, 0);
</source>
<h4>Инкапсулируйте условия</h4>
Плохо:
<source lang="javascript">
if (fsm.state === 'fetching' && isEmpty(listNode)) {
  // ...
}
</source>
Хорошо:
<source lang="javascript">
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === 'fetching' && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
</source>
<h4>Избегайте негативных условий</h4>
Плохо:
<source lang="javascript">
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
</source>
Хорошо:
<source lang="javascript">
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
</source>
<h4>Избегайте условных конструкций</h4>
Такая задача кажется невозможной. Услышав подобное, большинство людей говорят: "Как я должен делать что-либо без выражения if?". Ответ заключается в том, что во многих случаях для достижения тех же целей можно использовать полиморфизм. Второй вопрос, как правило, звучит так: "Хорошо, замечательно, но почему я должен их избегать?". Ответ — предыдущая концепция чистого кода, которую мы узнали: функция должна выполнять только одну задачу. Если у вас есть классы и функции, содержащие конструкцию 'if', вы словно говорите своему пользователю, что ваша функция выполняет больше одной задачи. Помните: одна функция — одна задача.
Плохо:
<source lang="javascript">
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
</source>
Хорошо:
<source lang="javascript">
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
</source>
<h4>Избегайте проверки типов (часть 1)</h4>
JavaScript является нетипизированным языком, а это значит, что ваши функции могут принимать аргументы любого типа. Порой вы обжигались этой свободой, что побуждало вас производить проверку типов в ваших функциях. Есть множество способов ее избежать. В первую очередь стоит подумать над согласованным API.
Плохо:
<source lang="javascript">
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.peddle(this.currentLocation, new Location('texas'));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location('texas'));
  }
}
</source>
Хорошо:
<source lang="javascript">
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location('texas'));
}
</source>
<h4>Избегайте проверки типов (часть 2)</h4>
Если вы работаете с базовыми примитивами, такими как строки, целые числа и массивы, и не можете использовать полиморфизм, хотя все еще чувствуете необходимость в проверках типа, вам стоит рассмотреть возможность применения TypeScript. Это отличная альтернатива обычному JavaScript, предоставляющая возможность статической типизации поверх стандартного синтаксиса JavaScript. Проблема с ручной проверкой типов в обычном JavaScript в том, что иллюзия безопасности, которую она создает, никак не компенсируется потерей читабельности из-за многословности кода. Держите ваш код в чистоте, пишите хорошие тесты и делайте эффективные ревизии кода. Или делайте все то же самое, но с помощью TypeScript (который, как я уже сказал, является прекрасной альтернативой!). 
Плохо:
<source lang="javascript">
function combine(val1, val2) {
  if (typeof val1 === 'number' && typeof val2 === 'number' ||
      typeof val1 === 'string' && typeof val2 === 'string') {
    return val1 + val2;
  }

  throw new Error('Must be of type String or Number');
}
</source>
Хорошо:
<source lang="javascript">
function combine(val1, val2) {
  return val1 + val2;
}
</source>
<h4>Не оптимизируйте сверх меры</h4>
Современные браузеры производят множество оптимизаций под капотом во время исполнения кода. Оптимизируя код вручную, вы, зачастую, просто тратите свое время. <a href="https://github.com/petkaantonov/bluebird/wiki/Optimization-killers">Есть прекрасные ресурсы</a> с описанием ситуаций, когда оптимизация действительно хромает. Поглядывайте на них в свободное время, пока эти проблемы не будут исправлены, если вообще будут, конечно.
Плохо:
<source lang="javascript">
// В старых браузерах каждая итерация с некэшированной `list.length` будет дорогостоящей
// из-за перерасчета `list.length`. В современных браузерах это оптимизировано.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
</source>
Хорошо:
<source lang="javascript">
for (let i = 0; i < list.length; i++) {
  // ...
}
</source>
<h4>Удаляйте мертвый код</h4>
Мертвый код так же плох, как повторяющийся код. Нет никаких причин, чтобы держать его в репозитории. Если код не вызывается, избавьтесь от него! 
Он по-прежнему будет в системе контроля версий, если когда-нибудь он все-таки вам понадобится.
Плохо:
<source lang="javascript">
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');
</source>
Хорошо:
<source lang="javascript">
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');
</source>
