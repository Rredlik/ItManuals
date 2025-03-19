# Запуск программ без отображения консоли

Будет использоваться утилита Supervisor, позволяет автоматически запускать скрипт после перезагрузки сервера или перезапускать при возникновении ошибки. 
Установка:
```bash
sudo apt install supervisor
```
Чтобы программа работала постоянно, её надо добавить в автозагрузку и запустить. Для этого выполните:
```bash
sudo systemctl enable supervisor --now
```
посмотреть состояние с помощью команды:
```bash
sudo systemctl status supervisor
```
# Пример использования
Путь до выполняемого скрипта /root/TelegramBot/TgBot/run.py

Теперь для этого процесса можно создать конфигурационный файл supervisor. Лучше для каждой программы создавать отдельный конфигурационный файл в каталоге /etc/supervisor/conf.d/ с именем *.conf. 
```bash
sudo nano /etc/supervisor/conf.d/TgBot.conf
```
Например, для этой программы этот файл будет выглядеть вот так:
```bash
[program:TgBot]
directory=/root/TelegramBot/TgBot
command=/bin/bash -c "source my_venv/bin/activate && python3 run.py"

autostart=true
autorestart=true
stderr_Logfile=/root/TelegramBot/logfile_err.log
stdout_Logfile=/root/TelegramBot/logfile.log
```
Затем надо перезапустить supervisor
```bash
sudo supervisorctl reload
```
Затем вы можете посмотреть состояние настроенных процессов с помощью команды:
```bash
sudo supervisorctl status
```
Если процесс находится в состоянии RUNNING, значит всё хорошо и он был запущен успешно.
Если вашей программе нужны какие-либо переменные окружения, вы можете их передать с помощью параметра environment в конфигурационном файле. Переменные надо записать через запятую. Например:
```bash
environment=DISPLAY=":1",HOME="/root"
```
Остановить процесс:
```bash
sudo supervisorctl stop TgBot
```
Запустить процесс:
```bash
sudo supervisorctl start TgBot
```
Перезапустить процесс:
```bash
sudo supervisorctl restart TgBot
```
Кроме того, можно подключится к процессу и посмотреть что он выводит в stdout/stderr с помощью команды fg:
```bash
sudo supervisorctl fg TgBot
```
