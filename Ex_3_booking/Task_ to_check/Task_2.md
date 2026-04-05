# Task 2

## Условие

Необходимо провести анализ клиентов, которые сделали более двух бронирований в разных отелях и потратили более 500 долларов на свои бронирования.

В результате нужно определить клиентов, которые:

- сделали более двух бронирований;
- бронировали номера более чем в одном отеле;
- потратили на бронирования более 500 долларов.

Для каждого такого клиента нужно вывести:

- `ID_customer`;
- имя;
- общее количество бронирований;
- общую сумму, потраченную на бронирования;
- количество уникальных отелей.

Результаты необходимо отсортировать по общей сумме затрат в порядке возрастания.

## Решение

```sql
SELECT
    c.ID_customer,
    c.name,
    COUNT(b.ID_booking) AS total_bookings,
    ROUND(SUM(r.price), 2) AS total_spent,
    COUNT(DISTINCT h.ID_hotel) AS unique_hotels
FROM Customer c
JOIN Booking b ON c.ID_customer = b.ID_customer
JOIN Room r ON b.ID_room = r.ID_room
JOIN Hotel h ON r.ID_hotel = h.ID_hotel
GROUP BY c.ID_customer, c.name
HAVING COUNT(b.ID_booking) > 2
   AND COUNT(DISTINCT h.ID_hotel) > 1
   AND SUM(r.price) > 500
ORDER BY total_spent ASC;
```
## Ожидаемый вывод для тестовых данных

| ID_customer | name        | total_bookings | total_spent | unique_hotels |
|-------------|-------------|----------------|-------------|---------------|
| 4           | Bob Brown   | 3              | 820.00      | 2             |
| 7           | Ethan Hunt  | 3              | 850.00      | 2             |