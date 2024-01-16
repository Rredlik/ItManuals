# Установка PostgreSQL
Отметим пользовательские хранилища, поскольку туда обычно в первую очередь выгружают самые свежие версии.
```bash
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
```

Добавление пакетов.
```bash
wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add
```
Обновляем системные библиотеки
```bash
sudo apt-get update
```
Получение последней доступной версии PostgreSQL из официального репозитория
```bash
sudo apt-get install postgresql postgresql-contrib
```

## Первый запуск PostgreSQL
Управлению от лица созданной по умолчанию учетной записи
```bash
sudo su - postgres
```
Вход в консоль управления под видом используемого профиля
```bash
psql
```
Просмотр информации о текущем сеансе 
```bash
\conninfo
```
Выйти из окружения
```bash
\q
```
## Создание пользователя и базы данных
Находясь в консоли под управлением профиля postgres
```bash
createuser --interactive
```
Базу данных лучше назвать таким же именем, как была названа учетная запись. Пример, lumpics — имя пользователя
```bash
createdb lumpics
```
Переход к работе с указанной базой данных 
```bash
psql -d lumpics
```

## Создание таблицы и работа со строками
```bash
CREATE TABLE users (user_id serial PRIMARY KEY, 
user_name varchar (20), 
user_mail varchar (30), 
registration_date timestamp default current_timestamp
);

INSERT INTO users (user_name, user_mail) VALUES ('tester', 'test@mail.ru');

SELECT * FROM test;

DELETE FROM test WHERE type = 'slide';
```
Вывести таблицы на экран
```bash
\d
```

## Установка phpPgAdmin
```bash
sudo apt-get update
sudo apt-get install apache2
```
Проверка работоспособности и корректности синтаксиса
```bash
sudo apache2ctl configtest
```
Запуск сервера
```bash
sudo systemctl start apache2
```
Загрузка phpPgAdmin
```bash
sudo apt install phppgadmin
```
Следует немного изменить конфигурационный файл
```bash
vi /etc/apache2/conf-available/phppgadmin.conf
```
Перед строкой «Require local» поставьте #, чтобы переделать ее в комментарий, а снизу введите Allow From all. После этого перезапустить веб-сервер
```bash
sudo service apache2 restart
```
Адрес для подключения (server-ip — это IP адрес вашего сервера): http://server-ip/phppgadmin


