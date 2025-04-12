# Лабораторная работа №1: Основы защиты баз данных в PostgreSQL

**Дата выполнения:** 11.04.2025  
**Студент:** Рогожин Максим Матвеевич
**Группа:** ИС-22  
**Дисциплина:** Технология разработки и защиты баз данных

---

## Цель работы
Познакомиться с основными принципами защиты баз данных в PostgreSQL, научиться создавать пользователей с различными уровнями доступа и настраивать базовые механизмы безопасности.

---

## Оборудование и программное обеспечение
- Персональный компьютер с операционной системой Windows/Linux  
- СУБД PostgreSQL (версия 11 или выше)  
- Программа pgAdmin 4 для графического управления базой данных  

---

## Теоретические сведения
PostgreSQL предоставляет различные механизмы защиты данных:  
- Аутентификация: процесс проверки подлинности пользователя.  
- Авторизация: определение прав доступа пользователя к объектам базы данных.  
- Роли и привилегии: система разграничения доступа к объектам базы данных.  
- Шифрование данных: защита конфиденциальной информации.  

В PostgreSQL используется ролевая модель доступа. Роль может представлять пользователя или группу пользователей. Роли могут владеть объектами базы данных и предоставлять привилегии на эти объекты другим ролям.  

Основные привилегии в PostgreSQL:  
- SELECT: чтение данных  
- INSERT: добавление данных  
- UPDATE: изменение данных  
- DELETE: удаление данных  
- TRUNCATE: очистка таблицы  
- REFERENCES: создание внешних ключей  
- TRIGGER: создание триггеров  
- CREATE: создание объектов  
- CONNECT: подключение к базе данных  
- TEMPORARY: создание временных таблиц  
- EXECUTE: выполнение функций  

---

## Ход работы

### Часть 1: Создание базы данных и таблиц
1. Запущен pgAdmin 4 и выполнено подключение к серверу PostgreSQL.  
2. Создана новая база данных с именем school_library:  

![image](https://github.com/user-attachments/assets/605e4668-600d-464f-bcb7-632762f06c02)


3. Открыт инструмент запросов  и выполнен следующий SQL-скрипт для создания таблиц:  

   `sql
   CREATE TABLE books (
       book_id SERIAL PRIMARY KEY,
       title VARCHAR(100) NOT NULL,
       author VARCHAR(100) NOT NULL,
       publication_year INTEGER,
       available BOOLEAN DEFAULT TRUE
   );

   CREATE TABLE readers (
       reader_id SERIAL PRIMARY KEY,
       first_name VARCHAR(50) NOT NULL,
       last_name VARCHAR(50) NOT NULL,
       class VARCHAR(10) NOT NULL,
       registration_date DATE DEFAULT CURRENT_DATE
   );

   CREATE TABLE loans (
       loan_id SERIAL PRIMARY KEY,
       book_id INTEGER REFERENCES books(book_id),
       reader_id INTEGER REFERENCES readers(reader_id),
       loan_date DATE DEFAULT CURRENT_DATE,
       return_date DATE,
       returned BOOLEAN DEFAULT FALSE
   );
![image](https://github.com/user-attachments/assets/eacea3d6-4da1-4c38-aa62-c8ce3f8936e8)

4. Заполнены таблицы данными с помощью следующих комманд:
   
INSERT INTO books (title, author, publication_year, available)
VALUES 
    ('Война и мир', 'Лев Толстой', 1869, true),
    ('Преступление и наказание', 'Федор Достоевский', 1866, true),
    ('Мастер и Маргарита', 'Михаил Булгаков', 1967, true),
    ('Евгений Онегин', 'Александр Пушкин', 1833, true),
    ('Гарри Поттер и философский камень', 'Джоан Роулинг', 1997, true);

INSERT INTO readers (first_name, last_name, class)
VALUES 
    ('Иван', 'Иванов', '10А'),
    ('Мария', 'Петрова', '10Б'),
    ('Алексей', 'Сидоров', '10А'),
    ('Екатерина', 'Смирнова', '10В');

INSERT INTO loans (book_id, reader_id, loan_date, return_date, returned)
VALUES 
    (1, 1, '2023-09-01', '2023-09-15', true),
    (2, 2, '2023-09-05', NULL, false),
    (3, 3, '2023-09-10', NULL, false);

![image](https://github.com/user-attachments/assets/4df0d325-6960-4b8d-abe2-10607b4ab9d5)

---

### Часть 2: Создание пользователей и настройка прав доступа

1. Созданы роли для различных типов пользователей.
  -Роль администратора библиотеки
  -Роль библиотекаря (Может просматривать и изменять данные)
  -Роль ученика (Может только просматривать определенные данные)
   
![image](https://github.com/user-attachments/assets/019e8585-f150-4351-b9da-3650476f878f)

3. Предоставлены соотвутствующие привилегии ролям

![image](https://github.com/user-attachments/assets/f7ef5a0c-ab45-4569-b04c-42f40293f231)

4. Настроена политика защиты 

![image](https://github.com/user-attachments/assets/fa557d11-33e5-40f3-89e4-7c780bc60369)

---

### Часть 3: Проверка настроек безопасности

1. Проверили права доступа для роли библиотекарь

Вошли в базу данных как библиотекарь:

![image](https://github.com/user-attachments/assets/866e7395-111c-4494-a0cb-815d4a8c6052)

Вошли в базу данных как студент 

![image](https://github.com/user-attachments/assets/a072e696-451a-4018-bb2d-f9248544bbaf)

---

## Самостоятельная работа


### 1. Создание роли 'teacher'

Создана новая роль 'teacher' с возможностью входа в систему и паролем 'teacher123'. Использована команда:

![image](https://github.com/user-attachments/assets/683cb270-4d86-4870-8855-f003dbe5efe6)

### 2. Предоставление прав роли 'teacher'

Предоставлены следующие права 
  - Право на просмотр всех таблиц в схеме public
  - Право на обновление поля returned в таблице loans
Использованы команды: 

![image](https://github.com/user-attachments/assets/5c35b46b-7e15-4bf8-a309-9e799db07b74)

### 3. Создание представления overdue_loans

Представление отображает просроченные выдачи книг. Использована команда: 

![image](https://github.com/user-attachments/assets/3c41a5be-21c3-4fb6-96fa-2bbf664635cf)

### 4. Предоставление доступа к представлению ролям 'librarian' и 'teacher'

Ролям предоставлен доступ к просмотру данных из представления overdue_loands.
Использована команда: 

![image](https://github.com/user-attachments/assets/da63a6fe-ba9b-48db-984b-cbcad29f3d72)

### 5. Проверка работы созданных прав доступа

Подключение под ролью teacher: 
  1. Выполнено подключение к БД под ролью teacher с паролем teacher123
     ![image](https://github.com/user-attachments/assets/0c3e452c-23b2-42ae-81bd-2700301891bb)
  2. Выполнен запрос к представлению overdue_loans:
     ![image](https://github.com/user-attachments/assets/70449c04-56af-4efc-9193-5d2266272e78)
  3. Выполнен запрос на просмотр таблицы loans:
     ![image](https://github.com/user-attachments/assets/195b48b2-9d19-4628-84c7-4664d5de740f)
  4. Выполнено обновление статуса возврата книги:
     ![image](https://github.com/user-attachments/assets/1e4dd638-3dca-41e1-96e6-f262228bf549)
     
(так как таблицу не заполняли была получена ошибка)

  Подключение под ролью librarian 
  ![image](https://github.com/user-attachments/assets/8fd65ca7-a86a-45dc-b22a-0aa1406bead0)

---

## Заключение 
В ходе самостоятельной работы создана роль teacher с необходимыми правами доступа, разработано представление overdue_loans для отслеживания просроченных выдач книг, и настроен доступ к этому представлению для ролей librarian и teacher. Проверка под разными ролями подтвердила корректность настройки прав: роль teacher может просматривать данные и обновлять статус возврата книг, а роль librarian — только просматривать представление.
     



