## PostgreSQL di Docker
pada konfigurasi database suatu aplikasi pastikan bahwa akun (username dan password) adalah pemilik dari database tersebut.

- #### Masuk ke dalam bash kontainer menggunakan docker exec:
  `docker exec -it <container_name_or_id> bash`

- #### Setelah masuk ke dalam psql (untuk melakukan query):
  `psql -U <username>`

  atau bisa juga bila langsung ingin ke db tertentu :

  `psql -U <username> -d <database_name>`
  
- #### Masuk ke dalam konsol psql juga bisa langsung :
  `docker exec -it <container_name_or_id> psql -U <username> -d <database_name>`

- #### Untuk keluar dari konsol psql :
  `\q`

- #### Untuk keluar dari bash kontainer :
  `exit`

- #### Beberapa command yang berbeda dengan mysql :
  - use database : `\c <nama_database>` --> `USE <nama_database>;`
  - daftar table : `\dt` --> `SHOW TABLES;`
  - daftar database : `\l` --> `SHOW DATABASES;`
  - skema table : `\d <nama_tabel>` --> `DESCRIBE <nama_tabel>;`
  - lihat kolom : `\d <nama_tabel>` --> `SHOW COLUMNS FROM <nama_tabel>;`

- #### Untuk Backup database :
  `docker exec -t <container_name_or_id> pg_dump -U <username> -d <database_name> > <backup_file.sql>`

- #### Untuk restore database :
  `cat <backup_file.sql> | docker exec -i <container_name_or_id> psql -U <username> -d <database_name>`

