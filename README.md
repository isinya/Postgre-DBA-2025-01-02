# Postgre-DBA-2025-01 Занятие #02
SQL и реляционные СУБД. Введение в PostgreSQL

Домашнее задание

Сделать в первой сессии новую таблицу и наполнить ее данными\
create table persons(id serial, first_name text, second_name text);\
insert into persons(first_name, second_name) values('ivan', 'ivanov');\
insert into persons(first_name, second_name) values('petr', 'petrov');\
commit;    

Посмотреть текущий уровень изоляции: show transaction isolation level


[Текст ссылки](https://htmlacademy.ru)
