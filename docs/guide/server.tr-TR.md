---
title: Sunucu
order: 2
nav:
  title: Kılavuz
  order: 1
group:
  title: Dağıtım
  order: 1
toc: content
---

## Çalışma Ortamı Gereksinimleri

| Bileşen           | Gereksinim                                  |
| ---------------- | ------------------------------------------- |
| Sunucu Yapılandırması | Minimum: 1 çekirdek CPU, 2GB RAM; Önerilen: 2 çekirdek CPU, 4GB RAM |
| MySQL            | 5.7 ve üzeri (Önerilen MySQL 8)            |
| Redis            | 6 ve üzeri                                  |
| NGINX/Apache     | Herhangi bir sürüm                          |

## Docker Kurulumu

Docker'ı hızlı bir şekilde kurmak için aşağıdaki komutu çalıştırın:

```sh
curl -fsSL https://get.docker.com | bash -s docker
```

## Hızlı Başlangıç

### Web Arayüzü ile Başlatma

PPanel, manuel yapılandırma sürecini basitleştiren web tabanlı bir başlatma desteği sunar.

### Başlatma Adımları

1. **Gerekli yapılandırma dosyalarını oluşturun**: Öncelikle `/app/etc/ppanel.yaml` dosyasını manuel olarak oluşturun ve yapılandırın.

2. **Docker konteynerini çalıştırın**:

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

3. **Başlatma sayfasına erişin**: Tarayıcınızda `http://SunucuIP:8080/init` adresine gidin.

4. **Yapılandırmayı tamamlayın**: Yönergeleri izleyerek yönetici hesabını, MySQL veritabanını ve Redis sunucusunu yapılandırın.

5. **Başlatma düğmesine tıklayın**: Yapılandırmayı tamamladıktan sonra "Başlat" düğmesine tıklayın, uygulama yapılandırmayı otomatik olarak kaydedecek ve yeniden başlatacaktır.

   > **Not**: PPanel varsayılan olarak **8080** portunu kullanır, lütfen bu portun erişilebilir olduğundan emin olun ve gerekirse güvenlik duvarı ayarlarını düzenleyin.

## Docker ile Dağıtım

PPanel varsayılan olarak **8080** portunu kullanır, lütfen bu portun erişilebilir olduğundan emin olun ve gerekirse güvenlik duvarı ayarlarını düzenleyin.

Web başlatma kullanmıyorsanız, varsayılan yönetici hesabıyla giriş yapabilirsiniz:

- **Kullanıcı Adı**: `admin@ppanel.dev`
- **Şifre**: `password`

> **Not**: İlk giriş yaptıktan sonra varsayılan şifreyi hemen değiştirin, böylece güvenliği sağlayın.

### Gerekli Yapılandırma Dosyalarını Oluşturma

Docker dağıtımına başlamadan önce, sistemin düzgün çalışmasını sağlamak için `/app/etc/ppanel.yaml` dosyasını manuel olarak oluşturun ve yapılandırın. Ayrıntılı yapılandırma örnekleri için belgenin [Yapılandırma Örnekleri](#yapılandırma-örnekleri) bölümüne bakın.

### Docker Konteynerini Çalıştırma

Konteyneri başlatmak için aşağıdaki komutu çalıştırın:

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

## Docker Compose ile Dağıtım

PPanel varsayılan olarak **8080** portunu kullanır, lütfen bu portun erişilebilir olduğundan emin olun ve gerekirse güvenlik duvarı ayarlarını düzenleyin.

### Gerekli Yapılandırma Dosyalarını Oluşturma

Docker Compose dağıtımına başlamadan önce, sistemin düzgün çalışmasını sağlamak için `/app/etc/ppanel.yaml` dosyasını manuel olarak oluşturun ve yapılandırın. Ayrıntılı yapılandırma örnekleri için belgenin [Yapılandırma Örnekleri](#yapılandırma-örnekleri) bölümüne bakın.

### Docker Compose Yapılandırma Dosyasını Oluşturma

`docker-compose.yml` dosyasını oluşturun:

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

### Servisi Başlatma

Servisi başlatmak için aşağıdaki komutu çalıştırın:

```sh
docker compose up -d
```

## Önemli Notlar

- **Güvenlik**: Uygulamanın güvenliğini sağlamak için varsayılan şifreyi ve anahtarı mutlaka değiştirin.
- **Port Yapılandırması**: Gerekli portların açık olduğundan emin olun, aksi takdirde ağ erişim sorunları yaşanabilir.
- **Veri Kalıcılığı**: Veri kalıcılığı sağlamak için konteyner çalıştırırken veri hacimlerini bağlamanızı öneririz.

## Yapılandırma Örnekleri

Aşağıda `ppanel.yaml` için yapılandırma örnekleri bulunmaktadır:

```yaml
Host: 0.0.0.0 # Dinleme IP'si
Port: 8080 # Çalışma portu
Debug: true # Hata ayıklama modunu aç
JwtAuth:
  AccessSecret: your-secret-key # Token anahtarı (lütfen değiştirin)
  AccessExpire: 604800 # Token geçerlilik süresi (saniye)
Logger:
  FilePath: ./ppanel.log # Günlük dosyası yolu
  MaxSize: 50
  MaxBackup: 3
  MaxAge: 30
  Compress: true
  LogType: json
  Level: info # Günlük seviyesi: info, debug, error
MySQL:
  Addr: 127.0.0.1:3306 # Veritabanı adresi
  Dbname: vpnboard # Veritabanı adı
  Username: root # Veritabanı kullanıcı adı
  Password: your-password # Veritabanı şifresi (lütfen değiştirin)
  Config: charset=utf8mb4&parseTime=true&loc=Asia%2FShanghai
  MaxIdleConns: 10
  MaxOpenConns: 10
  LogMode: dev
  LogZap: false
  SlowThreshold: 1000
Redis:
  Host: 127.0.0.1:6379
  Pass: # Redis şifresi (varsa)
  DB: 0

Administer:
  Password: password # Yönetici şifresi (değiştirilmelidir)
  Email: admin@ppanel.dev # Yönetici e-posta adresi
```

> **Not**: Yapılandırma dosyasını değiştirdikten sonra, değişikliklerin uygulanması için Docker konteynerini yeniden başlatın.

## Destek Alma

Herhangi bir sorunla karşılaşırsanız, lütfen resmi belgeleri inceleyin veya destek ekibiyle iletişime geçin.
