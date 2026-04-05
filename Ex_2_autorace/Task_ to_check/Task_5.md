# Task 5

## Условие

Определить, какие классы автомобилей имеют наибольшее количество автомобилей с низкой средней позицией, то есть со средней позицией больше `3.0`.

Для найденных классов нужно вывести информацию о каждом автомобиле.

В результате нужно показать:

- название автомобиля;
- класс автомобиля;
- среднюю позицию;
- количество гонок, в которых автомобиль участвовал;
- страну производства класса автомобиля;
- общее количество гонок для каждого класса.

Результат необходимо отсортировать по количеству автомобилей с низкой средней позицией.

## Решение

```sql
WITH CarStats AS (
    SELECT
        c.name AS car_name,
        c.class AS car_class,
        AVG(r.position) AS average_position,
        COUNT(r.race) AS race_count
    FROM Cars c
    JOIN Results r ON c.name = r.car
    GROUP BY c.name, c.class
),
ClassLowCount AS (
    SELECT
        car_class,
        COUNT(*) AS low_position_cars,
        RANK() OVER (ORDER BY COUNT(*) DESC) AS rnk
    FROM CarStats
    WHERE average_position > 3.0
    GROUP BY car_class
),
ClassRaces AS (
    SELECT
        c.class AS car_class,
        COUNT(DISTINCT r.race) AS total_races
    FROM Results r
    JOIN Cars c ON r.car = c.name
    GROUP BY c.class
)
SELECT
    cs.car_name,
    cs.car_class,
    ROUND(cs.average_position, 4) AS average_position,
    cs.race_count,
    cl.country AS car_country,
    cr.total_races
FROM CarStats cs
JOIN ClassLowCount clc ON cs.car_class = clc.car_class AND clc.rnk = 1
JOIN Classes cl ON cs.car_class = cl.class
JOIN ClassRaces cr ON cs.car_class = cr.car_class
WHERE cs.average_position > 3.0
ORDER BY clc.low_position_cars DESC, cs.car_class, cs.average_position;
```
## Ожидаемый вывод для тестовых данных

| car_name           | car_class    | average_position | race_count | car_country | total_races | low_position_count |
|--------------------|--------------|------------------|------------|-------------|-------------|--------------------|
| Audi A4            | Sedan        | 8.0000           | 1          | Germany     | 2           | 2                  |
| Chevrolet Camaro   | Coupe        | 4.0000           | 1          | USA         | 1           | 1                  |
| Renault Clio       | Hatchback    | 5.0000           | 1          | France      | 1           | 1                  |
| Ford F-150         | Pickup       | 6.0000           | 1          | USA         | 1           | 1                  |