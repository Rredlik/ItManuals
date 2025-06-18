Перед тем как устанавливать python необходимо настроить сервер
- [Настройка сервера](../../Os/Linux/Ubuntu)
- [Настройка git](../../Tech/Git/commands.md)
- [Настройка PostgreSQL](../../SQL/PostgreSQL.md)
- [Запуск скрипта без блокировки консоли](./deploy.md)


# Установка Python 3.12
  - Обновление пакетов
  - Установка необходимых зависимостей. Для корректной работы Python 3.12 требуется установка некоторых зависимостей, таких как software-properties-common, которые облегчают управление репозиториями.
  - Установка pip для Python 3.12. 
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa -y
sudo apt update
sudo apt install python3.12 -y
python3.12 --version
sudo apt install -y python3.12-distutils
wget https://bootstrap.pypa.io/get-pip.py
sudo python3.12 get-pip.py
sudo apt install -y python3.12-venv
```

Затем создайте новое виртуальное окружение:

```bash
python3.12 -m venv my_venv
```
# Установка Python 3

[Сайт](https://www.digitalocean.com/community/tutorials/how-to-install-python-3-and-set-up-a-programming-environment-on-an-ubuntu-20-04-server-ru#1-python-3) на котором уже все расписано подробно
```bash
python3 -V
```
Пакеты Python можно установить с помощью pip:
```bash
sudo apt install -y python3-pip
```

```
pip3 install package_name
```

# Настройка виртуальной среды / How to create venv for python
Virtualenv is a tool for creating an isolated Python environment.
when you using it, the downloaded libraries aren't available to others.
This environment doesn't have access to global libraries.

```bash
sudo apt install -y python3-venv
```
Создание виртуального окружения:
```bash
python3 -m venv my_venv
```
Запуск окружения:
```bash
source my_venv/bin/activate
```
Установка пакетов в окружении из файла requirements.txt:
```bash
pip install -r requirements.txt
```
Запуск окружения и скрипта одной командой:
```bash
source my_venv/bin/activate && python3 run.py
```
Для выхода из среды введите команду deactivate (ctrl+C)

[Запуск скрипта без блокировки консоли](./deploy.md)
[Запуск сервера Telegram Bot API](Telegram-bot-api)
[TgBotService](TgBotService)
