#### Peringatan : Guide disini mungkin tidak mengajarkan cara penggunaan Nginx dan Setting SSL dan tidak terikat dengan deployment aplikasi tertentu. Sehingga harus memahami dan menyambungkan petunjuk disini dengan halaman : https://github.com/Nanang-Wahyudi/DeployMOOC untuk konfigurasi Nginx dan SSL terutama untuk aplikasi Spring Boot
  
## 1. Hal yang di lakukan di lokal
1.  #### lakukan pembuatan image
    **Perintah untuk membangun image berdasar dockerfile adalah :**
    
    ```sh
    docker build -t <nama_image> .
    ```
  
2.  #### coba jalankan pada lokal
    **Perintah untuk menjalankan container adalah :**
    ```sh
    docker run -dti -e <nama_var1>='<value_var1>' -e <nama_var2>='<value_var2>' -p <port-host>:<port-container> --name <nama_container> <nama_image>
    ```
    
    - Cara membuat variable lingkungan dapat menambahkan sebanyak yang dibutuhkan dengan mengulang opsi -e.
    - Untuk penjelasan lainnya dapat diakses pada url berikut : 
      
    atau bila env-nya banyak bisa juga menggunakan file .list, dengan :
    ```sh
    docker run -dti --env-file </directory/nama_file> -p <port-host>:<port-container> --name <nama_container> <nama_image>
    ```

3. #### bila berjalan lancar maka push image ke dockerhub
    **Perintah untuk login (pastikan sudah login untuk melakukan push ke repo) :**
    ```sh
    docker login
    ```
    
    lalu masukan username dan password.
    bila ingin login ke registry Docker lainnya, gunakan perintah :
    ```sh
    sh docker login <registry_URL>
    ```
    
    #### Perintah untuk membuat tag :
    ```sh
    docker tag <nama_image> <username>/<nama_image>
    ```
    #### Perintah untuk melakukan push ke dockerhub :
    ```sh
    docker push <hasil_tag>
    ```
  
4. #### Otomatis akan dibuatkan repository bila belum membuat (namun public), jangan lupa setting ke private bila perlu (bisa melalui dockerhub browser).
  
<br><br>
## 2. Hal yang di lakukan di server (Ubuntu OS)

  #### cara install docker di ubuntu

1. **Perbarui Daftar Paket:**
   ```sh
   sudo apt update
   ```

2. **Instal Paket Prasyarat:**
   ```sh
   sudo apt install apt-transport-https ca-certificates curl software-properties-common
   ```

3. **Tambahkan GPG Key Docker:**
   ```sh
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   ```

4. **Tambahkan Repositori Docker:**
   ```sh
   echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

5. **Perbarui Daftar Paket Lagi:**
   ```sh
   sudo apt update
   ```

6. **Instal Docker:**
   ```sh
   sudo apt install docker-ce
   ```

7. **Cek Status Docker:**
   ```sh
   sudo systemctl status docker
   ```

   Untuk memastikan Docker berjalan dengan baik, Anda bisa melihat statusnya di sini.

8. **Verifikasi Instalasi Docker:**
   ```sh
   docker --version
   ```

   Anda juga dapat menjalankan contoh container untuk memastikan semuanya berjalan dengan baik:
   ```sh
   docker run hello-world
   ```


- #### pastikan Nginx dan Certbot (SSL) sudah diinstall dan dikonfigurasikan sesuai url berikut : 
  - Bagian NGINX : https://github.com/Nanang-Wahyudi/DeployMOOC?tab=readme-ov-file#create-an-nginx-config-file\
  - Bagian SSL : https://github.com/Nanang-Wahyudi/DeployMOOC?tab=readme-ov-file#sslhttps
 
- #### Database di docker
  Untuk database guide ada <a href="https://github.com/Habbatul/Guide-Documentation-NgulikPribadi/blob/main/postgresinDocker.md">Disini</a>

### Berkaitan dengan container aplikasi kamu :

- #### lakukan pull dari dockerhub :
  #### lakukan login seperti sebelumnya di lokal
  ```sh
  docker login
  ```
  
  #### Lakukan pull repo
  ```sh
  docker pull <username>/<nama_image>:<tag>`
  ```
  
  atau jika ingin menarik image dari registry Docker lainnya

  ```sh
  docker pull <registry_URL>/<nama_image>:<tag>
  ```

- #### Melakukan Running Container di Server :
  #### Lakukan pembuatan file env (bila env nya banyak)
  ```sh
  vim /etc/env.list
  ```

  lalu buat `env.list`, seperti :
  ```sh
   url_postgres=jdbc:uhuy://111.11.0.1:1111/uhuy
   username_db=uhuy 
   password_db=uhuy
   ...
  ```
   
  #### Jalankan perintah docker run (seperti sebelumnya di lokal)
  <b>Peringatan </b> : Disini belum melakukan setingan Docker Network sehingga akan default network bridge pada container yang berjalan

  ```sh
  docker run -dti --env-file` </directory/nama_file> -p <port-host>:<port-container> --name <nama_container> <nama_image>
  ```

  #### Cek apakah container berjalan
  ```sh
  docker ps
  ```

  #### Cek log aplikasi/container
  ```sh
  docker logs -f <nama_container>` atau `docker logs --tail 5 <nama_container>
  ```

<br><br>
## 3. Peringatan untuk Aplikasi yang Membutuhkan Database (Berjalan pada Container yang Sama)
  Ketika menjalankan aplikasi Spring Boot di dalam container Docker dan tidak mendefinisikan jaringan Docker yang khusus, maka secara default aplikasi akan berjalan di dalam jaringan yang disebut <b>bridge network</b>. Dalam konfigurasi bridge network, container Docker akan mendapatkan alamat IP yang berbeda dari host yang menjalankan Docker.
  <br>
  Ketika merujuk ke localhost dari dalam aplikasi Spring Boot yang berjalan di dalam container, aplikasi tersebut akan mencoba terhubung ke alamat IP lokal di dalam container itu sendiri. Namun, karena alamat IP container tidak sama dengan alamat IP host, maka aplikasi tidak akan dapat terhubung ke layanan eksternal yang berjalan di host.
  <br>
  
  Untuk mengatasi ini ada beberapa cara :
  - Bisa melakukan inspect `docker inspect my_postgres`. Lalu scroll kebawah sampai menemukan container akan berjalan default pada network bridge, lalu gunakan ip address pada bagaian bridge tersebut sebagai pengganti keyword 'localhost' pada konfigurasi database contoh :
  
    `url_postgres=jdbc:postgresql://localhost:<port-host>/<database_name>` ubah menjadi `url_postgres=jdbc:postgresql://<alamat_ip>:<port-host>/<database_name>` 
    
    contoh :
  
    `url_postgres=jdbc:postgresql://172.17.0.2:5432/database_name`
  
  - Bisa menggunakan settingan docker network sesuai pada url : 

  
  

