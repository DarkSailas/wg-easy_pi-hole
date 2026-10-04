# wg-easy + Pi-hole

Docker Compose file that runs a WireGuard server with the [wg-easy](https://github.com/wg-easy/wg-easy) web panel next to [Pi-hole](https://github.com/pi-hole/pi-hole). VPN clients use Pi-hole as their DNS server, so ads and trackers are filtered for every connected device.

[Русская версия](#русская-версия)

## What is inside

| File | Purpose |
|---|---|
| `docker-compose.yml` | two services, `wg-easy` and `pihole`, in one bridge network `172.18.0.0/16` |
| `wg.env` | settings of wg-easy |
| `pi-hole.env` | settings of Pi-hole |

Addresses inside the network are fixed: wg-easy is `172.18.0.2`, Pi-hole is `172.18.0.3`.

## Requirements

- Linux host with Docker and the Compose plugin
- WireGuard kernel module on the host
- UDP port 51820 reachable from the internet

## Setup

1. Clone the repository.
2. Fill in `wg.env`:

   | Key | Value |
   |---|---|
   | `WG_HOST` | public IP address or domain name of the server |
   | `PASSWORD` | password for the wg-easy web panel |
   | `WG_DEFAULT_DNS` | address of the Pi-hole container, `172.18.0.3` |

3. Fill in `pi-hole.env`. The file ships with `KEY: value` lines; Docker expects `KEY=value`, so write it this way:

   ```
   TZ=Europe/Moscow
   WEBPASSWORD=your-password
   ```

   `TZ` is optional.
4. Start the containers:

   ```
   docker compose up -d
   ```

Do not commit the filled-in env files: they hold passwords.

## Ports

| Port on the host | Service |
|---|---|
| 51820/udp | WireGuard |
| 51821/tcp | wg-easy web panel |
| 54/tcp, 54/udp | Pi-hole DNS |
| 67/udp | Pi-hole DHCP |
| 83/tcp | Pi-hole web panel, `http://<server>:83/admin` |

VPN clients reach Pi-hole DNS directly at `172.18.0.3:53`; host port 54 is only for queries from outside the VPN.

## Data

WireGuard configuration is stored in `./wireguard/conf`, Pi-hole data in `./etc-pihole` and `./etc-dnsmasq.d`. These folders appear next to the compose file on first start.

## Notes

The compose file uses the image `weejewel/wg-easy:latest`. The project has since moved to `ghcr.io/wg-easy/wg-easy`, and its newer versions take a password hash instead of `PASSWORD`. Check the wg-easy documentation before changing the image.

---

## Русская версия

Файл Docker Compose, который поднимает сервер WireGuard с веб-панелью [wg-easy](https://github.com/wg-easy/wg-easy) и рядом [Pi-hole](https://github.com/pi-hole/pi-hole). Клиенты VPN получают Pi-hole как DNS-сервер, поэтому реклама и трекеры режутся на всех подключённых устройствах.

### Состав

| Файл | Назначение |
|---|---|
| `docker-compose.yml` | две службы, `wg-easy` и `pihole`, в одной сети `172.18.0.0/16` |
| `wg.env` | настройки wg-easy |
| `pi-hole.env` | настройки Pi-hole |

Адреса внутри сети постоянные: wg-easy — `172.18.0.2`, Pi-hole — `172.18.0.3`.

### Требования

- Linux с Docker и плагином Compose
- модуль ядра WireGuard на хосте
- порт 51820/udp, доступный из интернета

### Установка

1. Клонируйте репозиторий.
2. Заполните `wg.env`:

   | Ключ | Значение |
   |---|---|
   | `WG_HOST` | внешний IP-адрес или доменное имя сервера |
   | `PASSWORD` | пароль веб-панели wg-easy |
   | `WG_DEFAULT_DNS` | адрес контейнера Pi-hole, `172.18.0.3` |

3. Заполните `pi-hole.env`. В файле строки записаны как `KEY: value`, а Docker ждёт `KEY=value`, поэтому пишите так:

   ```
   TZ=Europe/Moscow
   WEBPASSWORD=ваш-пароль
   ```

   `TZ` указывать необязательно.
4. Запустите контейнеры:

   ```
   docker compose up -d
   ```

Заполненные env-файлы не коммитьте: в них пароли.

### Порты

| Порт на хосте | Служба |
|---|---|
| 51820/udp | WireGuard |
| 51821/tcp | веб-панель wg-easy |
| 54/tcp, 54/udp | DNS Pi-hole |
| 67/udp | DHCP Pi-hole |
| 83/tcp | веб-панель Pi-hole, `http://<сервер>:83/admin` |

Клиенты VPN обращаются к DNS Pi-hole напрямую по адресу `172.18.0.3:53`; порт 54 на хосте нужен только для запросов не из VPN.

### Данные

Конфигурация WireGuard лежит в `./wireguard/conf`, данные Pi-hole — в `./etc-pihole` и `./etc-dnsmasq.d`. Папки появляются рядом с compose-файлом при первом запуске.

### Примечания

В compose-файле указан образ `weejewel/wg-easy:latest`. Проект переехал на `ghcr.io/wg-easy/wg-easy`, и новые версии принимают хеш пароля вместо `PASSWORD`. Перед сменой образа сверьтесь с документацией wg-easy.
