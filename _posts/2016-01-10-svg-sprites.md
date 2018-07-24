---
layout:     post
title:      "SVG-спрайты"
subtitle:   "Мои предпочтения"
permalink:  /page/:title
categories: web
---

Спрайт, в контексте веб-разработки, чаще всего, — один большой файл с графикой, получившийся в результате «сшития» нескольких маленьких графических файлов (иконок, элементов оформления). Это нужно для экономии количества запросов к серверу: меньше запросов — сайт работает и загружается быстрее. Сшивать в спрайт можно и растровую, и векторную графику.

Я расскажу о своём любимом способе работы с векторными спрайтами.

## Работа с SVG (введение)

Не буду расхваливать SVG, [причин](http://codepen.io/pukhalski/pen/uxIKB) [его](http://codepen.io/yoksel/pen/yuIFe) [невероятной](http://habrahabr.ru/post/207908/) [крутизны](https://codyhouse.co/demo/animated-svg-image-slider/index.html#0) [много](http://tympanus.net/codrops/2014/08/19/making-svgs-responsive-with-css/) и они неоднократно описаны. Поддержка [начинается с IE9](http://caniuse.com/#feat=svg).

Источником векторных иконок (помимо дизайнеров) служат [icomoon.io](https://icomoon.io/app/), [flaticon.com](http://www.flaticon.com/), [freepik.com/free-vectors](http://www.freepik.com/free-vectors/icons) и им подобные. Нарисовать самому можно в [Adobe Illustrator](http://www.adobe.com/ru/products/illustrator.html) или в [Inkscape](https://inkscape.org/ru/). Поскольку это обычный текстовой формат, редактировать его можно в текстовом редакторе.

Редакторы очень любят вставлять в код SVG-файлов служебную информацию, увеличивая вес файлов. Оптимизировать можно или он-лайн на [jakearchibald.github.io/svgomg](https://jakearchibald.github.io/svgomg/), или задачей для какого-либо таск раннера (см. ниже).

И да, SVG [лучше](http://frontender.info/ten-reasons-we-switched-from-an-icon-font-to-svg/) иконочных шрифтов, ибо даёт больше возможностей и не является костылём).

У меня есть [репозиторий со сводом рекомендаций](https://github.com/nicothin/web-design#Векторная-графика) для дизайнеров по работе с векторной графикой. Наиболее важные из них, актуальные для подготовки SVG-файлов к «сшивке» их в спрайт:

*   Артборд подогнан по габариту фигуры или все однотипные артборды подогнаны под единый размер.
*   Всё, что может быть слито в единую форму, слито.
*   Убраны лишние объекты.
*   Проведено сжатие.

## SVG-спрайты

В подавляющем большинстве случаев, я использую такие спрайты для иконок. Мой любимый вариант основан на переиспользовании (`symbol` и `use`):

1.  Собираем один общий svg-файл со множеством `symbol` (элементы спрайта), имеющих свои `id` . У тега `svg` задаём `style="display:none"`. [Смотреть пример](http://codepen.io/nicothin/pen/ZQKZWW?editors=100).
2.  Вставляем полученный спрайт в разметку страницы. (Я предпочитаю делать это с помощью javascript и localStorage, об этом ниже.)
3.  В разметке используем ссылки на `symbol` при помощи `use`. [Смотреть пример](http://codepen.io/nicothin/pen/qbmwNP?editors=100).

### Достоинства метода:

*   чистый, читабельный код,
*   управление с уровня CSS страницы (в том числе, `fill="currentColor"`),
*   кеширование в localStorage (спрайт загружается только единожды),
*   доступность (можно добавить `title`).

### Недостатки метода:

*   управление с уровня CSS не полное: сложно управлять отдельными частями символа (имеющими свои CSS-классы), если он вставлен несколько раз,
*   работает только с сервера (локального/удалённого — не важно), ибо использует localStorage.

## Как собирать

Увы, в отличие от [растровых спрайтов](http://htmllinks.ru/search/%D1%81%D0%BF%D1%80%D0%B0%D0%B9%D1%82), он-лайн инструментов для сборки SVG-спрайта я не нашел (плохо искал?). Помимо ручной сборки ([не-не-не-не, Девид Блейн, нет!](https://youtu.be/CdTIQ6BVlvw?t=10s)) предложить могу только сборку с помощью Grunt/Gulp.

[Вот репозиторий](https://github.com/nicothin/svg-sprite), собранный мной из своего старого стартового. В нём оставлена автоматизация, ответственная за сборку SVG-спрайта, сжатие javascript, запуск локального сервера и автообновление. После вызова команды `grunt` в консоли, автомат соберёт спрайт, сожмёт javascript (нужен для работы с localStorage), запустит локальный сервер и откроет в браузере `index.html` .

Для сжатия SVG использован [imagemin](https://github.com/gruntjs/grunt-contrib-imagemin) ([вот прекрасный аналог](https://github.com/sindresorhus/grunt-svgmin)), для сборки спрайта использован [grunt-svgstore](https://github.com/FWeinb/grunt-svgstore). Уверен, что найти аналогичные задачи для Gulp не составит большого труда.

## Как вставить спрайт в разметку

<div class="alert">
  <strong>Внимание</strong><br>
  Информация о вставке несколько устарела. Сейчас я использую <code>svg > use</code>.
</div>

Способов много. Поскольку полученный спрайт — строка текста, вставить её на страницу можно, как минимум, с уроня серверного программирования и с уровня автоматизации фронтенда.

Я для себя выбрал способ с javascript и localStorage, т.к. это не вызывает зависимостей ни от чего, кроме включенного javascript на стороне клиента: [поддержка localStorage](http://caniuse.com/#search=local%20storage) очень хорошая. Именно этот способ и используется в [демонстрационном репозитории](https://github.com/nicothin/svg-sprite). Есть, впрочем, небольшая оговорка: localStorage имеет ограничение в 5 Мбайт на домен.

Способ описан в [недавней статье Osvaldas Valutis](http://osvaldas.info/caching-svg-sprite-in-localstorage) и состоит в следующем: при срабатывании javascript происходит проверка: спрайт уже записан в localStorage? Если да, он берется оттуда и вставляется на страницу. Если нет, файл скачивается, записывается в localStorage и потом вставляется на страницу.

Скрипт снабжён переменной `revision`, позволяющей контролировать кеширование спрайта. Если она изменится, спрайт будет перезагружен с сервера даже в том случае, если уже есть в localStorage.

Привожу листинг кода с комментариями:

```js
;(function(window, document) {
  'use strict';
  var file = 'img/sprite.svg', // путь к файлу спрайта на сервере
      revision = 1;            // версия спрайта
  if (!document.createElementNS || !document.createElementNS('http://www.w3.org/2000/svg', 'svg').createSVGRect) return true;
  var isLocalStorage = 'localStorage' in window && window\['localStorage'\] !== null,
    request,
    data,
    insertIT = function() {
      document.body.insertAdjacentHTML('afterbegin', data);
    },
    insert = function() {
      if (document.body) insertIT();
      else document.addEventListener('DOMContentLoaded', insertIT);
    };
  if (isLocalStorage && localStorage.getItem('inlineSVGrev') == revision) {
    data = localStorage.getItem('inlineSVGdata');
    if (data) {
      insert();
      return true;
    }
  }
  try {
    request = new XMLHttpRequest();
    request.open('GET', file, true);
    request.onload = function() {
      if (request.status >= 200 && request.status < 400) {
        data = request.responseText;
        insert();
        if (isLocalStorage) {
          localStorage.setItem('inlineSVGdata', data);
          localStorage.setItem('inlineSVGrev', revision);
        }
      }
    }
    request.send();
  } catch (e) {}
}(window, document));

```
В [сжатом виде](https://github.com/nicothin/svg-sprite/blob/master/js/script.min.js) этот скрипт имеет очень небольшой размер.

## Заключение

Если не нужен контроль с уровня CSS страницы, возможно лучшим решением будет вставлять небольшие (до 10 Кбайт) иконки прямо в CSS-файл, кодируя их в base64. В LESS даже [встроенная функция](http://lesscss.org/functions/#misc-functions-data-uri) для этого есть.

