
- Скачайте архив при помощи команды

```
wget https://cloudpub.ru/download/stable/clo-1.4.86-stable-linux-x86_64.tar.gz
```

- Распакуйте архив при помощи команды

```
tar -xvf clo-1.4.86-stable-linux-x86_64.tar.gz
```

## Привяжите ваш аккаунт

Если в еще этого не сделали, создайте аккаунт в [личном кабинете](https://cloudpub.ru/dashboard). После этого привяжите ваш аккаунт к клиенту, выполнив следующую команду:

```
./clo set token <ваш токен> 
```

Свой токен вы можете найти на главной странице в личном кабинете после регистрации.

## Опубликуйте ваш первый ресурс[​](https://cloudpub.ru/docs/#%D0%BE%D0%BF%D1%83%D0%B1%D0%BB%D0%B8%D0%BA%D1%83%D0%B9%D1%82%D0%B5-%D0%B2%D0%B0%D1%88-%D0%BF%D0%B5%D1%80%D0%B2%D1%8B%D0%B9-%D1%80%D0%B5%D1%81%D1%83%D1%80%D1%81 "Прямая ссылка на Опубликуйте ваш первый ресурс")

Для публикации локального HTTP сервера работающего на порте 8080 выполните команду:

```
clo publish http 8080
```

После этого вам будет предоставлен URL, по которому ваш ресурс будет доступен в интернете, например:

```
Сервис опубликован: http://localhost:8080 -> https://wildly-suitable-fish.cloudpub.ru
```

В этом случае ваш ресурс будет доступен по адресу `https://wildly-suitable-fish.cloudpub.ru`.

URL вашего ресурса будет отличаться от приведенного в примере.

## Автоматический запуск

```bash
sudo nano /etc/supervisor/conf.d/webservice.conf
```

В команду необходимо подставить свой токен
```
[program:webservice]
directory=/home/rredlik
command=/bin/bash -c "/home/rredlik/clo set token <YOUR-TOKEN> & sudo /home/rredlik/clo publish http 8080"

autostart=true
autorestart=true
stderr_Logfile=/home/rredlik/web_logfile_err.log
stdout_Logfile=/home/rredlik/web_logfile.log
```
Затем надо перезапустить supervisor
```bash
sudo supervisorctl reload
```
