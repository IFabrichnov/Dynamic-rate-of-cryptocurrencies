# Динамический курс криптовалют

## Запуск проекта

*Чтобы запустить проект, нужно перейти по ссылке на gh-pages:* [ссылка на проект](https://ifabrichnov.github.io/Dynamic-rate-of-cryptocurrencies/ "ссылка на проект")

![1](https://github.com/IFabrichnov/Dynamic-rate-of-cryptocurrencies/raw/master/README-IMG/1.jpg)

## Описание

Отрисовать таблицу, подгружать в нее данные через fetch. Каждые 5 секунд обновлять данные в таблице и подсвечивать ячейку в зеленый - если курс вырос, красным - понижение, желтым - не изменился. 

### Что применено

**fetch** - Fetch API работает с запросами и ответами http. Также предоставляет глобальный метод fetch() , который позволяет легко и логично получать ресурсы по сети асинхронно.

Пример базового запроса на получение данных:
```javascript
fetch('http://example.com/movies.json')
  .then((response) => {
    return response.json();
  })
  .then((data) => {
    console.log(data);
  });
```
Здесь забираем JSON файл по сети и выводим его содержимое в консоль. Самый простой способ использования fetch() заключается в вызове этой функции с одним аргументом — строкой, содержащей путь к ресурсу, который надо получить — которая возвращает promise, содержащее ответ (объект response).

**Применение в задании**

```javascript
const url = "https://api.coingecko.com/api/v3/coins/";
//первое получение данных и отрисовка
const apiData = () => {
  fetch(url)
    .then(data => {
      return data.json()
    })
    .then(data => {
      console.log(data);
      firstDataItems(data);
    });
};
```

**setInterval** - позволяет вызывать функцию регулярно, повторяя вызов через определённый интервал времени.

```javascript
let timerId = setInterval(func|code, [delay], [arg1], [arg2], ...)
```
Все аргументы имеют такое же значение. Но отличие этого метода от setTimeout в том, что функция запускается не один раз, а периодически через указанный интервал времени.

Чтобы остановить дальнейшее выполнение функции, необходимо вызвать clearInterval(timerId).

Следующий пример выводит сообщение каждые 2 секунды. Через 5 секунд вывод прекращается:

```javascript
// повторить с интервалом 2 секунды
let timerId = setInterval(() => alert('tick'), 2000);

// остановить вывод через 5 секунд
setTimeout(() => { clearInterval(timerId); alert('stop'); }, 5000);
```


## Пошаговое написание кода

**1.** Изначально написана функция apiData для получения и отрисовки данных с помощью fetch() (показано выше). 

**2.** Далее пишу функцию для первой отрисовки таблицы и наполнения ее данными полученными через fetch().

```javascript
const firstDataItems = (el) => {
  let key;

  for (key in el) {
    createTable(el, key);
  }
};
```

**3.** Далее создается функция отрисовки таблицы с контентом 

```javascript
const createTable = (el, key) => {
  const row1 = document.createElement('td');
  const row2 = document.createElement('td');
  rowClass(row1, row2);
  createRow(el, key, row1, row2);
  createRowsData(el, key, row1, row2);
};
 
```

**4.** Для строк таблицы надо присвоить классы (rowClass), создаю данную функцию

```javascript
const rowClass = (firstRow, secondRow) => {
  firstRow.classList.add('row1');
  secondRow.classList.add('row2');
};
```

**5.** Создание строки 

```javascript
const createRow = (el, key, firstRow, secondRow) => {
  const tableItem = document.getElementById('table');
  const col = document.createElement('tr');
  col.classList.add('tr');
  //задал id строке
  col.setAttribute('id', el[key].id);
  col.append(firstRow, secondRow);
  tableItem.append(col);
};
```

**6.** Заполнение строки контентом (первая строка название валюты, вторая – значение).

```javascript
const createRowsData = (el, key, firstRow, secondRow) => {
  firstRow.textContent = el[key].id;
  secondRow.textContent = el[key].market_data.current_price.rub;
};
```

**7.** После первой отрисовки – нужно получить новые данные и перерисовать их в таблице.

```javascript
const apiDataSecond = () => {
  fetch(url)
    .then(data => {
      return data.json()
    })
    .then(data => {
      secondDataItems(data);
    });
};
```

**8.** Тут будет происходить сравнение с старыми данными и их замена в случае изменения

```javascript
const secondDataItems = (el) => {
  let key;
  for (key in el) {
    matchingData(el, key);
  }
};
```


**9.** Нахождение старых и новых данных, с последующей отрисовкой новых

```javascript
const matchingData = (el, key) => {
  //нахождение строки с старыми данными
  const colId = el[key].id;
  const colIdOld = document.getElementById(colId);
  //старое значение
  const oldRow = colIdOld.lastChild.textContent;
  const oldValue = parseInt(oldRow);
  //новая строка
  const row2 = colIdOld.lastChild;

  rowsDataAfterMatching(row2);

  //новое значение
  const newRow = parseInt(row2.textContent);
  //подсвет желтым, зеленым или красным, в зависимости от значения
  compare(oldValue, newRow, row2);
};
```

Также присутствует функция rowsDataAfterMatching, которая помещает новое значение в ячейку номер 2

```javascript
//отрисовка новых данных в строке
const rowsDataAfterMatching = (rowItem) => {
  // row2.textContent = el[key].market_data.current_price.rub;
  rowItem.textContent = Math.round(Math.random() * (100 - 1) + 1);
};
```

Функция compare, которая сравнивает цвета и выбирает в какой цвет покрасить ячейку.

```javascript
const compare = (valueOld, currentValue, lengthItem) => {
  if (valueOld == currentValue) {
    hightLight('yellow', lengthItem);
  } else if (valueOld < currentValue) {
    hightLight('green', lengthItem);
  } else {
    hightLight('red', lengthItem);
  }
};
```
В compare в свою очередь включает в себя функцию hightLight которая добавляет класс с цветом и через 1 секунду убирает его

```javascript
const hightLight = (color, lengthItem) => {
  lengthItem.classList.add(color);
  setTimeout(() => {
    lengthItem.classList.remove(color);
  }, 1000);
};
```

В завершении вызываю setInterval, который вызывает функцию apiDataSecond каждые 5 секунд.

```javascript
setInterval(apiDataSecond, 5000);
```
