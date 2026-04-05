# Scripts to Create Tables

В этом файле собраны SQL-скрипты для создания таблиц базы данных по теме `orgstructure`.

## 1. Создание таблицы `Departments`

```sql
CREATE TABLE Departments (
    DepartmentID INT PRIMARY KEY,
    DepartmentName VARCHAR(100) NOT NULL
);
```

## 2. Создание таблицы `Roles`

```sql
CREATE TABLE Roles (
    RoleID INT PRIMARY KEY,
    RoleName VARCHAR(100) NOT NULL
);
```

## 3. Создание таблицы `Employees`

```sql
CREATE TABLE Employees (
    EmployeeID INT PRIMARY KEY,
    Name VARCHAR(100) NOT NULL,
    Position VARCHAR(100),
    ManagerID INT,
    DepartmentID INT,
    RoleID INT,
    FOREIGN KEY (ManagerID) REFERENCES Employees(EmployeeID),
    FOREIGN KEY (DepartmentID) REFERENCES Departments(DepartmentID),
    FOREIGN KEY (RoleID) REFERENCES Roles(RoleID)
);
```

## 4. Создание таблицы `Projects`

```sql
CREATE TABLE Projects (
    ProjectID INT PRIMARY KEY,
    ProjectName VARCHAR(100) NOT NULL,
    StartDate DATE,
    EndDate DATE,
    DepartmentID INT,
    FOREIGN KEY (DepartmentID) REFERENCES Departments(DepartmentID)
);
```

## 5. Создание таблицы `Tasks`

```sql
CREATE TABLE Tasks (
    TaskID INT PRIMARY KEY,
    TaskName VARCHAR(100) NOT NULL,
    AssignedTo INT,
    ProjectID INT,
    FOREIGN KEY (AssignedTo) REFERENCES Employees(EmployeeID),
    FOREIGN KEY (ProjectID) REFERENCES Projects(ProjectID)
);
```
