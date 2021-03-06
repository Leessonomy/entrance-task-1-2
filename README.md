# Задание 1 — найди ошибки

В этом репозитории находятся материалы тестового задания "Найди ошибки" для [14-й Школы разработки интерфейсов](https://academy.yandex.ru/events/frontend/shri_msk-2018-2) (осень 2018, Москва, Санкт-Петербург, Симферополь).

Для работы тестового приложения нужен Node.JS v9. В проекте используются [Yandex Maps API](https://tech.yandex.ru/maps/doc/jsapi/2.1/quick-start/index-docpage/) и [ChartJS](http://www.chartjs.org).

## Задание

Код содержит ошибки разной степени критичности. Некоторые из них — стилистические, а другие — даже не позволят вам запустить приложение. Вам нужно найти все ошибки и исправить их.

Пункты для самопроверки:

1. Приложение должно успешно запускаться.
1. По адресу http://localhost:9000 должна открываться карта с метками.
1. Должна правильно работать вся функциональность, перечисленная в условиях задания.
1. Не должно быть лишнего кода.
1. Все должно быть в едином codestyle.

## Запуск

```
npm i
npm start
```

При каждом запуске тестовые данные генерируются заново случайным образом.

Алгоритм решения;

1) Исправляем некорректный export в модуле `map.js`, вместо него пишем `export default`. Альтернативный вариант: обернуть `initMap` в фигурные скобки.
2) Высота карты равна 0. Исправим добавив к id `map` и классу `ymaps-2-1-73-map` - `height: 100%`.
3) На карте не отображались метки, я открыл код, прошелся debugger'ом по всем модулям - посмотрел в `mappers.js` данные с сервера и обнаружил наши метки, объекты упакованные в массивы. 
Затем я решил открыть документацию, чтобы лучше понять логику кода. Переменная `myMap` создается конструктором с координатами. 
В разделе "Работа с большим числом объектов" - в нашем случае их 721, присутствовало подробное описание менеджера объектов. Добавлением `myMap.geoObjects.add(objectManager)` проблема разрешилась. Метки появились в Иране. Теперь понятно, что заданы неправильные координаты. Поменял местами "долготу" и "широту" в `mappers.js`.
4) Открывался пустой мини попап. В `details.js`, в `BalloonContentLayout` понадобилось сменить стрелочные функции на обычные, чтобы задать правильный контекст this, поскольку внутри стрелочных - нет собственного контекста this, но берется снаружи.
5) Я обнаружил, что `popup.js` нигде не импортируется, при этом popup уже работает и отображается. Очевидно лишний файл. Удалил.
5) График станций некорректно отображается. Так как в визуальной части графика величина Y равняется 10, в `chart.js` меняем в `yAxes: ticks: max: с 0 на 10`.
6) Неисправные объекты входящие в кластер, должны отображаться в иконке кластера. Удаление `objectManager.clusters.options.set()` решило проблему, иконка пришла с сервера.
