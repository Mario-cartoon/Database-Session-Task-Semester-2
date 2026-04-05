# Task 4

## Условие

Определить, какие автомобили имеют среднюю позицию лучше, чем средняя позиция всех автомобилей в их классе.

Учитываются только те классы, в которых есть минимум два автомобиля.

В результате нужно показать:

- название автомобиля;
- класс автомобиля;
- среднюю позицию;
- количество гонок, в которых автомобиль участвовал;
- страну производства класса автомобиля.

Результат необходимо отсортировать сначала по классу автомобиля, а затем по средней позиции в порядке возрастания.

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
        AVG(average_position) AS class_avg_position,
        COUNT(*) AS cars_in_class
    FROM CarStats
    GROUP BY car_class
    HAVING COUNT(*) >= 2
)
SELECT
    cs.car_name,
    cs.car_class,
    ROUND(cs.average_position, 4) AS average_position,
    cs.race_count,
    cl.country AS car_country
FROM CarStats cs
JOIN ClassAvg ca ON cs.car_class = ca.car_class
JOIN Classes cl ON cs.car_class = cl.class
WHERE cs.average_position < ca.class_avg_position
ORDER BY cs.car_class, cs.average_position;
```
## Ожидаемый вывод для тестовых данных

| car_name       | car_class | average_position | race_count | car_country |
|----------------|-----------|------------------|------------|-------------|
| BMW 3 Series   | Sedan     | 3.0              | 1          | Germany     |
| Toyota RAV4    | SUV       | 2.0000           | 1          | Japan       |