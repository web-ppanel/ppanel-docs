---
title: Серверна частина
order: 2
nav:
  title: Посібник
  order: 1
group:
  title: Розгортання
  order: 1
toc: content
---

## Вимоги до середовища виконання

| Компонент            | Вимоги                                                                    |
| -------------------- | ------------------------------------------------------------------------- |
| Конфігурація сервера | Мінімум: 1 ядро CPU, 2GB пам'яті; Рекомендується: 2 ядра CPU, 4GB пам'яті |
| MySQL                | 5.7 і вище (рекомендується MySQL 8)                                       |
| Redis                | 6 і вище                                                                  |
| NGINX/Apache         | Будь-яка версія                                                           |

## Встановлення Docker

Запустіть наступну команду для швидкого встановлення Docker:

```sh
curl -fsSL https://get.docker.com | bash -s docker
```

## Швидкий старт

### Ініціалізація через веб-інтерфейс

PPanel підтримує веб-ініціалізацію, що спрощує процес ручного налаштування.

### Кроки ініціалізації

1. **Створіть необхідні конфігураційні файли**: спочатку вручну створіть і налаштуйте файл `/app/etc/ppanel.yaml` для подальшого налаштування.

2. **Запустіть контейнер Docker**:

   ```sh
   docker run -d \
     --network host \
     --name ppanel-server \
     -v /app/etc/ppanel.yaml:/app/etc/ppanel.yaml \
     --restart=always \
     --log-opt max-size=10m \
     --log-opt max-file=3 \
     ppanel/ppanel-server:beta
   ```

3. **Перейдіть на сторінку ініціалізації**: у браузері перейдіть за адресою `http://IP_сервера:8080/init`.

4. **Завершіть налаштування**: дотримуйтесь інструкцій для налаштування облікового запису адміністратора, конфігурації бази даних MySQL та сервера Redis.

5. **Натисніть кнопку ініціалізації**: після завершення налаштування натисніть кнопку "Ініціалізувати", програма автоматично зберегти налаштування та перезапуститься.

   > **Увага**: PPanel за замовчуванням використовує порт **8080**, будь ласка, переконайтеся, що цей порт доступний, за необхідності налаштуйте параметри брандмауера.

## Використання Docker для розгортання

PPanel за замовчуванням використовує порт **8080**, будь ласка, переконайтеся, що цей порт доступний, за необхідності налаштуйте параметри брандмауера.

Якщо ви не використовуєте веб-ініціалізацію, ви можете увійти за замовчуванням з обліковим записом адміністратора:

- **Ім'я користувача**: `admin@ppanel.dev`
- **Пароль**: `password`

> **Увага**: після першого входу терміново змініть пароль за замовчуванням для забезпечення безпеки.

### Створення необхідних конфігураційних файлів

Перед початком розгортання Docker спочатку вручну створіть і налаштуйте файл `/app/etc/ppanel.yaml`, щоб забезпечити нормальну роботу системи. Для детальних прикладів конфігураційних параметрів зверніться до розділу [Приклади конфігураційних параметрів](#приклади-конфігураційних-параметрів).

### Запуск контейнера Docker

Запустіть наступну команду для запуску контейнера:

```sh
docker run -d \
  --network host \
  --name ppanel-server \
  -v /app/etc/ppanel.yaml:/app/etc/ppanel.yaml \
  --restart=always \
  --log-opt max-size=10m \
  --log-opt max-file=3 \
  ppanel/ppanel-server:beta
```

## Використання Docker Compose для розгортання

PPanel за замовчуванням використовує порт **8080**, будь ласка, переконайтеся, що цей порт доступний, за необхідності налаштуйте параметри брандмауера.

### Створення необхідних конфігураційних файлів

Перед початком розгортання Docker Compose спочатку вручну створіть і налаштуйте файл `/app/etc/ppanel.yaml`, щоб забезпечити нормальну роботу системи. Для детальних прикладів конфігураційних параметрів зверніться до розділу [Приклади конфігураційних параметрів](#приклади-конфігураційних-параметрів).

### Створення конфігураційного файлу Docker Compose

Створіть файл `docker-compose.yml`:

```yaml
version: '3'

services:
  ppanel-server:
    image: ppanel/ppanel-server:beta
    container_name: ppanel-server
    network_mode: host
    volumes:
      - /app/etc/ppanel.yaml:/app/etc/ppanel.yaml
    restart: always
    logging:
      driver: 'json-file'
      options:
        max-size: '10m'
        max-file: '3'
```

### Запуск служби

Запустіть наступну команду для запуску служби:

```sh
docker compose up -d
```

## Важливі зауваження

- **Безпека**: обов'язково змініть пароль і ключі за замовчуванням для забезпечення безпеки програми.
- **Конфігурація портів**: переконайтеся, що необхідні порти відкриті, щоб уникнути проблем з доступом до мережі.
- **Збереження даних**: рекомендується монтувати томи даних під час запуску контейнера для забезпечення збереження даних.

## Приклади конфігураційних параметрів

Ось приклад конфігураційних параметрів для `ppanel.yaml`:

```yaml
Host: 0.0.0.0 # IP для прослуховування
Port: 8080 # Порт для роботи
Debug: true # Чи увімкнено режим налагодження
JwtAuth:
  AccessSecret: your-secret-key # Ключ токена (будь ласка, змініть)
  AccessExpire: 604800 # Термін дії токена (в секундах)
Logger:
  FilePath: ./ppanel.log # Шлях до файлу журналу
  MaxSize: 50
  MaxBackup: 3
  MaxAge: 30
  Compress: true
  LogType: json
  Level: info # Рівень журналу: info, debug, error
MySQL:
  Addr: 127.0.0.1:3306 # Адреса бази даних
  Dbname: vpnboard # Назва бази даних
  Username: root # Ім'я користувача бази даних
  Password: your-password # Пароль бази даних (будь ласка, змініть)
  Config: charset=utf8mb4&parseTime=true&loc=Asia%2FShanghai
  MaxIdleConns: 10
  MaxOpenConns: 10
  LogMode: dev
  LogZap: false
  SlowThreshold: 1000
Redis:
  Host: 127.0.0.1:6379
  Pass: # Пароль Redis (якщо є)
  DB: 0

Administer:
  Password: password # Пароль адміністратора (повинен бути змінений)
  Email: admin@ppanel.dev # Адреса електронної пошти адміністратора
```

> **Увага**: після зміни конфігураційного файлу перезапустіть контейнер Docker, щоб застосувати зміни.

## Отримання підтримки

Якщо у вас виникли проблеми, будь ласка, зверніться до офіційної документації або зв'яжіться з командою підтримки для отримання допомоги.
