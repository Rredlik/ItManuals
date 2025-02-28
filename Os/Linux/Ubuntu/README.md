#  Debian/Ubuntu setup
Итак, вы купили VPS у хостера и вам пришли логин и пароль от него. Что я советую сделать сразу после установки:

- Сменить пароль пользователя root с изначального на ваш уникальный.
  ```
  passwd root
  ```
  Не использовать учетную запись root, а создать непривелигированного пользователя командой adduser и назначить ей сложный пароль командой passwd <username>.
  ```
  adduser <username>
  ```
  ```
  sudo adduser <username> sudo
  ```
  > Чтобы отключить необходимость ввода пароля для команд sudo, необходимо изменить файл:
  > ```
  > sudo nano /etc/sudoers
  > ```
  > Найти в нем строку includedir /etc/sudoers.d и под ней написать
  > ```
  > username ALL=(ALL) NOPASSWD:ALL
  > ```
  Когда вы убедитесь, что новая учетка работает, стоит запретить вход для root‑юзера по SSH, выставив (раскомментировав) в «no» или «prohibit‑password» опцию PermitRootLogin в файле /etc/ssh/sshd_config
  ```
  nano /etc/ssh/sshd_config
  ```
  После изменения необходимо перезапустить службу sshd:
  ```
  systemctl restart sshd
  ```


- Перевесить SSH со стандартного порта 22 на нестандартный порт (например, на 54 321, 41 467, выберите любой рандомный выше 1000). Это особенно важно для маскировки XTLS‑Reality. Порт задается опцией «Port» в том же /etc/ssh/sshd_config (но если у вас Ubuntu 22.10 и новее, то там все сложнее)
  ```
  apt install net-tools
  netstat -tulpan | grep 22
  ```
  Видим, что порт 22 занят. Проверим 2233:
  ```
  netstat -tulpan | grep 2233
  ```
  Вывод пуст, можно занимать. Меняем порт в конфигурационном файле SSH сервера, воспользоваться можно любым текстовым редактором:
  ```
  nano /etc/ssh/sshd_config
  ```
  Ищем строку "Port 22". Если строка начинается с символа #, его нужно удалить, и вместо 22 порта написать любой другой, например, 2233. Сохраняем изменения. Перезапускаем SSH
  ```
  systemctl restart ssh
  ```
  Так как текущая сессия SSH ещё активна, можно открыть новое окно и проверить, что подключение по новому порту работает:
  ```
  ssh username@ip_address -p2233
  ```
  Проверяем:
  ```
  netstat -tulpan | grep ssh
  ```
  Если все нормально, увидим похожий результат:
  ```
  tcp        0      0 *:2233                  *:*                     LISTEN      3849/sshd       

  tcp6       0      0 [::]:2233               [::]:*                  LISTEN      3849/sshd
  ```
  

- Обновите версии пакетов в системе на свежие, в Debian и Ubuntu это делается командами apt update и apt upgrade.
  ```
  apt update
  apt upgrade
  ```

## Создание нового пользователя

1. Create sudo user
    ```bash
    adduser your-username && usermod -aG sudo your-username
    ```
2. Update packages
    ```bash
    sudo apt-get update & sudo apt-get upgrade
    ```
3. Install packages
    ```bash
    sudo apt-get install -y btop tmux exa nvim git curl wget make unzip zip gcc build-essential make locales
    ```
4. Set time-zone
    ```bash
    timedatectl set-timezone Europe/Moscow
    ```
5. Install languages
    ```bash
    dpkg-reconfigure locales
    ```
6. Remove unused packages
    ```bash
    sudo apt-get autoremove
    ```
7. [Docker setup](../../../Tech/Docker/README.md)
8. [Ssh setup](ssh.md)
