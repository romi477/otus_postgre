### *PROJECT ID: postgres2021-220685*
### *VM: instance-1 (35.204.165.62)*
#### *Homework 4*
---

15: ERROR:  permission denied for table t1 - пользователю даны права на схему testnm, а таблица t1 была создана в схеме public, поэтому чтение не проходит.

27: testdb=> select * from testnm.t1;

>ERROR:  permission denied for table t1

    Предположу что нет доступа т.к. owner у таблицы testnm.t1 это postgres.
    Меняю владельца таблицы (под юзером postgres):

>ALTER TABLE testnm.t1 OWNER TO readonly;

    Почитал подсказку, понял что очень топорно решил проблему, зато кардинально ))).
    Переделал как по подсказке.

34: То что в схеме public любой новый юзер без каких либо прав может создавать таблицы / инсёртить в них - это конечно эпик фэйл.

36: Отозвал права как в подсказке, проверил.
    Вернул назад, как было:

>GRANT CREATE TO DATABASE testdb TO public;

>GRANT  ALL TO SCHEMA public TO public;