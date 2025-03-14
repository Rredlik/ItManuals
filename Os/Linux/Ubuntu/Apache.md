
Устанавливаем пакет
```
sudo apt update
sudo apt install apache2 libapache2-mpm-itk
```

Проверка статуса работы
```
sudo systemctl status apache2
```

При возникновении ошибки: 

```
Mar 14 11:27:15 4329763-cc66460 apachectl[70122]: AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 2a03:6f00:a::8538. Set the 'ServerName' dir...
Mar 14 11:27:15 4329763-cc66460 apachectl[70122]: (98)Address already in use: AH00072: make_sock: could not bind to address [::]:80
Mar 14 11:27:15 4329763-cc66460 apachectl[70122]: (98)Address already in use: AH00072: make_sock: could not bind to address 0.0.0.0:80

```

Проверить порты, которые указаны в ошибке:
```
netstat -ltnp | grep :80
```

Удаление мешающих пакетов
```
sudo apt-get remove nginx*
sudo apt-get purge nginx*

```

Когда все запустилось:
```
sudo chmod 400 /var/www/html
```

