#### Peringatan : Guide disini mungkin tidak mengajarkan cara penggunaan Nginx dan Setting SSL dan tidak terikat dengan deployment aplikasi tertentu. Sehingga harus memahami dan menyambungkan petunjuk disini dengan halaman : https://github.com/Nanang-Wahyudi/DeployMOOC untuk konfigurasi Nginx dan SSL terutama untuk aplikasi Spring Boot
  
## 1. Hal yang di lakukan di lokal
- #### lakukan pembuatan image
  #### Perintah untuk membangun image berdasar dockerfile adalah :

  `docker build -t <nama_image> .`
  
- #### coba jalankan pada lokal
  #### Perintah untuk menjalankan container adalah :

  `docker run -dti -e <nama_var1>='<value_var1>' -e <nama_var2>='<value_var2>' -p <port-host>:<port-container> --name <nama_container> <nama_image>`
  - Cara membuat variable lingkungan dapat menambahkan sebanyak yang dibutuhkan dengan mengulang opsi -e.
  - Untuk penjelasan lainnya dapat diakses pada url berikut : 
    
  atau bisa juga menggunakan

  `docker run -dti --env-file </directory/nama_file> -p <port-host>:<port-container> --name <nama_container> <nama_image>`

- #### bila berjalan lancar maka push image ke dockerhub
  #### Perintah untuk login (pastikan sudah login untuk melakukan push ke repo) :
  
  `docker login` lalu masukan username dan password

  bila ingin login ke registry Docker lainnya, gunakan perintah :

  `docker login <registry_URL>`
`
  #### Perintah untuk membuat tag :
  
  `docker tag <nama_image> <username>/<nama_image> `

  #### Perintah untuk melakukan push ke dockerhub :
  
  `docker push <hasil_tag> `
  
- #### otomatis akan dibuatkan repository bila belum membuat (namun public), jangan lupa setting ke private bila perlu (bisa melalui dockerhub browser).
  
<br><br>
## 2. Hal yang di lakukan di server (Ubuntu OS)
- #### pastikan Nginx dan Certbot (SSL) sudah diinstall dan dikonfigurasikan sesuai url berikut : 
  - Bagian NGINX : https://github.com/Nanang-Wahyudi/DeployMOOC?tab=readme-ov-file#create-an-nginx-config-file\
  - Bagian SSL : https://github.com/Nanang-Wahyudi/DeployMOOC?tab=readme-ov-file#sslhttps
- #### lakukan pull dari dockerhub :
  #### lakukan login seperti sebelumnya di lokal
  `docker login`
  #### Lakukan pull repo
  `docker pull <username>/<nama_image>:<tag>`
  
  atau jika ingin menarik image dari registry Docker lainnya

  `docker pull <registry_URL>/<nama_image>:<tag>`

- #### Melakukan Running Container di Server :
  #### Lakukan pembuatan file env (bila env nya banyak)
  `vim /etc/env.list `

  lalu buat env.list, seperti :
   ```
   url_postgres=jdbc:uhuy://111.11.0.1:1111/uhuy
   username_db=uhuy 
   password_db=uhuy
   ...
  ```
   
  #### Jalankan perintah docker run (seperti sebelumnya di lokal)
  <b>Peringatan </b> : Disini belum melakukan setingan Docker Network sehingga akan default network bridge pada container yang berjalan

  `docker run -dti --env-file </directory/nama_file> -p <port-host>:<port-container> --name <nama_container> <nama_image>`

  #### Cek apakah container berjalan
  `docker ps`

  #### Cek log aplikasi/container
  `docker logs -f <nama_container>` atau `docker logs --tail 5 <nama_container>`

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

  
  

