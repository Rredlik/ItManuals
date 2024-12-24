
Устанавливаем XRay на сервере:
```shell
apt install curl mc htop nano
bash -c "$(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install
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
Домен microsoft.com указан для примера, можно и нужно заменить на что‑то своё. Крайне рекомендуется воспользоваться этим[https://github.com/XTLS/RealiTLScanner] для нахождения подходящего домена для маскировки. Основное требование — поддержка TLSv1.3. Лучше всего выбирать сайт физически хостящийся в стране вашего сервера.
