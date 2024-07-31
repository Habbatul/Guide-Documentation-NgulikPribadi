### 1. Ubah Port di ports.conf

Jika ingin mengubah port Apache2 dari port 80 ke port lain (misalnya 8080), perlu mengubah file ports.conf. Ubah Listen 80 menjadi Listen 8080:

   apache
  ```sh
  Listen 8080
  
  <IfModule ssl_module>
          Listen 443
  </IfModule>
  
  <IfModule mod_gnutls.c>
          Listen 443
  </IfModule>
  ```

### 2. Ubah Port di Virtual Hosts

Pastikan bahwa semua konfigurasi virtual host juga diperbarui untuk mencerminkan port baru. Misalnya, file konfigurasi virtual host default biasanya ada di /etc/apache2/sites-enabled/000-default.conf. Ubah baris VirtualHost dari port 80 ke port 8080:

apache
  ```sh
<VirtualHost *:8080>
    # Konfigurasi lainnya
</VirtualHost>
```

### 3. Ubah Port di Virtual Host SSL (Jika Ada)

Jika menggunakan SSL, perlu memeriksa dan memperbarui konfigurasi virtual host SSL. Misalnya, jika menggunakan file /etc/apache2/sites-enabled/default-ssl.conf, pastikan portnya diubah dari 443 (atau port lain) sesuai kebutuhan:

apache
  ```sh
<VirtualHost _default_:443>
    # Konfigurasi SSL lainnya
</VirtualHost>
```

### 4. Restart Apache2

Setelah melakukan perubahan, restart Apache2 untuk menerapkan konfigurasi baru:

bash
  ```sh
sudo systemctl restart apache2
```

### 5. Periksa Konfigurasi Apache2

Untuk memastikan bahwa tidak ada kesalahan konfigurasi yang menghalangi Apache2 dari memulai, jalankan perintah berikut untuk memeriksa konfigurasi:

bash
  ```sh
sudo apache2ctl configtest
```

Jika ada kesalahan, perbaiki sesuai petunjuk yang ditampilkan.

### 6. Cek Jika Apache2 Berjalan

Pastikan Apache2 berjalan dan mendengarkan pada port yang baru:

bash
  ```sh
sudo netstat -tuln | grep 8080
```
atau
  ```sh
lsof -i :8080
```

harus melihat output yang menunjukkan bahwa Apache2 mendengarkan di port 8080.

### 7. Periksa Log

Jika Apache2 masih tidak mau memulai atau jika ada masalah, periksa log error Apache2 untuk informasi lebih lanjut:

bash
  ```sh
sudo tail -f /var/log/apache2/error.log
```

### 8. Perbarui Konfigurasi Nginx

Jika sudah memverifikasi bahwa Apache2 berfungsi pada port baru, pastikan konfigurasi Nginx untuk reverse proxy juga diperbarui dengan port yang benar. Misalnya:

nginx
```sh
server {
    listen 80;
    server_name <domain_name>;

    location / {
        proxy_pass http://localhost:8080; # Ganti dengan port Apache2 yang baru
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location ~ /\.ht {
        deny all;
    }
}
```
