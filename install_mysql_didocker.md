
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

### Untuk mengurangi penggunaan memory mysql pada container
Untuk vm kecil mysql biasanya akan sangat berat dengan konsumsi diatas 500Mb, untuk menguranginya bisa menggunakan cara dibawah ini

1. **buat file ``my.cnf`` sebagai berikut :**

   ```sh
   [mysqld]
   #### optimisasi memory
   innodb_buffer_pool_size=5M
   innodb_log_buffer_size=256K
   max_connections=10
   key_buffer_size=8K
   performance_schema = 0
   thread_cache_size=0
   host_cache_size=0
   innodb_ft_cache_size=1600000
   innodb_ft_total_cache_size=32000000
   
   # tambahan untuk mengurangi memory
   table_open_cache = 200
   table_definition_cache = 100
   tmp_table_size = 1K
   max_heap_table_size = 16K
   
   # per thread or per operation settings
   thread_stack=196K
   sort_buffer_size=512K
   read_buffer_size=256K
   read_rnd_buffer_size=512k
   bulk_insert_buffer_size=0
   join_buffer_size=128K
   net_buffer_length=1K
   innodb_sort_buffer_size=64K
   
   #### performance di off
   performance_schema = off
   
   host-cache-size=0
   skip-name-resolve
   datadir=/var/lib/mysql
   socket=/var/run/mysqld/mysqld.sock
   secure-file-priv=/var/lib/mysql-files
   user=mysql
   
   pid-file=/var/run/mysqld/mysqld.pid
   [client]
   socket=/var/run/mysqld/mysqld.sock
   ```

2. **masukan file tersebut ke lingkungan server**

   bisa menggunakan software ftp atau scp seperti WindSCP :https://github.com/winscp/winscp, atau cara lainnya.
4. **copy file tersebut dalam directory container untuk mengganti cnf default :**
   ```sh
   docker cp /path_yourfolder/my.cnf mysql-container:/etc/my.cnf
   ```
5. **lakukan restart container :**
   ```sh
   docker restart mysql-container
   ```


