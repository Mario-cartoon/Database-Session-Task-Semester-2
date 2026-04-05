# Task 2

## Условие

Определить автомобиль, который имеет наименьшую среднюю позицию в гонках среди всех автомобилей, и вывести информацию об этом автомобиле.

В результате нужно показать:

- название автомобиля;
- класс автомобиля;
- среднюю позицию;
- количество гонок, в которых автомобиль участвовал;
- страну производства класса автомобиля.

Если несколько автомобилей имеют одинаковую наименьшую среднюю позицию, необходимо выбрать только один автомобиль по алфавиту, то есть по имени автомобиля.

## Решение

```sql
WITH CarStat AS (
    SELECT
        c.name AS car_name,
        c.class AS car_class,
        AVG(r.position) AS average_position,
        COUNT(r.race) AS race_count
    FROM Cars c
    JOIN Results r ON c.name = r.car
    GROUP BY c.name, c.class
)
SELECT
    cs.car_name,
    cs.car_class,
    ROUND(cs.average_position, 4) AS average_position,
    cs.race_count,
    cl.country AS car_country
FROM CarStat cs
JOIN Classes cl ON cs.car_class = cl.class
ORDER BY cs.average_position, cs.car_name
LIMIT 1;
```

## Ожидаемый вывод для тестовых данных

| car_name       | car_class    | average_position | race_count | car_country |
|----------------|--------------|------------------|------------|-------------|
| Ferrari 488    | Convertible  | 1.0000           | 1          | Italy       |