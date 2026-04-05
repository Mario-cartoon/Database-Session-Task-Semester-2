# Task 2

## Условие

Найти информацию о производителях и моделях различных типов транспортных средств, которые соответствуют заданным критериям.

### Автомобили

Нужно извлечь данные обо всех автомобилях, которые:

- имеют мощность двигателя более 150 лошадиных сил;
- имеют объем двигателя менее 3 литров;
- стоят менее 35 000 долларов.

В результате должны быть указаны:

- производитель (`maker`);
- модель (`model`);
- мощность (`horsepower`);
- объем двигателя (`engine_capacity`);
- тип транспортного средства, обозначенный как `Car`.

### Мотоциклы

Нужно извлечь данные обо всех мотоциклах, которые:

- имеют мощность двигателя более 150 лошадиных сил;
- имеют объем двигателя менее 1.5 литра;
- стоят менее 20 000 долларов.

В результате должны быть указаны:

- производитель (`maker`);
- модель (`model`);
- мощность (`horsepower`);
- объем двигателя (`engine_capacity`);
- тип транспортного средства, обозначенный как `Motorcycle`.

### Велосипеды

Нужно извлечь данные обо всех велосипедах, которые:

- имеют количество передач больше 18;
- стоят менее 4 000 долларов.

В результате должны быть указаны:

- производитель (`maker`);
- модель (`model`);
- значение `NULL` для мощности;
- значение `NULL` для объема двигателя;
- тип транспортного средства, обозначенный как `Bicycle`.

### Сортировка

Результаты нужно объединить в один набор данных и отсортировать по мощности в порядке убывания. Велосипеды, у которых значение мощности отсутствует, должны располагаться внизу списка.

## Решение

```sql
SELECT
    v.maker,
    m.model,
    m.horsepower,
    m.engine_capacity,
    'Motorcycle' AS vehicle_type
FROM Motorcycle m
JOIN Vehicle v ON m.model = v.model
WHERE m.horsepower > 150
  AND m.engine_capacity < 1.5
  AND m.price < 20000.00

UNION ALL

SELECT
    v.maker,
    c.model,
    c.horsepower,
    c.engine_capacity,
    'Car' AS vehicle_type
FROM Car c
JOIN Vehicle v ON c.model = v.model
WHERE c.horsepower > 150
  AND c.engine_capacity < 3.0
  AND c.price < 35000.00

UNION ALL

SELECT
    v.maker,
    b.model,
    NULL AS horsepower,
    NULL AS engine_capacity,
    'Bicycle' AS vehicle_type
FROM Bicycle b
JOIN Vehicle v ON b.model = v.model
WHERE b.gear_count > 18
  AND b.price < 4000.00
ORDER BY horsepower DESC
```


## Ожидаемый вывод для тестовых данных

| maker    | model       | horsepower | engine_capacity | vehicle_type |
|----------|-------------|------------|----------------|--------------|
| Toyota   | Camry       | 203        | 2.50           | Car          |
| Yamaha   | YZF-R1      | 200        | 1.00           | Motorcycle   |
| Honda    | Civic       | 158        | 2.00           | Car          |
| Trek     | Domane      | NULL       | NULL           | Bicycle      |
| Giant    | Defy        | NULL       | NULL           | Bicycle      |