# Ссылки
[Открыть доступ к TCP/IP](#удаленное-подключение-к-бд)


# Установка PostgreSQL 17
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
sudo apt-get install postgresql postgresql-contrib -y
```

При возникновении предупреждения: Warning: apt-key is deprecated. Manage keyring files in trusted.gpg.d instead (see apt-key(8)) или The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 7FCC7D46ACCC4CF8.  Ввести команду:
```bash
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 7FCC7D46ACCC4CF8
```

##
По умолчанию служба Postgres не настроена на автоматический запуск после перезагрузки системы. Включить автоматический запуск службы 
```bash
sudo systemctl enable postgresql.service
```
Проверить состояние PostgreSQL
```bash
sudo systemctl status postgresql.service
```
Если служба не запущена, запускаем
```bash
sudo systemctl start postgresql.service
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
createuser -P --interactive
```
Изменить пароль созданного пользователя
```bash
ALTER USER user_name WITH PASSWORD 'new_password';
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

SELECT * FROM users;

DELETE FROM users WHERE user_name = 'slide';
```
Вывести таблицы на экран
```bash
\d
```



## Удаленное подключение к БД
В файле postgresql.conf 
```bash
vi /etc/postgresql/16/main/postgresql.conf
```
На строке 60 раскомментировать и заменить:
```
listen_addresses='localhost'
```
на 
```
listen_addresses='*'
```

Редактирование конфигурации в файле pg_hba.conf
```bash
vi /etc/postgresql/16/main/pg_hba.conf
```
125 строка
``` 
IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```
заменить на
``` 
IPv4 local connections:
host    all             all             all                     md5
```
Перезапуск службы:
```bash
systemctl restart postgresql
```




# Дополнительное
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

### При возникновении ошибки:
>Version of PostgreSQL not supported. Please upgrade to version or later.

Необходимо открыть файл:
```bash
vi /usr/share/phppgadmin/classes/database/Connection.php
```
Заменить секцию
``` 
// Detect version and choose appropriate database driver
        switch (substr($version,0,2)) {
            case '14': return 'Postgres';break;
            case '13': return 'Postgres13';break;
            case '12': return 'Postgres12';break;
            case '11': return 'Postgres11';break;
            case '10': return 'Postgres10';break;
        }  
```
Добавить используемую версию
``` 
// Detect version and choose appropriate database driver
        switch (substr($version,0,2)) {
            case '16': return 'Postgres';break;
            case '16': return 'Postgres16';break;
            case '15': return 'Postgres15';break;
            case '14': return 'Postgres14';break;
            case '13': return 'Postgres13';break;
            case '12': return 'Postgres12';break;
            case '11': return 'Postgres11';break;
            case '10': return 'Postgres10';break;
        }
```
Перезапустить Apache
```bash
systemctl restart apache2
```
