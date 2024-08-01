
### Menjalankan MySQL di dalam Docker
1. **Tarik image MySQL dari Docker Hub:**
   ```sh
   docker pull mysql:latest
   ```

2. **Jalankan container MySQL:**
   ```sh
   docker run --name mysql-container -e MYSQL_ROOT_PASSWORD=<terserah> -p 3306:3306 -d mysql:latest
   ```
   **ada pesan error kalo ga ngasih salah satu dibawah ini**
   
    You need to specify one of the following as an environment variable:
    - MYSQL_ROOT_PASSWORD
    - MYSQL_ALLOW_EMPTY_PASSWORD
    - MYSQL_RANDOM_ROOT_PASSWORD


### Mengimpor file SQL ke dalam MySQL
1.  **Salin file SQL ke dalam container MySQL:**
   ```sh
   docker cp /path/to/<yourFile.sql> mysql-container:/<yourFile.sql>l
   ```

2. **Masuk ke dalam container MySQL:**
   ```sh
   docker exec -it mysql-container bash
   ```
3. **Pastikan sudah buat database terlebih dahulu**

4. **Import file SQL ke dalam database:**
   ```sh
   mysql -u root -p <database_name> < /<yourFile.sql>
   ```


### Check bahwa user database ada dan bisa connect 
 Biasanya terdapat problem bila mengakses diluar container seperti Host '172.18.0.1' is not allowed to connect to this MySQL server
1. **Masuk ke container:**
   ```sh
   docker exec -it mysql-container bash
   ```

2. **Connect ke Mysql db:**
   ```sh
   mysql -u your_user -p
   ```

3. **Lihat list semua user yang ada :**
   ```sh
   SELECT host, user FROM mysql.user;
   ```

4. **Lihat list semua user yang ada :**
   ```
    +------------+------------------+
    | host       | user             |
    +------------+------------------+
    | %          | root             |
    | 127.0.0.1  | root             |
    | ::1        | root             |
    | localhost  | mysql.sys        |
    | localhost  | root             |
    | localhost  | sonar            |
    +------------+------------------+
   ```
 **Jika ada entri dengan host sebagai %, maka pengguna dapat terhubung dari semua alamat IP.**


 sumber lengkap di : https://github.com/docker-library/mysql/issues/275


untuk menambahkan user root dengan host % di MySQL:
```sh
CREATE USER 'root'@'%' IDENTIFIED BY 'root_password';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

 
