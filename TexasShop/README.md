Copyright © 2020-2021 Ivan Bukshev. All rights reserved.

# TexasShop

## Предыстория

К нашему агенству по разработке программного обеспечения обратилась семья из Техаса, у которой есть свой небольшой магазин в сельской местности.
Использование бумажного журнала и карандаша для ведения учёта покупателей и всех продаж им надоело — пришло время "компьютеризации".

Они купили компьютер и теперь ведут весь учёт в одном-единственном файле с расширением ".money". 
Файл с данными содержит в себе всю историю всех покупок за всё время.

*P.S. Семья хочет следить за прогрессом, поэтому нам нужно использовать git-репозиторий, в котором мы будем хранить наш код и историю коммитов. 
После реализации каждой мелкой функции приложения необходимо делать коммит с описанием проделанной работы.*

---

## Формат .money-файла
Формат сомнительный — да.
Убедить использовать XML или JSON не удалось.
```
#####
Person: %name%
Date: 10.08.2020
%item%;%amount%;%price%
...
#####
Person: %name%
Date: %date%
%item%;%amount%;%price%
...
```

---

## Условия

Ну, хоть нам дали условия, которые будут железно соблюдаться.

### О самих записях в журнале
**#####** — является разделителем между записями в журнале.

Каждая запись состоит из имени покупателя, даты совершения покупки и списка купленных товаров (точнее их типов). Записей может быть от 0 до 1'000'000 (условная бесконечность).

Конкретных типов товаров (строчек) за одной записью в журнале может числиться от 1 до 10. В рамках одной записи они не повторяются.


### Подробно о данных
**%name%** — имя покупателя. Тип значения: ```String```. Состоит из одного слова. Один и тот же покупатель может встречаться в файле несколько раз.

**%date%** — дата совершения покупки. Тип значения: ```String```. Имеет формат: ```dd.MM.yyy```. Ошибок быть не может.

**%item%** — наименование типа товара (тип enum ```ItemKind```). Значения enum'а зафиксированы. Один и тот же тип может встречаться несколько раз (от 0 до 5) для каждого покупателя с разным количеством и ценой. Значения: ```bread```, ```milk```, ```beer```, ```shotgun```, ```bullet```, ```cow```, ```cowboyHat```, ```bible```.

**%price%** — стоимость за одну позицию товара (1 штука / 1 упаковка /и т.д.). Тип значения: ```Double```. Диапазон значений: от 0 до 10'000.

**%amount%** — количество товаров определённого типа. Тип значения: ```Integer```. Диапазон значений: от 0 до 100.

---

## Технические детали
Программа должна представялть собой Command Line Tool приложение, написанное на языке Swift (версия >= 5.0).

В программу можно передать один из следующих аргументов: ```BuyersList```, ```BuyersSummary```, ```ItemsSummary```.

### Более детально об аргументах

#### BuyersList

Анализируем весь файл. Ищем и выделяем всех уникальных покупателей по всем данным.

Выводим результат в формате: ```"index. name\n"```.

#### BuyersSummary

Анализируем весь файл. Формируем вывод по следующему принципу:

Для каждого покупателя необходимо вывести информацию по каждому из купленных товаров:
1. Сколько суммарно каждого типа товара было приобретено (в штуках, упаковках и т. д.).
2. Сколько суммарно было потрачено на каждый из типов товаров.

*Помним, что покупатели могли приходить в магазин, к примеру, в разные дни за одними и теми же товарами*.

#### ItemsSummary

Анализируем весь файл. Формируем вывод по следующему принципу:

Для каждого представителя перечисления ```ItemKind``` формируем вывод:
1. Сколько человек (уникальных) приобретали этот товар.
2. Сколько суммарно штук/упаковок было куплено.
3. Сколько суммарно было потрачено денежных средств на данный тип товара.

---

## Обязательные функции для реализации

### Передача данных приложение через аргументы

Семья-заказчик будет работать не с кодом, а с **exec-файлом*** через Terminal.

Поэтому необходимо реализовать передачу аргументов через Command Line.

*В коде до переданных аргументов можно доступиться через __CommandLine.arguments__*.

### Help для приложения

Для приложения должна быть реализована функция помощи.

При вводе неверных данных пользователю должен выводиться красивый manual по использованию.

Должна быть возможность и прямого вызова через передачу аргумента ```--help``` или ```--h```.

*Для красивого вывода информации используйте \n, \t, etc.*

### PurchaseRecord

Описать сущность для одной строки из истории.

Данная сущность должна содержать в себе **тип товара** (```ItemKind```), **количество** (```Integer```), **стоимость за одну шутку/упакову** (```Double```), вычисляемое свойство **суммарная затрата по товару** (```Double```) == amount * price. 

### Purchase

Описать сущность для одной позиции. 

Данная сущность должна содержать в себе дату (```Date```), имя покупателя (```String```) и список всех покупок (тип на ваше усмотрение).

### PurchaseHistoryParser

Для данной сущности необходим **протокол** и **класс** (или **структура** – решать вам).

**Основная обязанность** — парсинг документа .money в swift-сущности.

---

## Опциональные требования

### Реализация паттерна Strategy

Хорошее описание можно найти здесь: https://refactoring.guru/ru/design-patterns/strategy

Для каждой из опций необходимо реализовать свою стратегию по одноимённому паттерну проектирования — заказчик требует от нас чистый код, что поделать.

Наш файл main.swift должен быть максимально простым и понятным, как и весь код в целом.

### Усложнённые опции

В качестве передаваемых аргументов должны быть добавлены ещё 2 опции: 
1. ```--n%name%```. Тот же **BuyersSummary**, но для конкретного человека (*%name%*).
1. ```--i%item%```. Тот же **ItemsSummary**, но для конкретного типа товара (*%item%*).

### Хранение данных в нескольких файлах

Семья-заказчик поняла, что хранить всю историю в одном файле — глупо. 
Теперь они хранят всю информацию в разных файлах по датам. 

Имя файла состоит из **основого префикса** ```PurchaseHistory```, а далее, через **нижнее подчёркивание** (```_```) стоит **дата конкретного дня** в формате: ```dd_MM_yyy```. В конце стоит **расширение** ```.money``` 

**Пример**: файл для 10 августа 2020 года будет иметь вид ```PurchaseHistory_10_08_2020.money```

1. Необходимо доделать приложение, чтобы оно поддерживало все старые функции, но уже с учётом всех файлов. 
2. В программу дополнительно приходит путь до существующей директории с файлами в формате: ```-ap%path%```.
3. Порядок входящих аргументов должен быть не важен.
4. Необходимо обновить ```--help``` приложения.

### Диапазоны для анализа

Семья-заказчик решила не останавливаться и пошла дальше — она хочет получать информацию по всем ранее реализованных функциям, но с указанием периода дат.

1. В приложение приходят дополнительные параметры: ```--fd%date%``` и ```--td%date%```. 
2. **%date%** имеет формат ```10.08.2020```, но конечный пользователь всегда может ошибиться — помните это.
