# Домашнее задание к занятию 3. «MySQL» - Филатов А. В.

## Введение

Перед выполнением задания вы можете ознакомиться с 
[дополнительными материалами](https://github.com/netology-code/virt-homeworks/blob/virt-11/additional/README.md).

## Задача 1

Используя Docker, поднимите инстанс MySQL (версию 8). Данные БД сохраните в volume.

Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/virt-11/06-db-03-mysql/test_data) и 
восстановитесь из него.

Перейдите в управляющую консоль `mysql` внутри контейнера.

Используя команду `\h`, получите список управляющих команд.

Найдите команду для выдачи статуса БД и **приведите в ответе** из её вывода версию сервера БД.

Подключитесь к восстановленной БД и получите список таблиц из этой БД.

**Приведите в ответе** количество записей с `price` > 300.

В следующих заданиях мы будем продолжать работу с этим контейнером.

## Решение 1
```
docker run -d --name mysql_instance -e MYSQL_ROOT_PASSWORD=root_password -e MYSQL_DATABASE=test_db -v mysql_volume:/var/lib/mysql -p 3306:3306 mysql:8
```
![sql-status]
![count-price-300]



## Задача 2

Создайте пользователя test в БД c паролем test-pass, используя:

- плагин авторизации mysql_native_password
- срок истечения пароля — 180 дней 
- количество попыток авторизации — 3 
- максимальное количество запросов в час — 100
- аттрибуты пользователя:
    - Фамилия "Pretty"
    - Имя "James".

Предоставьте привелегии пользователю `test` на операции SELECT базы `test_db`.
    
Используя таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES, получите данные по пользователю `test` и 
**приведите в ответе к задаче**.

## Решение 2
```
CREATE USER 'test'@'%' IDENTIFIED WITH mysql_native_password BY 'test-pass' PASSWORD EXPIRE INTERVAL 180 DAY FAILED_LOGIN_ATTEMPTS 3;
ALTER USER 'test'@'%' WITH MAX_QUERIES_PER_HOUR 100;
ALTER USER 'test'@'%' ATTRIBUTE '{"Last Name": "Pretty", "First Name": "James"}';
GRANT SELECT ON test_db.* TO 'test'@'%';
FLUSH PRIVILEGES;
```
![information-user]


## Задача 3

Установите профилирование `SET profiling = 1`.
Изучите вывод профилирования команд `SHOW PROFILES;`.

Исследуйте, какой `engine` используется в таблице БД `test_db` и **приведите в ответе**.

Измените `engine` и **приведите время выполнения и запрос на изменения из профайлера в ответе**:
- на `MyISAM`,
- на `InnoDB`.

## Решение 3
![engine-db]
![alter-engine]


## Задача 4 

Изучите файл `my.cnf` в директории /etc/mysql.

Измените его согласно ТЗ (движок InnoDB):

- скорость IO важнее сохранности данных;
- нужна компрессия таблиц для экономии места на диске;
- размер буффера с незакомиченными транзакциями 1 Мб;
- буффер кеширования 30% от ОЗУ;
- размер файла логов операций 100 Мб.

Приведите в ответе изменённый файл `my.cnf`.

## Решение 4

```
[mysqld]
innodb_flush_log_at_trx_commit = 2  # Скорость IO важнее сохранности данных
innodb_file_per_table = 1  # Компрессия таблиц для экономии места на диске
innodb_log_buffer_size = 1M  # Размер буфера с незакомиченными транзакциями
innodb_buffer_pool_size = 1G  # Буфер кеширования 30% от ОЗУ
innodb_log_file_size = 100M  # Размер файла логов операций
```

---

### Как оформить ДЗ

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
