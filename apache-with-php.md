1. Update daftar paket
  ```sh
  sudo apt update
  ```

2. Install PHP dan modul php dasar untuk apache

  ```sh
  sudo apt install php libapache2-mod-php
  ```
3. Install PHP dan modul php dasar untuk apache

  ```sh
sudo systemctl restart apache2
  ```
4. Cek apakah php ada

  ```sh
php -v
  ```

5. Cek ekstensi tersedia

  ```sh
php -m
  ```

6. Install beberapa ekstensi tambahan untuk codeigniter

  ```sh
apt install php-imagick
apt install php-gd
apt install php-mysqli
  ```

5. Untuk remove instalasi modul terkait
hapus paket tanpa hapus file konifgurasi
  ```sh
sudo apt remove php libapache2-mod-php
  ```
menghapus paket dan file konfigurasi
  ```sh
sudo apt purge php libapache2-mod-php
  ```
hapus paket tambahan php
  ```sh
sudo apt purge php-imagick php-pdo php-mysql php-mysqli
  ```
hapus paket lain yang tidak diperlukan
  ```sh
sudo apt autoremove
  ```
restart apache
  ```sh
sudo systemctl restart apache2
  ```
pastikan php terhapus
  ```sh
php -v
  ```
