# Task 3

## Условие

Необходимо провести анализ данных о бронированиях в отелях и определить предпочтения клиентов по типу отелей.

### Категоризация отелей

Категория каждого отеля определяется по средней стоимости номера:

- `Дешевый` — средняя стоимость менее 175 долларов;
- `Средний` — средняя стоимость от 175 до 300 долларов;
- `Дорогой` — средняя стоимость более 300 долларов.

### Анализ предпочтений клиентов

Для каждого клиента нужно определить предпочитаемый тип отеля:

- если у клиента есть хотя бы один дорогой отель, категория будет `Дорогой`;
- если дорогих отелей нет, но есть хотя бы один средний, категория будет `Средний`;
- если есть только дешевые отели, категория будет `Дешевый`.

### Вывод информации

Для каждого клиента нужно вывести:

- `ID_customer`;
- имя клиента;
- предпочитаемый тип отеля;
- список уникальных отелей, которые посетил клиент.

### Сортировка результатов

Результаты нужно отсортировать так, чтобы сначала шли клиенты с дешевыми отелями, затем со средними и в конце с дорогими.

## Решение

```sql
WITH HotelCategories AS (
    SELECT
        h.ID_hotel,
        h.name,
        CASE
            WHEN AVG(r.price) < 175 THEN 'Дешевый'
            WHEN AVG(r.price) <= 300 THEN 'Средний'
            ELSE 'Дорогой'
        END AS hotel_category,
        CASE
            WHEN AVG(r.price) < 175 THEN 1
            WHEN AVG(r.price) <= 300 THEN 2
            ELSE 3
        END AS category_sort
    FROM Hotel h
    JOIN Room r ON h.ID_hotel = r.ID_hotel
    GROUP BY h.ID_hotel, h.name
),
CustomerAnalysis AS (
    SELECT
        c.ID_customer,
        c.name,
        MAX(hc.category_sort) AS max_category_sort,
        CASE
            WHEN MAX(hc.category_sort) = 3 THEN 'Дорогой'
            WHEN MAX(hc.category_sort) = 2 THEN 'Средний'
            ELSE 'Дешевый'
        END AS preferred_hotel_type,
        GROUP_CONCAT(DISTINCT hc.name ORDER BY hc.name SEPARATOR ',') AS visited_hotels
    FROM Customer c
    JOIN Booking b ON c.ID_customer = b.ID_customer
    JOIN Room r ON b.ID_room = r.ID_room
    JOIN HotelCategories hc ON r.ID_hotel = hc.ID_hotel
    GROUP BY c.ID_customer, c.name
)
SELECT
    ID_customer,
    name,
    preferred_hotel_type,
    visited_hotels
FROM CustomerAnalysis
ORDER BY max_category_sort ASC, ID_customer ASC;
```
## Ожидаемый вывод для тестовых данных

| ID_customer | name              | preferred_hotel_type | visited_hotels                                |
|-------------|-------------------|----------------------|-----------------------------------------------|
| 10          | Hannah Montana    | Дешевый              | City Center Inn                               |
| 1           | John Doe          | Средний              | City Center Inn, Grand Hotel                  |
| 2           | Jane Smith        | Средний              | Grand Hotel                                   |
| 3           | Alice Johnson     | Средний              | Grand Hotel                                   |
| 4           | Bob Brown         | Средний              | Grand Hotel, Ocean View Resort                |
| 5           | Charlie White     | Средний              | Ocean View Resort                             |
| 6           | Diana Prince      | Средний              | Ocean View Resort                             |
| 7           | Ethan Hunt        | Дорогой              | Mountain Retreat, Ocean View Resort           |
| 8           | Fiona Apple       | Дорогой              | Mountain Retreat                              |
| 9           | George Washington | Дорогой              | City Center Inn, Mountain Retreat             |