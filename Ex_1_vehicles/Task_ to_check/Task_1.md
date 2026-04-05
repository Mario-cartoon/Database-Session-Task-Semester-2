# Task 1

## Условие

Найдите производителей (`maker`) и модели всех мотоциклов, которые:

- имеют мощность более 150 лошадиных сил;
- стоят менее 20 000 долларов;
- относятся к спортивному типу (`Sport`).

Результат необходимо отсортировать по мощности в порядке убывания.

## Решение

```sql
SELECT
    v.maker,
    m.model
FROM
    Motorcycle m
JOIN
    Vehicle v ON m.model = v.model
WHERE
    m.horsepower > 150
    AND m.price < 20000.00
    AND m.type = 'Sport'
ORDER BY
    m.horsepower DESC;
```
## Ожидаемый вывод для тестовых данных

| maker  | model   |
|--------|---------|
| Yamaha | YZF-R1  |