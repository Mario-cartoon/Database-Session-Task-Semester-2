# Task 1

## Условие

Определить, какие клиенты сделали более двух бронирований в разных отелях, и вывести информацию о каждом таком клиенте.

В результате нужно показать:

- имя клиента;
- электронную почту;
- телефон;
- общее количество бронирований;
- количество уникальных отелей;
- список отелей, в которых клиент бронировал номера;
- среднюю длительность пребывания в днях.

Результат необходимо отсортировать по количеству бронирований в порядке убывания.

## Решение

```sql
WITH CustomerBookings AS (
    SELECT
        c.ID_customer,
        c.name,
        c.email,
        c.phone,
        COUNT(b.ID_booking) AS total_bookings,
        COUNT(DISTINCT h.ID_hotel) AS unique_hotels,
        GROUP_CONCAT(DISTINCT h.name ORDER BY h.name SEPARATOR ', ') AS hotels_list,
        AVG(DATEDIFF(b.check_out_date, b.check_in_date)) AS avg_stay_duration
    FROM Customer c
    JOIN Booking b ON c.ID_customer = b.ID_customer
    JOIN Room r ON b.ID_room = r.ID_room
    JOIN Hotel h ON r.ID_hotel = h.ID_hotel
    GROUP BY c.ID_customer, c.name, c.email, c.phone
    HAVING COUNT(b.ID_booking) > 2
       AND COUNT(DISTINCT h.ID_hotel) > 1
)
SELECT
    name,
    email,
    phone,
    total_bookings,
    unique_hotels,
    hotels_list,
    ROUND(avg_stay_duration, 1) AS avg_stay_duration
FROM CustomerBookings
ORDER BY total_bookings DESC, avg_stay_duration DESC;
```
## Ожидаемый вывод для тестовых данных

| name        | email                        | phone         | total_bookings | hotels_visited                              | avg_nights |
|-------------|------------------------------|---------------|----------------|---------------------------------------------|------------|
| Bob Brown   | bob.brown@example.com        | +2233445566   | 3              | Grand Hotel, Ocean View Resort              | 3.0000     |
| Ethan Hunt  | ethan.hunt@example.com       | +5566778899   | 3              | Mountain Retreat, Ocean View Resort         | 3.0000     |