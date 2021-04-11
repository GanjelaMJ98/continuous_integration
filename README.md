## Continuous integration test

Hello World с простеньким Boost тестом, который необходимо собрать на удаленной машине и выложить на платформе дистрибуции.

В качестве примера возьмем
 - Travis CI — распределённый веб-сервис для сборки и тестирования (https://travis-ci.org/)
 - JFrog Bintray - платформе дистрибуции (https://bintray.com/)


### Hello World

Выводим версию приложения и  переменной PROJECT_VERSION_PATCH, которая генерируется с помошью version.h.in и зависит от переменной TRAVIS_BUILD_NUMBER, которая инкрементится при каждой новой сборке в Travis CI.
Для теста на локальной машине можно создать фаил version.h самостоятельно

Hапример :

```c++
#pragma once

#define PROJECT_VERSION_PATCH "1.1.1"
```

Так же пишем простенький тест для проверки опции тестирования на сервисе
Подробнее см. в CMake файле

### Deploy
Для взаимподействия с Travis CI в корене репозитория необходимо создать фаил .travis.yml, в котором выбирается buid-машина и описываются комманды сборки

В нашем случае это:
- cmake .
- cmake --build .
- cmake --build . --target test
- cmake --build . --target package

В случае успешной сборки заливаем пакет на платформу Bintray с помошью curl (см.https://www.jfrog.com/confluence/display/BT/Uploading)

``` bash
curl -f -T first-0.0.$TRAVIS_BUILD_NUMBER-Linux.deb -uganjelamj98:$BINTRAY_API_KEY "https://api.bintray.com/content/ganjela98/otus-cpp/homework/$TRAVIS_BUILD_NUMBER/first-0.0.$TRAVIS_BUILD_NUMBER-Linux.deb;deb_distribution=xenial;deb_component=main;deb_architecture=amd64;publish=1"
```
ganjelamj98 - пользователь
first-0.0.$TRAVIS_BUILD_NUMBER-Linux.deb - наш пакет
BINTRAY_API_KEY - ключ от Bintray, который мы укажем в настройках репозитория на Travis CI с помошью переменных среды сборки

Найти этот ключ можно по ссылке https://bintray.com/profile/edit в пункте API Key