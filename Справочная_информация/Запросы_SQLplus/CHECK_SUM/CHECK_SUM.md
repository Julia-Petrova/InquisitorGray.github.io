﻿---
layout: default
title: CHECK_SUM
nav_order: 5
parent: Запросы SQL+
grand_parent: Справочная информация
has_children: false
has_toc: false
---

# CHECK_SUM

Запрос позволяет проверить равенство контрольных сумм для указанной [дельты](../../../Обзор_понятий_компонентов_и_связей/Основные_понятия/Дельта/Дельта.md) 
в различных СУБД хранилища и, тем самым, проверить идентичность данных в этих СУБД.

Доступны следующие области проверки:
*   все [логические таблицы](../../../Обзор_понятий_компонентов_и_связей/Основные_понятия/Логическая_таблица/Логическая_таблица.md) 
    [логической базы данных](../../../Обзор_понятий_компонентов_и_связей/Основные_понятия/Логическая_база_данных/Логическая_база_данных.md),
*   все столбцы логической таблицы,
*   отдельные столбцы логической таблицы.

Проверка контрольной суммы данных каждой логической таблицы производится сравнением контрольных сумм, 
вычисленных по соответствующим данным, содержащимся в каждой из целевых СУБД.

Контрольная сумма данных формируется по хеш-функции от символьной контрольной строки для этих данных. 
Контрольная строка для данных всех логических таблиц формируется конкатенацией контрольных строк для 
данных каждой таблицы. Контрольная строка для данных таблицы формируется конкатенацией контрольных сумм
для данных в столбцах из указанного списка. Контрольные суммы для данных в столбцах сопоставляются между 
целевыми СУБД.

В ответе возвращается:
*   64-битное неотрицательное целочисленное значение совпадающих контрольных сумм указанных данных 
    в разных СУБД, при успешном выполнении запроса;
*   сообщение о несовпадении контрольных сумм указанных данных в разных СУБД при успешном выполнении 
    запроса или сообщение о неуспешном выполнении запроса.
    
## Синтаксис

```sql
CHECK_SUM(delta_num[, [db_name.]table_name[, square-bracketed_column_list]])
```

## Параметры

*   `delta_num` — номер горячей или закрытой [дельты](../../../Обзор_понятий_компонентов_и_связей/Основные_понятия/Дельта/Дельта.md), 
    в рамках которой вычисляется контрольная сумма. Если других аргументов не указано, контрольная сумма 
    вычисляется для данных, хранимых во всех [логических таблицах](../../../Обзор_понятий_компонентов_и_связей/Основные_понятия/Логическая_таблица/Логическая_таблица.md) 
    [логической БД](../../../Обзор_понятий_компонентов_и_связей/Основные_понятия/Логическая_база_данных/Логическая_база_данных.md), 
    [используемой по умолчанию](../../../Работа_с_системой/Другие_функции/Определение_логической_БД_по_умолчанию/Определение_логической_БД_по_умолчанию.md);
*   `table_name` — опционально указываемое имя логической таблицы, для данных которой вычисляется 
    контрольная сумма;
*   `square-bracketed_column_list` — опциональный список имён столбцов указанной логической таблицы, 
    для данных которых вычисляется контрольная сумма. Элементы списка перечисляются внутри квадратных 
    скобок через запятую. Если список столбцов не указан, контрольная сумма вычисляется по всем столбцам 
    логической таблицы.
    
## Ограничения

*   Не предоставляется строгой гарантии отсутствия ложноположительных результатов проверки контрольной 
    суммы.

## Примеры

Проверка контрольной суммы логической базы данных `sales` в рамках нулевой дельты:
```sql
USE sales
CHECK_SUM(0)
```

Проверка контрольной суммы столбцов логической таблицы `stores` в рамках нулевой дельты:
```sql
CHECK_SUM(0,sales.stores,[region, address])
```