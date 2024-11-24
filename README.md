# goit-rdb-hw-05

1. Напишіть SQL запит, який буде відображати таблицю order_details та поле customer_id 
з таблиці orders відповідно для кожного поля запису з таблиці order_details.

Це має бути зроблено за допомогою вкладеного запиту в операторі SELECT.
	
```
USE Mydb;

SELECT 
    od.*, 
    (SELECT o.customer_id 
     FROM orders o 
     WHERE o.id = od.order_id) AS customer_id
FROM 
    order_details od;
```

2. Напишіть SQL запит, який буде відображати таблицю order_details. 
Відфільтруйте результати так, щоб відповідний запис із таблиці orders виконував умову shipper_id=3.

Це має бути зроблено за допомогою вкладеного запиту в операторі WHERE.
```
USE Mydb;

SELECT *
FROM order_details
WHERE order_id IN (
    SELECT id
    FROM orders
    WHERE shipper_id = 3
);
```

3. Напишіть SQL запит, вкладений в операторі FROM, який буде обирати рядки з умовою quantity>10 з таблиці order_details. 
Для отриманих даних знайдіть середнє значення поля quantity — групувати слід за order_id.
```
USE Mydb;

SELECT 
    subquery.order_id,
    AVG(subquery.quantity) AS avg_quantity
FROM (
    SELECT order_id, quantity
    FROM order_details
    WHERE quantity > 10
) AS subquery
GROUP BY subquery.order_id;
```

4. Розв’яжіть завдання 3, використовуючи оператор WITH для створення тимчасової таблиці temp. 
Якщо ваша версія MySQL більш рання, ніж 8.0, створіть цей запит за аналогією до того, як це зроблено в конспекті.
```
USE Mydb;

WITH temp AS (
    SELECT order_id, quantity
    FROM order_details
    WHERE quantity > 10
)
SELECT 
    temp.order_id,
    AVG(temp.quantity) AS avg_quantity
FROM temp
GROUP BY temp.order_id;
```

5. Створіть функцію з двома параметрами, яка буде ділити перший параметр на другий.
Обидва параметри та значення, що повертається, повинні мати тип FLOAT.

Використайте конструкцію DROP FUNCTION IF EXISTS. 
Застосуйте функцію до атрибута quantity таблиці order_details. 
Другим параметром може бути довільне число на ваш розсуд.
```
USE Mydb;

DROP FUNCTION IF EXISTS Divide_floats;

DELIMITER //

CREATE FUNCTION Divide_floats(numerator FLOAT, denominator FLOAT)
RETURNS FLOAT
DETERMINISTIC
BEGIN    
    IF denominator = 0 THEN
        RETURN NULL;
    END IF;
    RETURN numerator / denominator;
END //

DELIMITER ;

SELECT 
    order_id,
    quantity,
    Divide_floats(quantity, 2) AS divided_quantity
FROM 
    order_details;
```