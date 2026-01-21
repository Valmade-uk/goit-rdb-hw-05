# Relational Databases: Concepts and Techniques

---

## Тема завдання

Вкладені запити. Повторне використання коду

---

## Мета роботи

Ознайомитись з використанням вкладених запитів у SQL, різними способами їх застосування (у SELECT, WHERE, FROM, WITH), а також навчитися створювати та використовувати користувацькі функції.

---

## Структура репозиторію

```
goit-rdb-hw-05/
│
├── task_1/                # вкладений запит в операторі SELECT
├── task_2/                # вкладений запит в операторі WHERE
├── task_3/                # вкладений запит в операторі FROM
├── task_4/                # вкладений запит з використанням оператора WITH (CTE)
├── task_5/                # створення функції ділення з двома параметрами типу FLOAT
└── README.md
```
---

## Завдання 1

### Умова

Написати SQL-запит, який буде відображати таблицю `order_details` та поле `customer_id` з таблиці `orders` відповідно для кожного запису з таблиці `order_details`. Реалізувати за допомогою вкладеного запиту в операторі `SELECT`.

### Рішення

```sql
USE mydb;

SELECT 
	od.*, 
    (select customer_id 
    from orders as o 
    where o.id = od.order_id) as customer_id
FROM order_details od;
```

---

## Завдання 2

### Умова

Написати SQL-запит, який буде відображати таблицю `order_details`, відфільтровану так, щоб відповідний запис із таблиці `orders` виконував умову `shipper_id = 3`. Використати вкладений запит у операторі `WHERE`.

### Рішення

```sql
USE mydb;

SELECT *
FROM order_details
WHERE order_id IN (
	SELECT id 
    FROM orders 
    WHERE shipper_id = 3
    );
```

---

## Завдання 3

### Умова

Написати SQL-запит, вкладений в операторі `FROM`, який буде обирати рядки з умовою `quantity > 10` з таблиці `order_details`. Для отриманих даних знайти середнє значення поля `quantity`, групуючи за `order_id`.

### Рішення

```sql
USE mydb;

SELECT
    od.order_id,
    AVG(od.quantity) AS average_quantity
FROM (
    SELECT order_id, quantity
    FROM order_details
    WHERE quantity > 10
) AS od
GROUP BY od.order_id;
```

---

## Завдання 4

### Умова

Розв’язати завдання 3, використовуючи оператор `WITH` для створення тимчасової таблиці `temp`.

### Рішення

```sql
USE mydb;

WITH od AS (SELECT order_id, quantity
            FROM order_details
            WHERE quantity > 10
            )
SELECT order_id, AVG(quantity) AS average_quantity
FROM od
GROUP BY order_id;
```

---

## Завдання 5

### Умова

Створити функцію з двома параметрами типу `FLOAT`, яка ділить перший параметр на другий. Функція повинна використовувати `DROP FUNCTION IF EXISTS`. Після цього застосувати функцію до атрибуту `quantity` таблиці `order_details`.

### Рішення

```sql
USE mydb;

DROP FUNCTION IF EXISTS divide_first_on_second;

DELIMITER //

CREATE FUNCTION divide_first_on_second(value_1 FLOAT, value_2 FLOAT)
RETURNS FLOAT
DETERMINISTIC
BEGIN
    RETURN CASE
        WHEN value_2 IS NULL OR value_2 = 0 THEN NULL
        WHEN value_1 IS NULL THEN NULL
        ELSE value_1 / value_2
    END;
END //

DELIMITER ;
```

### Приклад використання

```sql
SELECT
    od.*,
    divide_first_on_second(od.quantity, 2.5) AS quantity_divided
FROM order_details AS od;
```

---

## Висновок

У процесі виконання даної роботи були опрацьовані різні типи вкладених запитів, створення CTE через `WITH`, а також реалізація користувацьких функцій. Отримані навички дозволяють будувати більш гнучкі та масштабовані SQL-запити.
