# Scripts to Create Tables

В этом файле собраны SQL-скрипты для создания таблиц базы данных по теме `booking`.

## 1. Создание таблицы `Hotel`

```sql
CREATE TABLE Hotel (
    ID_hotel INT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    location VARCHAR(255) NOT NULL
);
```

## 2. Создание таблицы `Room`

```sql
CREATE TABLE Room (
    ID_room INT PRIMARY KEY,
    ID_hotel INT,
    room_type ENUM('Single', 'Double', 'Suite') NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    capacity INT NOT NULL,
    FOREIGN KEY (ID_hotel) REFERENCES Hotel(ID_hotel)
);
```

## 3. Создание таблицы `Customer`

```sql
CREATE TABLE Customer (
    ID_customer INT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    phone VARCHAR(20) NOT NULL
);
```

## 4. Создание таблицы `Booking`

```sql
CREATE TABLE Booking (
    ID_booking INT PRIMARY KEY,
    ID_room INT,
    ID_customer INT,
    check_in_date DATE NOT NULL,
    check_out_date DATE NOT NULL,
    FOREIGN KEY (ID_room) REFERENCES Room(ID_room),
    FOREIGN KEY (ID_customer) REFERENCES Customer(ID_customer)
);
```
