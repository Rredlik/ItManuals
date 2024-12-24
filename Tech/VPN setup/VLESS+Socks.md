# Установка VPN (Proxy) на сервере

Итак, вы купили VPS у хостера и вам пришли логин и пароль от него. Что я советую сделать сразу после установки:

- Сменить пароль пользователя root с изначального на ваш уникальный. Не использовать учетную запись root, а создать непривелигированного пользователя командой adduser и назначить ей сложный пароль командой passwd <username>. Когда вы убедитесь, что она работает, стоит запретить вход для root‑юзера по SSH, выставив (раскомментировав) в «no» или «prohibit‑password» опцию PermitRootLogin в файле /etc/ssh/sshd_config

- Перевесить SSH со стандартного порта 22 на нестандартный порт (например, на 54 321, 41 467, выберите любой рандомный выше 1000). Это особенно важно для маскировки XTLS‑Reality. Порт задается опцией «Port» в том же /etc/ssh/sshd_config (но если у вас Ubuntu 22.10 и новее, то там все сложнее)

-Обновите версии пакетов в системе на свежие, в Debian и Ubuntu это делается командами apt update и apt upgrade.

## Устанавливаем XRay на сервере
Разработчики XRay разработали скрипт, который автоматически загружает XRay для вашей операционной системы и создает systemd‑юнит для его запуска: [ссылка на скрипт](https://github.com/XTLS/Xray-install). 
Установить его можно таким образом:
```shell
apt install curl mc htop nano
bash -c "$(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install
```
```
systemctl stop xray.service
./usr/local/bin/xray uuid
```
^^ ключи стоит записать, они нигде не сохранятся в виде файла.
```shell
nano /usr/local/etc/xray/config.json
```
Пример конфига:
```
{
  "log": {
    "loglevel": "info"
  },
  "routing": {
    "rules": [],
    "domainStrategy": "AsIs"
  },
  "inbounds": [
    {
      "port": 42639,
      "tag": "ss",
      "protocol": "shadowsocks",
      "settings": {
        "method": "2022-blake3-aes-128-gcm",
        "password": "сюда вставить выхлоп команды openssl rand -base64 16",
        "network": "tcp,udp"
      }
    },
    {
      "port": 443,
      "protocol": "vless",
      "tag": "vless_tls",
      "settings": {
        "clients": [
          {
            "id": "сюда вставить ID из выхлопа ./usr/local/bin/xray uuid",
            "email": "user@server",
            "flow": "xtls-rprx-vision"
          }
        ],
        "decryption": "none"
      },
      "streamSettings": {
        "network": "tcp",
        "security": "reality",
    "realitySettings": {
      "show": false,
      "dest": "www.microsoft.com:443",
      "xver": 0,
      "serverNames": [
        "www.microsoft.com"
      ],
      "privateKey": "сюда вставить приватный ключ из выхлопа ./usr/local/bin/xray x25519",
      "minClientVer": "",
      "maxClientVer": "",
      "maxTimeDiff": 0,
      "shortIds": [
        "сюда вставить выхлоп команды openssl rand -hex 8"
      ]
    }
      },
      "sniffing": {
        "enabled": true,
        "destOverride": [
          "http",
          "tls"
        ]
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "tag": "direct"
    },
    {
      "protocol": "blackhole",
      "tag": "block"
    }
  ]
}
```
Единственное отличие при использовании скрипта заключается в том, что файлы конфигурации XRay будут храниться не в /opt/xray, а в /usr/local/etc/xray/.

Или вы можете установить XRay вручную. Для этого переходим по [ссылке](https://github.com/XTLS/Xray-core/releases) на последний релиз XRay-core и скачиваем самую свежую версию:
```
wget https://github.com/XTLS/Xray-core/releases/download/v1.8.9/Xray-linux-64.zip
```
Создаем каталог, распаковываем и делаем исполняемым файл (поскольку он поставляется в архиве .zip, права доступа могут быть потеряны при распаковке):
```
mkdir /opt/xray
unzip ./Xray-linux-64.zip -d /opt/xray
chmod +x /opt/xray/xray
```
Затем создаем systemd-юнит (nano /usr/lib/systemd/system/xray.service) и вставляем в него следующий текст
```
[Unit]
 Description=XRay
 [Service]
 Type=simple
 Restart=on-failure
 RestartSec=30
 WorkingDirectory=/opt/xray
 ExecStart=/opt/xray/xray run -c /opt/xray/config.json
 [Install]
 WantedBy=multi-user.target
```
Активируем:
```
systemctl daemon-reload
systemctl enable xray
```


## Настройка


Домен microsoft.com указан для примера, можно и нужно заменить на что‑то своё. Крайне рекомендуется воспользоваться [этим](https://github.com/XTLS/RealiTLScanner) для нахождения подходящего домена для маскировки. Основное требование — поддержка TLSv1.3. Лучше всего выбирать сайт физически хостящийся в стране вашего сервера.

Подготавливаем строки конфигурации, которую можно вставить в клиента; выглядят примерно так:

VLESS:
vless://<ID из конфига>@<IP адрес машины>:443?security=reality&encryption=none&pbk=<публичный ключ из конфига>&headerType=none&fp=chrome&type=tcp&flow=xtls-rprx-vision&sni=<значение домена в serverNames из конфига>&sid=<значение shortIds из конфига>#<название под которым профиль будет сохранён в приложении>

Shadowsocks-22:
ss://<пароль из конфига shadowsocks>@<IP адрес машины>:<порт shadowsocks из конфига>#<название под которым профиль будет сохранён в приложении>

Любую из них можно запихнуть в QR в виде plain текста, большинство клиентов умеют их импортировать.
