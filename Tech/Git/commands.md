# Работа с репозиториями Github

Чтобы быстро подключиться к своему аккаунту необходимо установить программу gh
```bash
sudo apt install gh
gh auth login
```
Следуя инструкциям подключить аккаунт. Ссылка на генерацию токенов: https://github.com/settings/tokens


Ввести команду, вставив нужную ссылку, в необходимой директории
```bash
git clone https://github.com/Rredlik/TgBot_EGEBot.git
```

Обновить локальный репозиторий в нужной папке ввести
```bash
git pull
```

Удаление файлов из репозитория ()
```bash
git rm -rf --cached /.env
```
