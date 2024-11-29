

### **1. Pastikan Docker dan Docker Compose Terinstal**
1. Pastikan sudah menginstall docker

2. Instal Plugin Docker untuk Docker Compose:
   ```bash
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   ```

3. Verifikasi instalasi:
   ```bash
   docker --version
   docker compose version
   ```


### **2. Siapkan Struktur Direktori**

Buat direktori di server Ubuntu untuk menyimpan konfigurasi Nginx dan sertifikat Certbot:

contohnya :

```bash
sudo mkdir -p /srv/nginx/conf.d    # Untuk Konfigurasi Nginx
sudo mkdir -p /srv/nginx/certs     # Untuk Sertifikat SSL
sudo mkdir -p /srv/nginx/html      # Untuk Root untuk validasi SSL (nanti di nginx untuk test certbot arahkan kesini)
```


### **3. Buat File `docker-compose.yml`**
Buat file `docker-compose.yml` di server (sesuaikan versi tag nya):

```yaml
version: '3.8'

services:
  nginx:
    image: nginx:latest
    container_name: nginx-proxy
    ports:
      - "80:80"   # HTTP
      - "443:443" # HTTPS
    volumes:
      - /srv/nginx/conf.d:/etc/nginx/conf.d:ro        # Folder server untuk Konfigurasi Nginx
      - /srv/nginx/certs:/etc/letsencrypt:ro          # Folder server Sertifikat SSL
      - /srv/nginx/html:/usr/share/nginx/html:ro      # Folder server untuk validasi SSL
    network_mode: host  # Menjalankan container dengan jaringan host
    restart: always     # Pastikan restart diatur di dalam layanan

  certbot:
    image: certbot/certbot:latest
    container_name: certbot
    volumes:
      - /srv/nginx/certs:/etc/letsencrypt             # Folder server untuk Penyimpanan sertifikat
      - /srv/nginx/html:/usr/share/nginx/html         # Folder server untuk Root untuk validasi
    entrypoint: "/bin/sh -c 'trap exit TERM; while :; do sleep 2073600; done'" # Agar container tetap berjalan
    restart: always     # Menambahkan restart di layanan certbot

```


### **4. Konfigurasi Nginx**
Buat file konfigurasi Nginx di `/srv/nginx/conf.d/default.conf` :

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    location /.well-known/acme-challenge/ {
        root /usr/share/nginx/html; # Folder untuk validasi SSL Certbot
    }

    location / {
        # Perhatikan bahwa sebelumnya di docker compose nginx network_mode = host
        proxy_pass http://localhost:8080; # Ganti dengan alamat aplikasi
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

#kalo certbot sudah aktif tambahkan ini 
server {
    listen 443 ssl;
    server_name yourdomain.com www.yourdomain.com;

    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;

    location / {
        # Perhatikan bahwa sebelumnya di docker compose nginx network_mode = host
        proxy_pass http://localhost:8080; # Ganti dengan alamat aplikasi
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Ganti `yourdomain.com` dengan nama domain aplikasi.


### **5. Jalankan Docker Compose**
Mulai layanan dengan:

```bash
docker compose up -d
```

- Nginx sekarang berjalan di container dengan pengaturan di `/srv/nginx`.
- Folder `/srv/nginx/certs` digunakan untuk sertifikat SSL.



### **6. Dapatkan Sertifikat SSL**
Untuk meminta sertifikat SSL dengan Certbot:

```bash
docker run --rm \
  -v /srv/nginx/certs:/etc/letsencrypt \
  -v /srv/nginx/html:/usr/share/nginx/html \
  certbot/certbot certonly --webroot \
  -w /usr/share/nginx/html \
  -d yourdomain.com \
  --email your-email@example.com \
  --agree-tos \
  --no-eff-email
```

Setelah berhasil, sertifikat akan disimpan di:
- `/srv/nginx/certs/live/yourdomain.com/fullchain.pem`
- `/srv/nginx/certs/live/yourdomain.com/privkey.pem`



### **7. Restart Nginx**
Muat ulang Nginx untuk mulai menggunakan sertifikat:

```bash
docker compose restart nginx
```


### **8. Otomatisasi Pembaruan Sertifikat**
Tambahkan cron job untuk memperbarui sertifikat otomatis:
--Not Yet

---

### Penjelasan
1. **Volume di Server**: Semua file konfigurasi, sertifikat, dan web root disimpan di `/srv/nginx`, sehingga dapat dikelola langsung di server tanpa masuk ke container.
2. **Certbot**: Container Certbot digunakan untuk validasi dan memperbarui sertifikat SSL.
3. **Nginx**: Container Nginx menggunakan volume untuk membaca file konfigurasi dan sertifikat secara langsung.
