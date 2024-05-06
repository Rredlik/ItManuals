# Ssh setup
### Keys
1. Unix os (in client terminal)
 - Generate ssh key - 3x Enter
   ```bash
   ssh-keygen
   ```
- Push ssh key to server
   ```bash
   ssh-copy-id username@server_ip
   ```
### Protect
1. Change password
    ```bash
    sudo passwd user_name
    ```
2. protect your server access
   - AllowUsers **usernames**
   - PermitRootLogin no
   - PasswordAuthentication no
    ```bash
    sudo nano /etc/ssh/sshd_config
    ```
3. Restart ssh service
    ```bash
    sudo service ssh restart
    ```

## Добавление SSH-ключей для других пользователей
1. Создайте нового пользователя и укажите для него оболочку bash для использования по умолчанию:
    ```bash
    sudo useradd -m -d /home/testuser -s /bin/bash testuser
    ```
2. Переключитесь на созданного пользователя:
    ```bash
    sudo su - testuser
    ```
3. Создайте папку .ssh в домашней директории нового пользователя. Создайте в папке .ssh файл authorized_keys:
    ```bash
    mkdir .ssh
    touch .ssh/authorized_keys
    ```
4. Добавьте в файл authorized_keys публичный ключ нового пользователя:
    ```bash
    echo "<публичный_ключ>" >> /home/testuser/.ssh/authorized_keys
    ```
6. Измените права доступа к файлу authorized_keys и каталогу .ssh:
    ```bash
    chmod 700 ~/.ssh
    chmod 600 ~/.ssh/authorized_keys
    ```
8. Проверьте подключение для нового пользователя:
    ```bash
    ssh testuser@<публичный_IP-адрес_ВМ>
    ```
   
