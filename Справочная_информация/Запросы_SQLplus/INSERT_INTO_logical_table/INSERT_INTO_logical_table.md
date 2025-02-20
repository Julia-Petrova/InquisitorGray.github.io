﻿---
layout: default
title: INSERT INTO logical_table
nav_order: 25
parent: Запросы SQL+
grand_parent: Справочная информация
has_children: false
has_toc: false
---

# INSERT INTO logical_table

Запрос позволяет загрузить данные в [логическую таблицу](../../../Обзор_понятий_компонентов_и_связей/Основные_понятия/Логическая_таблица/Логическая_таблица.md) 
[логической базы данных](../../../Обзор_понятий_компонентов_и_связей/Основные_понятия/Логическая_база_данных/Логическая_база_данных.md) 
из внешнего источника данных. Загружаемые данные должны соответствовать формату, 
[указанному при создании внешней таблицы загрузки](../CREATE_UPLOAD_EXTERNAL_TABLE/CREATE_UPLOAD_EXTERNAL_TABLE.md) и описанному 
в разделе [Формат загрузки данных](../../Формат_загрузки_данных/Формат_загрузки_данных.md).

Запрос обрабатывается в порядке, описанном в разделе [Порядок обработки запросов на загрузку данных](../../../Обзор_понятий_компонентов_и_связей/Связи_с_другими_системами_и_компонентами/Порядок_обработки_запросов_на_загрузку_данных/Порядок_обработки_запросов_на_загрузку_данных.md).

В ответе возвращается:
*   пустой объект ResultSet при успешном выполнении запроса;
*   исключение при неуспешном выполнении запроса.

При успешном выполнении запроса данные загружаются в СУБД [хранилища](../../../Обзор_понятий_компонентов_и_связей/Основные_понятия/Хранилище_данных/Хранилище_данных.md), 
выбранные для размещения данных таблицы. Месторасположение данных таблицы можно задавать запросами 
[CREATE TABLE](../CREATE_TABLE/CREATE_TABLE.md) и [DROP TABLE](../DROP_TABLE/DROP_TABLE.md).

**Примечания**

*   Перед выполнением запроса необходимо создать [внешнюю таблицу](../../../Обзор_понятий_компонентов_и_связей/Основные_понятия/Внешняя_таблица/Внешняя_таблица.md), 
    загрузить данные в топик Kafka и открыть [дельту](../../../Обзор_понятий_компонентов_и_связей/Основные_понятия/Дельта/Дельта.md). 
    Подробнее о порядке выполнения действий для загрузки данных см. в разделе [Загрузка данных](../../../Работа_с_системой/Загрузка_данных/Загрузка_данных.md).
*   Имена и порядок следования столбцов должны совпадать в топике Kafka, внешней таблице загрузке и 
    запросе на загрузку данных. Исключением является служебное поле `sys_op`, которое обязательно 
    для топика и опционально для внешней таблицы, но должно отсутствовать в запросе на загрузку данных 
    при явном перечислении столбцов. Подробнее о требованиях к загружаемым данным см. в разделе 
    [Формат загрузки данных](../../Формат_загрузки_данных/Формат_загрузки_данных.md).

## Синтаксис

Запрос с явным перечислением столбцов внешней таблицы:
```sql
INSERT INTO [db_name.]table_name
SELECT column_list FROM [db_name.]ext_table_name
```

Запрос с использованием символа `*`:
```sql
INSERT INTO [db_name.]table_name SELECT * FROM [db_name.]ext_table_name
```

## Параметры

*   `db_name` — имя логической базы данных. Опционально, если выбрана логическая БД, 
    [используемая по умолчанию](../../../Работа_с_системой/Другие_функции/Определение_логической_БД_по_умолчанию/Определение_логической_БД_по_умолчанию.md);
*   `table_name` — имя логической таблицы, в которую загружаются данные;
*   `column_list` — список имен столбцов внешней таблицы загрузки. Должен включать все имена столбцов 
    логической таблицы. Если внешняя таблица содержит служебное поле `sys_op`, оно не указывается;
*   `ext_table_name` — имя внешней таблицы загрузки.

## Ограничения

Выполнение запроса возможно только при наличии открытой дельты 
(см. [BEGIN DELTA](../BEGIN_DELTA/BEGIN_DELTA.md)).

## Пример

Пример загрузки данных с открытием и закрытием дельты:
```sql
BEGIN DELTA

INSERT INTO sales.sales
SELECT * FROM sales.sales_ext_upload

COMMIT DELTA
```