# Тест SQL

На основе таблиц базы данных, напишите SQL код, который возвращает необходимые результаты
Пример: 

Общее количество товаров
```sql
select count (*) from items
```

## Структура данных

Используемый синтаксис: Oracle SQL или другой

| Сustomer       | Description           |
| -------------- | --------------------- |
| customer\_id   | customer unique id    |
| customer\_name | customer name         |
| country\_code  | country code ISO 3166 |

| Items             | Description       |
| ----------------- | ----------------- |
| item\_id          | item unique id    |
| item\_name        | item name         |
| item\_description | item description  |
| item\_price       | item price in USD |

| Orders       | Description                 |
| ------------ | --------------------------- |
| date\_time   | date and time of the orders |
| item\_id     | item unique id              |
| customer\_id | user unique id              |
| quantity     | number of items in order    |

| Countries     | Description           |
| ------------- | --------------------- |
| country\_code | country code          |
| country\_name | country name          |
| country\_zone | AMER, APJ, LATAM etc. |


| Сonnection\_log         | Description                           |
| ----------------------- | ------------------------------------- |
| customer\_id            | customer unique id                    |
| first\_connection\_time | date and time of the first connection |
| last\_connection\_time  | date and time of the last connection  |

## Задания

### 1) Общее количество покупателей

| **CustomerCountDistinct** |
| ----------------------------- |
| #                             |

```sql
SELECT COUNT(DISTINCT  customer_id) as CustomerCountDistinct
FROM Customer;
```

### 2) Количество покупателей из Италии и Франции

| **Country_name** | **CustomerCountDistinct** |
| ------------------------- | ----------------------------- |
| France                    | #                             |
| Italy                     | #                             |

```sql
SELECT Country_name, COUNT(DISTINCT(customer_id))
FROM Customer INNER JOIN Countries USING(country_code)
WHERE country_name IN ("France", "Italy");
```

### 3) ТОП 10 покупателей по расходам

| **Customer_name** | **Revenue** |
| ---------------------- | ----------- |
| #                      | #           |
| #                      | #           |
| #                      | #           |
| #                      | #           |
| #                      | #           |
| #                      | #           |
| #                      | #           |

```sql
SELECT Customer_name, SUM(quantity*item_price) as Revenue
FROM Customer INNER JOIN USING(customer_id)
INNER JOIN Items USING(item_id)
GROUP BY Customer_name
ORDER BY Revenue DESC
LIMIT 10;
```

### 4) Общая выручка USD по странам, если нет дохода, вернуть NULL

| **Country_name** | **RevenuePerCountry** |
| ------------------------- | --------------------- |
| Italy                     | #                     |
| France                    | NULL                  |
| Mexico                    | #                     |
| Germany                   | #                     |
| Tanzania                  | #                     |

```sql
SELECT Country_name, IF(SUM(quantity*item_price) IS NONE, NULL, SUM(quantity*item_price)) as "RevenuePerCountry"
FROM Countries LEFT JOIN Customer USING(country_code)
INNER JOIN Orders USING(customer_id)
GROUP BY Country_name;
```

### 5) Самый дорогой товар, купленный одним покупателем

| **Customer\_id** | **Customer\_name** | **MostExpensiveItemName** |
| ---------------- | ------------------ | ------------------------- |
| #                | #                  | #                         |
| #                | #                  | #                         |
| #                | #                  | #                         |
| #                | #                  | #                         |
| #                | #                  | #                         |
| #                | #                  | #                         |
| #                | #                  | #                         |

```sql
SELECT Customer_id, Customer_name, item_name as "MostExpensiveItemName"
FROM Customer INNER JOIN Orders USING(customer_id)
INNER JOIN Items USING(item_id)
GROUP BY 1, 2, 3
ORDER BY MAX(item_price) DESC;
```

### 6) Ежемесячный доход

| **Month (MM format)** | **Total Revenue** |
| --------------------- | ----------------- |
| #                     | #                 |
| #                     | #                 |
| #                     | #                 |
| #                     | #                 |
| #                     | #                 |
| #                     | #                 |
| #                     | #                 |

```sql
-- for only one year
SELECT EXTRACT(MONTH FROM date_time), SUM(quantity*item_price) as "Total Revenue"
FROM Orders INNER JOIN Items USING(item_id)
GROUP BY 1;
```

### 7) Общий доход в MENA

| **Total Revenue MENA** |
| ---------------------- |
| #                      |

```sql
SELECT SUM(quantity*item_price) as "Total Revenu MENA"
FROM Countries LEFT JOIN Customer USING(country_code)
INNER JOIN Orders USING(customer_id)
WHERE country_name = "MENA"
GROUP BY 1;
```

### 8) Найти дубликаты

Во время передачи данных произошел сбой, в таблице orders появилось несколько 
дубликатов (несколько результатов возвращаются для date_time + customer_id + item_id). 
Вы должны их найти и вернуть количество дубликатов.

```sql
SELECT date_time, customer_id, item_id, count(*)
FROM Orders
GROUP BY date_time, customer_id, item_id
HAVING count(*)>1;
```
