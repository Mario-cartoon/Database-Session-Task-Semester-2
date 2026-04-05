# Task 2

## Условие

Найти всех сотрудников, подчиняющихся Ивану Иванову (`EmployeeID = 1`), включая их подчиненных, подчиненных подчиненных, а также самого Ивана Иванова.

Для каждого сотрудника нужно вывести:

- `EmployeeID`;
- имя сотрудника;
- идентификатор менеджера;
- название отдела;
- название роли;
- названия проектов;
- названия задач;
- общее количество задач, назначенных сотруднику;
- общее количество прямых подчиненных.

Если у сотрудника нет назначенных проектов или задач, необходимо отобразить `NULL`.

## Решение

```sql
WITH RECURSIVE Employe AS (
    SELECT
        EmployeeID, Name, ManagerID, DepartmentID, RoleID
    FROM Employees
    WHERE EmployeeID = 1

    UNION ALL

    SELECT
        e.EmployeeID, e.Name, e.ManagerID, e.DepartmentID, e.RoleID
    FROM Employees e
    JOIN Employe eh ON e.ManagerID = eh.EmployeeID
),
EmployeeTasks AS (
    SELECT
        t.AssignedTo,
        COUNT(*) AS total_tasks,
        GROUP_CONCAT(DISTINCT t.TaskName ORDER BY t.TaskName SEPARATOR ', ') AS task_names,
        GROUP_CONCAT(DISTINCT p.ProjectName ORDER BY p.ProjectName SEPARATOR ', ') AS project_names
    FROM Tasks t
    JOIN Projects p ON t.ProjectID = p.ProjectID
    GROUP BY t.AssignedTo
),
SubordinateCounts AS (
    SELECT
        ManagerID,
        COUNT(*) AS total_subordinates
    FROM Employees
    WHERE ManagerID IS NOT NULL
    GROUP BY ManagerID
)
SELECT
    eh.EmployeeID,
    eh.Name AS employee_name,
    eh.ManagerID,
    d.DepartmentName AS department_name,
    r.RoleName AS role_name,
    et.project_names,
    et.task_names,
    COALESCE(et.total_tasks, 0) AS total_tasks,
    COALESCE(sc.total_subordinates, 0) AS total_subordinates
FROM Employe eh
LEFT JOIN Departments d ON eh.DepartmentID = d.DepartmentID
LEFT JOIN Roles r ON eh.RoleID = r.RoleID
LEFT JOIN EmployeeTasks et ON eh.EmployeeID = et.AssignedTo
LEFT JOIN SubordinateCounts sc ON eh.EmployeeID = sc.ManagerID
ORDER BY eh.Name;
```

## Ожидаемый вывод для тестовых данных

| EmployeeID | EmployeeName            | ManagerID | DepartmentName      | RoleName                     | ProjectNames | TaskNames                                                                 | TotalTasks | TotalSubordinates |
|------------|-------------------------|-----------|---------------------|------------------------------|--------------|---------------------------------------------------------------------------|------------|-------------------|
| 20         | Александр Александров   | 3         | Отдел маркетинга    | Менеджер                     | Проект B     | NULL                                                                      | 0          | 0                 |
| 4          | Алексей Алексеев        | 2         | Отдел продаж        | Менеджер                     | Проект A     | Задача 14: Создание презентации для клиентов, Задача 1: Подготовка отчета по продажам | 2 | 4 |
| 16         | Анастасия Анастасиева   | 7         | Отдел поддержки     | Специалист по поддержке      | Проект D     | NULL                                                                      | 0          | 0                 |
| 29         | Анастасия Анастасиевна  | 7         | Отдел поддержки     | Специалист по поддержке      | Проект D     | NULL                                                                      | 0          | 0                 |
| 6          | Андрей Андреев          | 1         | Отдел разработки    | Разработчик                  | Проект C     | Задача 15: Обновление сайта, Задача 6: Обновление документации            | 2          | 6                 |
| 30         | Валентин Валентинов     | 6         | Отдел разработ