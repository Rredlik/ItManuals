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
openssl rand -base64 16
./usr/local/bin/xray x25519
openssl rand -hex 8
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
    "rules": [
      {
        "type": "field",
        "protocol": "bittorrent",
        "outboundTag": "block"
      }
    ],
    "domainStrategy": "IPIfNonMatch"
  },
  "inbounds": [
    {
      "listen": "IP_адрес_вашего_сервера",
      "port": 443,
      "protocol": "vless",
      "tag": "reality-in",
      "settings": {
        "clients": [
          {
            "id": "сюда вставить ID из выхлопа ./usr/local/bin/xray uuid",
            "email": "user@server",
            "flow": "xtls-rprx-vision"
          }
        ],
        "decryption": "none"
      }
    },
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
      "streamSettings": {
        "network": "tcp",
        "security": "reality",
    "realitySettings": {
      "show": false,
      "dest": "ваш_маскировочный_домен:443", ## (www.microsoft.com:443)
      "xver": 0,
      "serverNames": [
        "ваш_маскировочный_домен" 
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
          "tls",
          "quic"
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

## Настройка VLESS c XTLS-Reality

Сначала нужно определиться, под какой сайт вы будете маскироваться.

Вариант раз: это должно быть какой‑то очень популярный иностранный сайт, желательно не связанный с политикой и СМИ, не относящийся к «нежелательным организациям», и к которому наврядли возникнут вопросы со стороны органов РФ (например, youtube.com — довольно плохой выбор в этом плане, есть шанс блокировки в будущем). Просто попробуйте вспомнить какой‑нибудь ресурс, которым пользуются много людей и компаний, который цензоры до последнего не будет блокировать без большой нужды, чтобы не разломать пол‑интернета (и нет, это не гугл). Предлагать варианты тут не буду, пусть каждый выберет какой‑то свой. И да, не обязательно использовать главный домент сайта, можно брать какие‑то из поддоменов.

Выбрав домен, проверяем, что он подходит под наши критерии командой curl. Например, для www.microsoft.com команда будет выглядеть так:

```
curl -v -L --tlsv1.3 --http2 https://www.microsoft.com
```

Важно отметить тот факт, что XTLS‑Reality имеет недостаток — если сайт, под который вы маскируетесь, испытывает технические проблемы (например, временно отключил TLSv1.3), или огородился от вашего сервера (устав от проксируемых запросов на установление соединений), или попал под блокировку, то точно так же перестанет работать и ваш прокси. В этом случае совершенно нормально будет поменять используемый маскировочный домен на какой‑то другой. Мы потом еще настроим на сервере Shadowsocks и SSH как запасной вариант на случай проблем, а пока продолжаем с настройкой VLESS.
Меняем домен в конфиге:
```shell
nano /usr/local/etc/xray/config.json
```
