---
layout:     post
title:      "Как комфортно работать с&nbsp;GitHub в&nbsp;консоли Windows"
subtitle:   "В дополнение к&nbsp;возможностям GitHub Desktop for&nbsp;Windows"
permalink:  /page/:title
categories: web
---

Предполагается, что Вы используете тот вариант работы с консолью, который я описал в [одной из предыдущих статей]({{ site.url }}/page/console-windows).

Ранее на этой странице был вольный перевод [страницы помощи github о соединении с ним по SSH](https://help.github.com/articles/connecting-to-github-with-ssh/). Я решил выпилить эту статью, т.к. Вы вполне можете клонировать/связать репозитории по HTTPS и [указать своему гиту кешировать пароль](https://help.github.com/articles/caching-your-github-password-in-git/). После этого вы введёте пароль один раз и более он не будет запрашиваться при обмене данными с github.

Если потребуется изменить пароль к github, то кешированный пароль может мешать. Чтобы отключить кеширование, найдите в своей папке пользователя файл `.gitconfig` и временно сотрите оттуда строки про `credential.helper`.
