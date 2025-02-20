﻿---
layout: default
title: CREATE TABLE
nav_order: 12
parent: Запросы SQL+
grand_parent: Справочная информация
has_children: false
has_toc: false
---

# CREATE TABLE

Запрос позволяет создать [логическую таблицу](../../../Обзор_понятий_компонентов_и_связей/Основные_понятия/Логическая_таблица/Логическая_таблица.md) 
в [логической базе данных](../../../Обзор_понятий_компонентов_и_связей/Основные_понятия/Логическая_база_данных/Логическая_база_данных.md). 
В зависимости от параметров запроса данные логической таблицы размещаются в указанных или всех 
[СУБД](../../../Введение/Поддерживаемые_СУБД_хранилища/Поддерживаемые_СУБД_хранилища.md) 
[хранилища](../../../Обзор_понятий_компонентов_и_связей/Основные_понятия/Хранилище_данных/Хранилище_данных.md).

Рекомендуется создавать логическую таблицу с размещением данных, как минимум, в СУБД, которая выбрана 
для выгрузки данных в [конфигурации](../../../Эксплуатация/Конфигурация/Конфигурация.md) системы. 
Данные могут размещаться в любых СУБД хранилища, однако, если они размещаются вне СУБД выгрузки, 
их выгрузка недоступна.

В ответе возвращается:
*   пустой объект ResultSet при успешном выполнении запроса;
*   исключение при неуспешном выполнении запроса.

При успешном выполнении запроса появляется новая таблица в [логической схеме данных](../../../Обзор_понятий_компонентов_и_связей/Основные_понятия/Логическая_схема_данных/Логическая_схема_данных.md). 
Соответствующие [физические таблицы](../../../Обзор_понятий_компонентов_и_связей/Основные_понятия/Физическая_таблица/Физическая_таблица.md) 
появляются в СУБД хранилища, указанных в запросе, или, если СУБД не указаны в запросе, — 
во всех СУБД хранилища.

**Примечание:** изменение логической таблицы недоступно. Для замены некорректной таблицы необходимо 
удалить ее и создать новую.

## Синтаксис

```sql
CREATE TABLE [db_name.]table_name (
  column_name_1 datatype_1 NOT NULL,
  column_name_2 datatype_2 DEFAULT default_value_2,
  column_name_3 datatype_3,
  PRIMARY KEY (column_list_1)
) DISTRIBUTED BY (column_list_2)
DATASOURCE_TYPE (datasource_aliases)
```

## Параметры

*   `db_name` — имя логической базы данных, в которой создается логическая таблица. Указывается 
    опционально, если выбрана логическая БД, [используемая по умолчанию](../../../Работа_с_системой/Другие_функции/Определение_логической_БД_по_умолчанию/Определение_логической_БД_по_умолчанию.md);
*   `table_name` — имя создаваемой логической таблицы, уникальное среди логических сущностей логической БД;
*   `column_name_N` — имя столбца таблицы;
*   `datatype_N` — тип данных столбца `column_name_N`. Возможные значения см. 
    в разделе [Логические типы данных](../../Поддерживаемые_типы_данных/Логические_типы_данных/Логические_типы_данных.md);
*   `default_value_N` — значение столбца `column_name_N` по умолчанию;
*   `column_list_1` — список столбцов, входящих в первичный ключ таблицы;
*   `column_list_2` — список столбцов целочисленного типа, входящих в ключ шардирования таблицы. Столбцы 
    должны быть из числа столбцов `column_list_1`;
*   `datasource_aliases` — список псевдонимов СУБД хранилища, в которых нужно разместить данные таблицы. 
    Элементы списка перечисляются через запятую. Возможные значения: `adb`, `adqm`, `adg`.

## Ограничения

*   Имена столбцов должны быть уникальны в рамках логической таблицы.
*   Недопустимо использование зарезервированных имен столбцов: `sys_op`, `sys_from`, `sys_to`, 
    `sys_close_date`, `bucket_id`, `sign`.
*   Первичный ключ должен включать все столбцы ключа шардирования.
*   Ключ шардирования может содержать только целочисленные столбцы.

## Примеры

Создание логической таблицы с размещением данных во всех СУБД хранилища:
```sql
CREATE TABLE sales.sales (
  identification_number INT NOT NULL,
  transaction_date TIMESTAMP NOT NULL,
  product_code VARCHAR(256) NOT NULL,
  product_units INT NOT NULL,
  store_id INT NOT NULL,
  description VARCHAR(256),
  PRIMARY KEY (identification_number)
)
DISTRIBUTED BY (identification_number)
```

Создание логической таблицы с составным первичным ключом и размещением данных во всех СУБД хранилища:
```sql
CREATE TABLE sales.stores (
  identification_number INT NOT NULL,
  category VARCHAR(256) NOT NULL,
  region VARCHAR(256) NOT NULL,
  address VARCHAR(256) NOT NULL,
  description VARCHAR(256),
  PRIMARY KEY (identification_number, region)
)
DISTRIBUTED BY (identification_number)
```

Создание логической таблицы с размещением данных в ADQM и ADG (без размещения в ADB):
```sql
CREATE TABLE sales.clients (
  identification_number INT NOT NULL,
  first_name VARCHAR(256) NOT NULL,
  last_name VARCHAR(256) NOT NULL,
  patronymic_name VARCHAR(256),
  birth_date DATE,
  PRIMARY KEY (identification_number)
) DISTRIBUTED BY (identification_number)
DATASOURCE_TYPE (adqm,adg)
```