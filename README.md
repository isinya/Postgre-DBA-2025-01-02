# Postgre-DBA-2025-01 Занятие #02
SQL и реляционные СУБД. Введение в PostgreSQL

Домашнее задание

>Сделать в первой сессии новую таблицу и наполнить ее данными\
create table persons(id serial, first_name text, second_name text);\
insert into persons(first_name, second_name) values('ivan', 'ivanov');\
insert into persons(first_name, second_name) values('petr', 'petrov');\
commit;    
Посмотреть текущий уровень изоляции: show transaction isolation level   
   ```sh
   otus=> Begin;    
BEGIN    
otus=*> create table persons(id serial, first_name text, second_name text);    
CREATE TABLE    
otus=*> insert into persons(first_name, second_name) values('ivan', 'ivanov');    
INSERT 0 1    
otus=*> insert into persons(first_name, second_name) values('petr', 'petrov');    
INSERT 0 1    
otus=*> commit;    
COMMIT    
otus=>    
otus=> show transaction isolation level;    
 transaction_isolation    
-----------------------    
 read committed    
(1 строка)    
   ```
>начать новую транзакцию в обоих сессиях с дефолтным (не меняя) уровнем изоляции    
в первой сессии добавить новую запись
insert into persons(first_name, second_name) values('sergey', 'sergeev');
   ```sh
   otus=> begin;
BEGIN     
otus=*> insert into persons(first_name, second_name) values('sergey', 'sergeev');     
INSERT 0 1     
otus=*>     
   ```
>сделать select from persons во второй сессии    
видите ли вы новую запись и если да то почему?
>
   ```sh
otus=> begin;
BEGIN
otus=*> Select * From persons;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
(2 строки)
otus=*>
   ```

**Нет. Новая запись не видна. Уровень изоляции транзакций - read committed, аномалия "грязное чтение" невозможна. Транзакция в первой сессии не заверешена, внесенные изменения не видны другим сессиям.**
>завершить первую транзакцию - commit;
сделать select from persons во второй сессии
видите ли вы новую запись и если да то почему?
   ```sh
otus=*> Select * From persons;    
 id | first_name | second_name    
----+------------+-------------    
  1 | ivan       | ivanov    
  2 | petr       | petrov    
  3 | sergey     | sergeev    
(3 строки)
   ```
**Да, новая запись видна. Транзакция в сессии 1 завершена, внесенные ее изменения доступны другим сессиям.**
>завершите транзакцию во второй сессии    
начать новые но уже repeatable read транзации - set transaction isolation level repeatable read;    
в первой сессии добавить новую запись insert into persons(first_name, second_name) values('sveta', 'svetova');    
   ```sh
otus=> begin;    
BEGIN    
otus=*> set transaction isolation level repeatable read;    
SET    
otus=*> insert into persons(first_name, second_name) values('sveta', 'svetova');    
INSERT 0 1    
   ```
>сделать select* from persons во второй сессии    
видите ли вы новую запись и если да то почему?
   ```sh
otus=> begin;    
BEGIN    
otus=*> set transaction isolation level repeatable read;    
SET    
otus=*> Select * From persons;    
 id | first_name | second_name    
----+------------+-------------    
  1 | ivan       | ivanov    
  2 | petr       | petrov    
  3 | sergey     | sergeev    
(3 строки)
   ```
**Нет новая запись не видна. Транзакция в первой серии не заверешна, аномалия Граязное чтение не достускается в Postgres.**
>завершить первую транзакцию - commit;    
сделать select from persons во второй сессии    
видите ли вы новую запись и если да то почему?    
   ```sh
otus=*> Select * From persons;    
 id | first_name | second_name    
----+------------+-------------    
  1 | ivan       | ivanov    
  2 | petr       | petrov    
  3 | sergey     | sergeev    
(3 строки)    
   ```
**Нет, новая запись не видна. Уровень изоляции Repeatable read - аномлия "неповторяемое чтение" невозможна.**
>завершить вторую транзакцию    
сделать select * from persons во второй сессии    
видите ли вы новую запись и если да то почему?
   ```sh
otus=*> commit;
COMMIT
otus=> begin;
BEGIN
otus=*>  Select * From persons;
 id | first_name | second_name
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
  4 | sveta      | svetova
(4 строки)
   ```
**Да, новая запись видна. Транзакция в сессии 2 началась после завершения транзакции в сессии 1. Выполненные изменения видны всем.**
