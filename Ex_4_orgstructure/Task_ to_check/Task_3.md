# Task 3

## Условие

Найти всех сотрудников, которые занимают роль менеджера и имеют подчиненных, то есть количество подчиненных больше `0`.

Для каждого такого сотрудника нужно вывести:

- `EmployeeID`;
- имя сотрудника;
- идентификатор менеджера;
- название отдела;
- название роли;
- названия проектов;
- названия задач;
- общее количество подчиненных, включая подчиненных более глубоких уровней.

Если у сотрудника нет назначенных проектов или задач, необходимо отобразить `NULL`.

## Решение

```sql
WITH RECURSIVE
AllWorkers AS (
    SELECT
        ManagerID AS RootID,
        EmployeeID AS SubID,
        1 AS depth
    FROM Employees
    WHERE ManagerID IS NOT NULL

    UNION ALL

    SELECT
        aw.RootID,
        e.EmployeeID,
        aw.depth + 1
    FROM Employees e
    JOIN AllWorkers aw ON e.ManagerID = aw.SubID
    WHERE aw.depth < 10
),
ManagersAndWorkers AS (
    SELECT
        e.EmployeeID,
        e.Name,
        e.ManagerID,
        e.DepartmentID,
        e.RoleID,
        r.RoleName,
        COUNT(DISTINCT aw.SubID) AS total_subordinates
    FROM Employees e
    JOIN AllWorkers aw ON e.EmployeeID = aw.RootID
    JOIN Roles r ON e.RoleID = r.RoleID
    WHERE r.RoleName = 'Менеджер'
    GROUP BY e.EmployeeID, e.Name, e.ManagerID, e.DepartmentID, e.RoleID, r.RoleName
),
ManagerTasks AS (
    SELECT
        t.AssignedTo,
        GROUP_CONCAT(DISTINCT p.ProjectName ORDER BY p.ProjectName SEPARATOR ', ') AS project_names,
        GROUP_CONCAT(DISTINCT t.TaskName ORDER BY t.TaskName SEPARATOR ', ') AS task_names
    FROM Tasks t
    JOIN Projects p ON t.ProjectID = p.ProjectID
    GROUP BY t.AssignedTo
)
SELECT
    mw.EmployeeID,
    mw.Name AS employee_name,
    mw.ManagerID,
    d.DepartmentName,
    mw.RoleName,
    mt.project_names,
    mt.task_names,
    mw.total_subordinates
FROM ManagersAndWorkers mw
LEFT JOIN Departments d ON mw.DepartmentID = d.DepartmentID
LEFT JOIN ManagerTasks mt ON mw.EmployeeID = mt.AssignedTo
ORDER BY mw.EmployeeID;
```
## Ожидаемый вывод для тестовых данных

| EmployeeID | EmployeeName      | ManagerID | DepartmentName | RoleName | ProjectNames | TaskNames | TotalSubordinates |
|------------|-------------------|-----------|----------------|----------|--------------|-----------|-------------------|
| 4          | Алексей Алексеев  | 2         | Отдел продаж   | Менеджер | Проект A     | NULL      | 0                 |