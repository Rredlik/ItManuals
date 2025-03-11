Как открыть доступ по SFTP к определенной папке и заблокировать ко всем остальным
```
sudo adduser videohelper
```

```
adduser devuser -g 1000 -o -u 1000 -d /home/videohelper/ -s /sbin/nologin
```

```
sudo mkdir -p /var/sftp/userfolder
```

```
sudo chown root:root /var/sftp
```

```
sudo chmod 755 /var/sftp
```

```
sudo chown videohelper:videohelper /var/sftp/userfolder
```

```
sudo nano /etc/ssh/sshd_config
```
Записать в файл:
```
Match User videohelper
ForceCommand internal-sftp
PasswordAuthentication yes
ChrootDirectory /var/sftp
PermitTunnel no
AllowAgentForwarding no
AllowTcpForwarding no
X11Forwarding no
```

```
sudo systemctl restart ssh
```

Теперь отправляемся в конфиг SSH - 
```
nano /etc/ssh/sshd_config
```
Ищем следующую строчку:
```
Subsystem sftp /usr/lib/openssh/sftp-server
```
и меняем на
```
Subsystem sftp internal-sftp
```

Настройка директорий для пользователя SFTP
Отправляемся в директорию /home и там ищем папку свежесозданного пользователя, а в ней папку chroot. Устанавливаем её владельцем пользователя root:
```
chown root:root /home/videohelper/chroot
```
Устанавливаем нужные права на папку:
```
chmod 755 /home/videohelper
```

```
mkdir /home/videohelper/media_files
```

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


Но при перезагрузке сервера, монтирование отвалится. Чтобы этого не происходило, добавим в /etc/fstab
```
/home/rredlik/TgBot_VideoHelper/media_files/ /home/videohelper/media_files/chroot none bind 0 0
```


Если понадобится дать такой же доступ ещё одному пользователю.

adduser seconduser -g1000 -o -u1000 -d /home/videohelper/
passwd seconduser
usermod -aG videohelper seconduser

Источники:
- https://selectel.ru/blog/protocol-sftp/
- https://qna.habr.com/q/601686
- http://trofimovdigital.ru/blog/how-add-sftp-user-to-bitrixvm
- https://wiki.archlinux.org/title/SFTP_chroot

