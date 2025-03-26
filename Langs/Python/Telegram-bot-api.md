# Telegram Bot Api
 Локальный Api сервер для ботов - [Источник](https://alexell.ru/blog/various/podnimaem-server-telegram-local-bot-api.html)
 [Сайт](https://alexell.ru/blog/various/podnimaem-server-telegram-local-bot-api.html) на котором можно скачать бинарник

```
# для Debian 11-12: 
$ apt install libc++-dev 

# для Ubuntu 22.04: 
$ apt install libc++-14-dev 

# для Ubuntu 24.04 
$ apt install libc++-18-dev
```

Для запуска сервера вам понадобится **api_id** и **api_hash**. Вы можете получить их [здесь](https://my.telegram.org/apps). Обратите внимание, что система очень строга к названию приложения, поэтому пробуйте разные варианты, пока не получится.

**Запускаем сервер:**
```
telegram-bot-api --local --api-id=<api_id> --api-hash=<api_hash>
```

Если никаких ошибок не появилось и сервер занял собой терминал, значит все хорошо. Чтобы окончательно убедиться, откройте еще один терминал и выполните команду `curl http://localhost:8081`

В ответ вы должны увидеть следующее:

```
{"ok":false,"error_code":404,"description":"Not Found"}
```

# Настройка автозапуска

```
sudo nano /etc/supervisor/conf.d/telegram-bot-api.conf
```


Добавьте следующий код:

```
[program:telegram-bot-api]
directory=/home/rredlik/telegram_api
command=/bin/bash -c "/home/rredlik/telegram_api/telegram-bot-api --local --api-id=<api_id> --api-hash=<api_hash> --max-webhook-connections=1000"

autostart=true
autorestart=true
stderr_Logfile=/home/rredlik/telegram_api/logfile_err.log
stdout_Logfile=/home/rredlik/telegram_api/logfile.log
user=rredlik
```

```
sudo supervisorctl reload
sudo supervisorctl status
```

