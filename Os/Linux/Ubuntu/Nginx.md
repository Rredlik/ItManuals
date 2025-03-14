
```
sudo apt install nginx
```

```
sudo nano /etc/nginx/sites-available/myapp
```

```
server {
    listen 80;
    server_name <SERVER IP>;

    location / {
        proxy_pass http://unix:/run/gunicorn.sock;
        proxy_set_header Host $host;
        proxy_set_header X-Real_IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

```

```
sudo ln -s /etc/nginx/sites-available/myapp /etc/nginx/sites-enabled/
systemctl restart nginx
systemctl status nginx.service
```

Настройка gunicorn
```
sudo nano /etc/systemd/system/gunicorn.socket
```

```
[Unit]
Description=gunicorn socket

[Socket]
ListenStream=/run/gunicorn.sock

[Install]
WantedBy=sockets.target
```

```
sudo nano /etc/systemd/system/gunicorn.service
```

```
[Unit]
Description=gunicorn daemon
Requires=gunicorn.socket
After=network.target

[Service]
User=rredlik
Group=www-data
WorkingDirectory=/home/rredlik/TgBot_VideoHelper
ExecStart=/home/rredlik/TgBot_VideoHelper/my_venv/bin/gunicorn --access-logfile - --workers 3 --bind unix:/run/gunicorn.sock webhook:app

[Install]
WantedBy=multi-user.target
```

```
systemctl daemon-reload
systemctl restart gunicorn
systemctl enable gunicorn

systemctl status gunicorn
```
