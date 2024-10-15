# lesson4

Создал таблицу accounts(id integer, amount numeric);
```
CREATE TABLE accounts (
    id INTEGER PRIMARY KEY,
    amount NUMERIC
);
```

Добавил несколько записей и подключившись через 2 терминала добился ситуации взаимоблокировки (deadlock).

```
INSERT INTO accounts (id, amount) VALUES (1, 100);
INSERT INTO accounts (id, amount) VALUES (2, 200);
```
```
Терминал 1:
BEGIN;
UPDATE accounts SET amount = amount + 50 WHERE id = 1;
```

```
Терминал 2:
BEGIN;
UPDATE accounts SET amount = amount + 100 WHERE id = 2;
```
```
Терминал 1
UPDATE accounts SET amount = amount + 50 WHERE id = 2;
Терминал 2
UPDATE accounts SET amount = amount + 100 WHERE id = 1;
```

```
ERROR:  deadlock detected
DETAIL:  Process 81918 waits for ShareLock on transaction 608; blocked by process 81809.
Process 81809 waits for ShareLock on transaction 609; blocked by process 81918.
HINT:  See server log for query details.
CONTEXT:  while updating tuple (0,3) in relation "accounts"
```
Гуглил где находтся лог posgresql, оказалось у меня его нет. Смотрел здесь cd /var/lib/postgresql/13/main 
```
2024-10-14 20:01:44.660 MSK [81809] postgres@isotest3 WARNING:  there is already a transaction in progress
2024-10-14 20:05:01.652 MSK [81918] postgres@isotest3 ERROR:  deadlock detected
2024-10-14 20:05:01.652 MSK [81918] postgres@isotest3 DETAIL:  Process 81918 waits for ShareLock on transaction 608; blocked by process >
        Process 81809 waits for ShareLock on transaction 609; blocked by process 81918.
```
