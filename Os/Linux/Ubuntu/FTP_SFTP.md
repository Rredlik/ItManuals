# Открытие доступа по SFTP к определенной папке и заблокировать ко всем остальным

#### Создаем пользователя
```
useradd -m -s /sbin/nologin videohelper -g 1000
```

Число 1000 — это id пользователя rredlik, чтобы создаваемые файлы были с нужными правами. Параметр -g позволяет назначить id существующего пользователя другому пользователю. id можно узнать командой:  id -u rredlik

```
chown videohelper:videohelper /home/videohelper
chmod 755 /home/videohelper/
```

Устанавливаем пароль
```
passwd videohelper
```
#### Добавление sshd настроек для группы нового пользователя

```
sudo nano /etc/ssh/sshd_config
```

Записать в файл:
```
Match Group videohelper
    ForceCommand internal-sftp
    PasswordAuthentication yes
    ChrootDirectory /home/videohelper
    PermitTunnel no
    AllowAgentForwarding no
    AllowTcpForwarding no
    X11Forwarding no

```
Ищем следующую строчку:

```
Subsystem sftp /usr/lib/openssh/sftp-server
```
и меняем на
```
Subsystem sftp internal-sftp
```

Перезапускаем
```
sudo systemctl restart ssh
```

#### Настройка директорий для пользователя SFTP

Отправляемся в директорию /home и там ищем папку свеже-созданного пользователя, а в ней папку chroot. Устанавливаем её владельцем пользователя root:
```
mkdir /home/videohelper/chroot
chown root:root /home/videohelper/chroot
```
Устанавливаем нужные права на папку:
```
chmod 755 /home/videohelper
```

```
mkdir /home/videohelper/media_files
```

В первом пути указываем папку, которая должна быть доступна новому пользователю
```
mount --bind /home/rredlik/TgBot_VideoHelper/media_files /home/videohelper/media_files
```

```
chmod 777 /home/rredlik/TgBot_VideoHelper/media_files
find /home/rredlik/TgBot_VideoHelper/media_files -type f -exec chmod 664 {} +
find /home/rredlik/TgBot_VideoHelper/media_files -type d -exec chmod 777 {} +
```

Если в процессе монтирования директории что-то пошло не так, то можно убрать монтирование командой unmount:
```
umount /home/rredlik/TgBot_VideoHelper/media_files
```

Если не получается взаимно управлять файлами

```
chmod g+rwxs media_files
```
#### Добавление монтирования при перезапуске сервера

Но при перезагрузке сервера, монтирование отвалится. Чтобы этого не происходило, добавим в nano /etc/fstab
```
nano /etc/fstab
```

```
/home/rredlik/TgBot_VideoHelper/media_files /home/videohelper/media_files none bind 0 0
```

Если понадобится дать такой же доступ ещё одному пользователю.

```
adduser seconduser -g1000 -o -u1000 -d /home/videohelper/
passwd seconduser
usermod -aG videohelper seconduser
```

Источники:
- https://selectel.ru/blog/protocol-sftp/
- https://qna.habr.com/q/601686
- http://trofimovdigital.ru/blog/how-add-sftp-user-to-bitrixvm
- https://wiki.archlinux.org/title/SFTP_chroot
- https://docs.vultr.com/setup-sftp-user-accounts-on-ubuntu-20-04 - это хороший для создания пользователя

