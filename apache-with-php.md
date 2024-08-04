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
      apt install php-curl
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
      sudo apt purge php-imagick php-pdo php-mysql php-mysqli php-curl
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

6. Bila pada file php nantinya terdapat htaccess maka pada bagian virtual host setting
    ```SH
    <Directory /var/www/html>
        AllowOverride All
    </Directory>
    ```
    pastikan mod_rewrite diaktifkan
     ```sh
     sudo a2enmod rewrite
     ```
    lalu restart setelah melakukan perubahan
    ```sh
    sudo systemctl restart apache2
    ```

<br>

## Bila fitur upload, write file, delete file, dsj pada server ubuntu tidak berjalan

Jika ada fitur upload, delete file di folder, write data disuatu file, dsj yang mempengaruhi perubahan suatu file atau folder maka perlu mengganti kepemilikan dari file atau folder tersebut. 

1. Cek kepemilikan dan hak akses dari suatu file atau folder dengan
   ```sh
   sudo ls -l <folder>
   ```
3. Ganti kepemilikan ke www-data:www-data agar dapat melakukan perubahan melalui fitur web
   ```sh
   sudo chown -R www-data:www-data /var/www/html/<folder_upload_atau_eksekusi_file>
   ```
5. Bila fitur tidak berjalan mungkin ada perbedaan user/groups, bisa dicek dengan
   ```sh
   cat /etc/apache2/apache2.conf | grep -E '^User|^Group'
   ```
   Lalu cek env_var sesuai dengan value diatas tersebut
   ```sh
   cat /etc/apache2/envvars | grep -E 'APACHE_RUN_USER|APACHE_RUN_GROUP'
   ```
   
