# Установка

```bash
sudo apt-get update
sudo apt-get install redis

```

```bash
sudo apt update
sudo apt install redis-tools
sudo snap install redis
sudo snap set redis service.start=true
```

# Запуск и остановка
```
sudo systemctl start redis
```

```
sudo systemctl stop redis
```

# Подключение
```
redis-cli
```

После подключения можно проверить:
```bash
127.0.0.1:6379> ping
PONG
```