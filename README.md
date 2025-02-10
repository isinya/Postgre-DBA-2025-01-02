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
[Текст ссылки](https://htmlacademy.ru)
