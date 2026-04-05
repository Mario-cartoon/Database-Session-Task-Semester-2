# Task 1

## Условие

Определить, какие автомобили из каждого класса имеют наименьшую среднюю позицию в гонках, и вывести информацию о каждом таком автомобиле для соответствующего класса.

В результате нужно показать:

- название автомобиля;
- класс автомобиля;
- среднюю позицию в гонках;
- количество гонок, в которых автомобиль участвовал.

Результат необходимо отсортировать по средней позиции.

## Решение

```sql
WITH RankedCars AS (
    SELECT
        c.name AS car_name,
        c.class AS car_class,
        AVG(r.position) AS average_position,
        COUNT(r.race) AS race_count,
        RANK() OVER (
            PARTITION BY c.class
            ORDER BY AVG(r.position)
        ) AS rnk
    FROM Cars c
    JOIN Results r ON c.name = r.car
    GROUP BY c.name, c.class
)
SELECT
    car_name,
    car_class,
    ROUND(average_position, 4) AS average_position,
    race_count
FROM RankedCars
WHERE rnk = 1
ORDER BY average_position;
```
## Ожидаемый вывод для тестовых данных

| car_name                 | car_class      | average_position | race_count |
|--------------------------|----------------|------------------|------------|
| Ferrari 488              | Convertible    | 1.0000           | 1          |
| Ford Mustang             | SportsCar      | 1.0000           | 1          |
| Toyota RAV4              | SUV            | 2.0000           | 1          |
| Mercedes-Benz S-Class    | Luxury Sedan   | 2.0000           | 1          |
| BMW 3 Series             | Sedan          | 3.0000           | 1          |
| Chevrolet Camaro         | Coupe          | 4.0000           | 1          |
| Renault Clio             | Hatchback      | 5.0000           | 1          |
| Ford F-150               | Pickup         | 6.0000           | 1          |
