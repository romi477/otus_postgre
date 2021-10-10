### *PROJECT ID: postgres2021-220685*
### *VM: instance-1 (34.147.46.105)*
---

1. Создал VM.
2. Прокинул ssh, зашёл на машинку.
3. Поставил postgre-13.
4. Запустил psql.
5. Создал базу iso; создал табличку persons (id, first_name, last_name); заполнил данными: ('ivan', 'ivanov'), ('petr', 'petrov').

6. Открыл две сессии в psql и подключился к базе iso; отключил автокоммит; режим READ COMMITTED.
    - В первой консоли добавил новую запись в таблицу ('sergey', 'sergeev') --> Во второй консоли сделал селект, новой записи не увидел.
    - В первой консоли сделал коммит, после чего во второй консоли увидел запись.
    - Т.е. наблюдаем "эффект неповторяющегося чтения", что как только произошёл коммит в одной сессии к базе - эти изменения становятся видны в других параллельных сессиях (в режиме READ COMMITTED).

7. Открыл две сессии в psql и подключился к базе iso; отключил автокоммит; установил режим REPEATABLE READ.
    - В первой консоли добавил новую запись в таблицу persons.
    - Вторая параллельная сессия не видит эту запись ни до ни после коммита в первой сессии.
    - Делаю коммит во второй сессии, т.е. автоматически возвращаюсь в режим READ COMMITTED. Делаю селект - вижу запись добавленную через первую консоль.
    - Наблюдаем работу "аномалии сериализации", т.е. две параллельные сессии к БД (в режиме REPEATABLE READ) полностью изолированы друг от друга.