---
layout:     post
title:      "Как комфортно работать с&nbsp;GitHub в&nbsp;консоли Windows"
subtitle:   "В дополнение к&nbsp;возможностям GitHub Desktop for&nbsp;Windows"
permalink:  /page/:title
categories: web
---

Предполагается, что Вы используете тот вариант работы с консолью, который я описал в [одной из предыдущих статей](http://nicothin.ru/page/console-windows).

Проблема: пока Вы не сгенерировали и не сохранили на GitHub SSH-ключ, он будет при любом запросе просить у Вас имя и пароль от аккаунта. То же самое будет, если Вы клонировали репозиторий по HTTPS, а не по SSH.

## Что сделать, чтобы GitHub не просил в консоли имя пользователя и пароль

Два относительно простых условия:

### 1\. Удаленный репозиторий должен иметь SSH-url

Ниже — данные, взятые со [страницы помощи github](https://help.github.com/articles/changing-a-remote-s-url/).

**Шаг 1.** В консоли переходим в папку проекта, связанного с удаленным репозиторием (например, клонированного из GitHub).

**Шаг 2.** Набираем `git remote -v` и смотрим на вывод. Он должен быть похож на следующий:

origin  git@github.com:USERNAME/OTHERREPOSITORY.git (fetch)
origin  git@github.com:USERNAME/OTHERREPOSITORY.git (push)

Это SSH-url-ы. Если это не так и url-ы начинаются с `https://github.com/` , то переходим к шагу 3, иначе сразу переходим к генерации SSH-ключа.

**Шаг 3.** Идем на страницу `https://github.com/USERNAME/REPOSITORY.git` (ссылка такого вида была показана в консоли на предыдущем шаге). ищем в сайдбаре «clone URL», нажимаем на «SSH» и копируем ссылку вида `git@github.com:USERNAME/OTHERREPOSITORY.git`

**Шаг 4.** Находясь в папке проекта, вводим в консоли `git remote set-url origin git@github.com:USERNAME/OTHERREPOSITORY.git` , где последняя часть — это ваш скопированный в буфер во время шага 3 адрес. Нажимаем Enter.

Готово, теперь выполняя команду из шага 2, Вы увидите, что локальный репозиторий связан с удаленным по url-ам, начинающимся с `git@github.com` и [это норма](https://www.google.ru/search?q=%D1%8D%D1%82%D0%BE+%D0%BD%D0%BE%D1%80%D0%BC%D0%B0&newwindow=1&tbm=isch).

### 2\. У Вас должен быть SSH-ключ и GitHub должен о нем знать

ВНИМАНИЕ! Нижеприведенная информация несколько устарела. Выполните шаги, описанные в [документации Гитхаба](https://help.github.com/articles/generating-an-ssh-key/):

*   сгенерируйте SHH-ключ
*   добавьте его в свой аккаунт на Гитхабе
*   протестируйте SSH-соединение

Ниже — данные, взятые со [страницы помощи github](https://help.github.com/articles/generating-ssh-keys/) о генерации SSH-ключа.

**Шаг 1.** Проверьте, может ключи уже есть. Введите в консоли: `ls -al ~/.ssh` . Это покажет список файлов в папке, предназначенной для хранения ключей. Если в списке будут `id_rsa.pub` и `id_rsa` , сразу переходите к шагу 3.

**Шаг 2.** Сгенерируйте SSH-ключ

1.  В консоли: `ssh-keygen -t rsa -b 4096 -C "YOUR_EMAIL@example.com"`
2.  На вопрос «Enter file in which to save the key (/Users/you/.ssh/id_rsa):» просто нажмите Enter
3.  На вопрос «Enter passphrase (empty for no passphrase):» введите контрольную фразу (пароль, который нужно запомнить). После нажатия Enter консоль попросит повторить ввод ключевой фразы. Рекомендуется отнестись к этому как к важному паролю: фраза должна быть устойчива к подбору.

Если все сделано верно, Вы увидите что-то вроде:

Your identification has been saved in /Users/YOUR\_NAME/.ssh/id\_rsa.
Your public key has been saved in /Users/YOUR\_NAME/.ssh/id\_rsa.pub.
The key fingerprint is:
01:0f:f4:3b:ca:85:d6:17:a1:7d:f0:68:9d:f0:a2:db YOUR_EMAIL@example.COM

**Шаг 3.** Добавьте сгенерированный ключ в свой SSH-агент. Мы используем внутри cmder Git Bash, так что включите SSH-агент командой `ssh-agent -s` , после чего добавьте в него ключ командой `ssh-add ~/.ssh/id_rsa` .

**Шаг 4.** Добавьте ключ в свой аккаунт github.com. В консоли: `clip < ~/.ssh/id_rsa.pub` (скопировать ключ в буфер обмена), в своем аккаунте: Settings → SSH keys → Add SSH key — вставить содержимое буфера в поле Key, придумать какое-то название («Консольный ключ Win10» — к примеру), нажать Add key и подтвердить эти действия вводом пароля от аккаунта.

**Шаг 5.** Проверьте соединение. В консоли: `ssh -T git@github.com` и на вопрос «Are you sure you want to continue connecting (yes/no)?» введите `yes` и нажмите Enter. Увидите что-то вроде:

Hi USERNAME! You've successfully authenticated, but GitHub does not provide shell access.

## Что сделать, чтобы не вводить постоянно контрольную фразу (passphrase)

Проблема: даже если Вы сделали все вышеуказанное, придется при любом запросе на GitHub вводить контрольную фразу. Безопасность превыше всего.

Однако, можно сделать так, чтобы запрос контрольной фразы происходил только один раз, при запуске (каждой новой) консоли. Для этого нужно открыть `C:/Users/ИМЯ_ПОЛЬЗОВАТЕЛЯ/.bashrc` (ранее мы писали в этот файл [алиасы](http://nicothin.ru/page/console-windows#alias)) и добавить туда следующее:

#! /bin/bash
eval \`ssh-agent -s\`
ssh-add

## Результат

Вот так [с помощью нехитрых приспособлений](https://www.google.ru/search?q=%D0%B2%D0%BE%D1%82+%D1%82%D0%B0%D0%BA+%D1%81+%D0%BF%D0%BE%D0%BC%D0%BE%D1%89%D1%8C%D1%8E+%D0%BD%D0%B5%D1%85%D0%B8%D1%82%D1%80%D1%8B%D1%85+%D0%BF%D1%80%D0%B8%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1%D0%BB%D0%B5%D0%BD%D0%B8%D0%B9+%D0%B1%D1%83%D1%85%D0%B0%D0%BD%D0%BA%D1%83&newwindow=1&biw=1920&tbm=isch) можно получить комфортную работу с GitHub в [консоли Windows](http://nicothin.ru/page/console-windows).
