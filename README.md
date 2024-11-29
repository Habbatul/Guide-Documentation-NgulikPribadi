# 🖥️ Personal Ngulik
Berisikan dokumentasi guide hasil ngulik pribadi dengan bahasa Indonesia.

## Topik Guide Hasil Eksperiment :
- <a href="https://github.com/Nanang-Wahyudi/DeployMOOC">Deployment dengan Nginx dan Ubuntu (Studi Kasus : Spring Application) </a>
  - setting nginx reverse proxy
  - setting ssl dengan certbot
  - spring doc swagger agar bisa akses https untuk menghindari cors
 
- <a href="https://github.com/Habbatul/Personal-Deployment-Doc-Ubuntu/blob/main/Simple-Deployment-withDocker-NginxOutside.md">Deployment simple dengan docker (Nginx diluar container tanpa docker compose)</a>
  - build dan run image dengan env
  - push dan pull image ke dan dari dockerhub
  - spring doc swagger agar bisa akses https untuk menghindari cors
  - instalasi docker
  - cek log dan cek ip container untuk keperluan deploy tanpa conf network

- <a href="https://github.com/Habbatul/Guide-Documentation-NgulikPribadi/blob/main/Apache_dengan_Nginx.md">Server Apache dengan Nginx sebagai reverse proxy</a>
  - mengubah port apache
  - cek log apache dan port service
  - nginx reverse proxy ke apache
    
- <a href="https://github.com/Habbatul/Guide-Documentation-NgulikPribadi/blob/main/apache-with-php.md">Install PHP pada server Apache</a>
  - instalasi php (modul dasar) untuk apache
  - mengecek dan menambah ekstensi php
  - setting untuk membaca `.htaccess`
  - projek php dimana ada fitur mempengaruhi perubahan suatu file atau folder (chown)

- <a href="https://github.com/Habbatul/Personal-Deployment-Doc-Ubuntu/blob/main/postgresinDocker.md">PostgreSQL di docker</a>
  - instalasi postgres di docker
  - masuk bash container dan masuk psql
  - query dasar postgres dan mysql

- <a href="https://github.com/Habbatul/Guide-Documentation-NgulikPribadi/blob/main/install_mysql_didocker.md">install mysql di docker (termasuk mengatasi problem memenuhi ram pada vm kecil)</a>
  - instalasi mysql di docker
  - import file sql
  - cek bahwa user database ada dan bisa connect
  - mengatasi problem size mysql sangat besar di ram untuk vm kecil
 
- <a href="https://github.com/Habbatul/Personal-Deployment-Doc-Ubuntu/blob/main/DeploymentForThreeJS.md">Deployment Three JS (Vite & Github Actions)</a>
  - build aplikasi js dengan vite
  - plugin vite agar bisa rollup sehingga tidak perlu di folder public
  - config github action untuk otomasi deployment hasil build dengan gh-pages

- <a href="https://github.com/Habbatul/Guide-Documentation-NgulikPribadi/blob/main/nginx%20%26%20certbot%20dengan%20docker-compose.md">Deployment nginx & certbot dengan docker compose</a>
  - instalasi docker compose plugin
  - config docker-compose.yml
  - setting reverse proxy & certbot dengan docker compose

<br><br>
## Plan Selanjutnya :
- <a href="">Deployment Naive (Ubuntu tanpa Nginx)</a>
- <a href="">Deployment dengan docker compose dan Nginx didalam container</a>
- <a href="">Membuat artefak Java dengan maven</a>
- <a href="">Membuat artefak Go</a>
- <a href="">Deployment khusus untuk PHP</a>
- <a href="">Docker Network untuk simple deployment</a>
