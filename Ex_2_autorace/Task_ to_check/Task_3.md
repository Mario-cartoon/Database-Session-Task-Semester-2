# Task 3

## Условие

Определить классы автомобилей, которые имеют наименьшую среднюю позицию в гонках, и вывести информацию о каждом автомобиле из этих классов.

В результате нужно показать:

- название автомобиля;
- класс автомобиля;
- среднюю позицию автомобиля;
- количество гонок, в которых автомобиль участвовал;
- страну производства класса автомобиля;
- общее количество гонок, в которых участвовали автомобили данного класса.

Если несколько классов имеют одинаковую наименьшую среднюю позицию, необходимо вывести все такие классы.

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
ClassAvg AS (
    SELECT
        car_class,
        AVG(average_position) AS class_avg_position
    FROM CarStats
    GROUP BY car_class
),
MinClassAvg AS (
    SELECT MIN(class_avg_position) AS min_avg
    FROM ClassAvg
),
TargetClasses AS (
    SELECT car_class
    FROM ClassAvg
    WHERE class_avg_position = (SELECT min_avg FROM MinClassAvg)
)
SELECT
    cs.car_name,
    cs.car_class,
    ROUND(cs.average_position, 4) AS average_position,
    cs.race_count,
    cl.country AS car_country,
    (
        SELECT COUNT(DISTINCT race)
        FROM Results r2
        JOIN Cars c2 ON r2.car = c2.name
        WHERE c2.class = cs.car_class
    ) AS total_races
FROM CarStats cs
JOIN Classes cl ON cs.car_class = cl.class
WHERE cs.car_class IN (SELECT car_class FROM TargetClasses)
ORDER BY cs.average_position;
```
## Ожидаемый вывод для тестовых данных

| car_name       | car_class    | average_position | race_count | car_country | total_races |
|----------------|--------------|------------------|------------|-------------|-------------|
| Ferrari 488    | Convertible  | 1.0000           | 1          | Italy       | 1           |
| Ford Mustang   | SportsCar    | 1.0000           | 1          | USA         | 1           |