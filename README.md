# Pengenalan Docker Compose
![DockerCompose](https://user-images.githubusercontent.com/32213421/97078971-80c09e80-161a-11eb-8427-a8596a5a68c6.JPG)

**Docker compose** merupakan tool dari docker untuk membuat dan menjalankan multi container, docker compose menggunakan konfigurasi file **YAML** dan untuk menjalankannya hanya cukup melalui satu perintah.

Jika sebelumnya pembuatan `container`, `network`, `volume` dll dilakukan secara manual satu per satu seperti:
```
$ docker run -d –name myWeb -p 8080:80 nginx:latest
```
```
$ docker network create myNetwork
```
```
$ docker volume create myVolume
```
Hasilnya akan sangat **tidak efektif** jika yang dibuat sangat banyak nah sebagai solusi digunakanlah docker compose, dengan docker compose semua itu bisa dibuat hanya dengan satu file yaitu `docker-compose.yml` dan dijalankan hanya dengan satu perintah maka semua konfigurasi yang terdapat pada file tersebut secara otomatis akan dijalankan. Jika ingin mengetahui lebih lanjut mengenai docker compose kalian bisa mengunjungi dokumentasinya [di sini](https://docs.docker.com/compose/).

Untuk mengInstall docker compose di OS Mac, Windows & Linux kalian bisa mengikuti instruksi yang ada [di sini](https://docs.docker.com/compose/install/).
## Struktur Format File Docker Compose
Secara sederhana format file konfigurasi `docker-compose.yml`, yaitu sebagai berikut:

```yaml
version_file_format:

services:
  service_name:
    property: value
      - or options

volumes: <optional>
  volume_name:
    property: value
      - or options

networks: <optional>
  network_name:
    property: value
      - or options
```
Jika ingin mengetahui lebih lanjut mengenai konfigurasi file **YAML** kalian bisa mengunjungi dokumentasinya [di sini](https://docs.docker.com/compose/compose-file/).

## Perintah Dasar Docker Compose
Docker compose bekerja dalam **group** sehingga semua konfigurasi yang terdapat pada file `docker-compose.yml` akan dianggap sebagai satu group. Berikut beberapa perintah dasar docker compose yang perlu kalian ketahui.

Untuk build dan menjalankan semua service
```
$ docker-compose up 
```
Untuk memberhentikan dan menghapus semua service
```
$ docker-compose down
```
Untuk memberhentikan semua service
```
$ docker-compose stop
```
Untuk menghapus semua service
```
$ docker-compose rm
```
Untuk merestart semua service
```
$ docker-compose restart
```
Untuk melihat logs semua service
```
$ docker-compose logs
```
Untuk melihat list service
```
$ docker-compose ps
```
## Contoh Skenario Docker Compose
![SkenarioDockerCompose](https://user-images.githubusercontent.com/32213421/97078974-85855280-161a-11eb-8293-217103ccb360.JPG)

Pada skenario ini kita akan coba menghubungkan **container phpmyadmin** sebagai management database dengan **container mysql** sebagai database nya. Pada dasarnya setiap container pada docker melakukan **isolasi** sehingga tidak dapat berkomunikasi satu dengan yang lain, untuk dapat berkomunikasi antar container maka digunakan `network` atau `link` sebagai penghubung. Sesuai skenario maka container phymyadmin dengan mysql akan berada pada `network` yang sama sehingga mysql akan bisa di akses menggunakan phpmyadmin.

Komunikasi dari container phpmyadmin ke container mysql dilakukan melalui port `3306` sedangkan komunikasi antara container phpmyadmin dengan docker host dilakukan malalui mapping port dari port host `8080` menuju ke port container phpmyadmin `80`.

## Membuat File Docker Compose
Buat file baru dengan nama `docker-compose.yml`, lalu isikan dengan konfigurasi berikut:
```yaml
version: '3.8'
services: 
    db:
        image: mysql:5.7
        container_name: mysql
        volumes: 
            - db-data:/var/lib/mysql
        environment: 
            MYSQL_DATABASE: app_db
            MYSQL_USER: jokopwt
            MYSQL_PASSWORD: user
            MYSQL_ROOT_PASSWORD: root
        restart: always
        networks:
           - int-network
    phpmyadmin:
        image: phpmyadmin/phpmyadmin
        container_name: phpmyadmin
        ports: 
            - '8080:80'
        restart: always
        depends_on: 
            - db
        networks: 
            - int-network
            
volumes: 
    db-data:
    
networks: 
    int-network:
```
![docker-compose.yml](https://user-images.githubusercontent.com/32213421/97078978-8e762400-161a-11eb-8aaa-e7a4bb107d7e.PNG)

Berikut penjelasan mengenai konfigurasi di atas
* **Baris 1** untuk mendefinisikan docker compose file version.
* **Baris 2** untuk mendefinisikan service yang digunakan.
* **Baris 3** untuk mendefinisikan nama service yaitu db.
* **Baris 4** untuk mendefinisikan base image yang digunakan oleh service db.
* **Baris 5** untuk mendefinisikan nama container dari service db.
* **Baris 6 - 7** untuk mendefinisikan mount volume dari host ke container, sehingga data pada container `/var/lib/mysql` akan tersimpan secara persistent.
* **Baris 8 - 12** untuk mendefinisikan `environment variable` ke dalam container atau service db.
* **Baris 13** untuk mendefinisikan restart policies dengan flag always yang artinya container atau service akan melakukan restart otomatis jika terjadi error.
* **Baris 14 - 15** untuk mendefinisikan network yang digunakan oleh service db.
* **Baris 17** untuk mendefinisikan nama service yaitu phpmyadmin.
* **Baris 18** untuk mendefinisikan base image yang digunakan oleh service phpmyadmin.
* **Baris 19** untuk mendefinisikan nama container dari service phpmyadmin.
* **Baris 20 - 21** untuk mendefinisikan port mapping dari port host `8080` ke port container `80`.
* **Baris 22** untuk mendefinisikan `restart policies` dengan flag always.
* **Baris 23 - 24** untuk mendefinisikan bahwa service phymyadmin bergantung kepada service db yang artinya service db dijalankan sebelum service phpmyadmin.
* **Baris 25 - 26** untuk mendefinisikan network yang digunakan oleh service phpmyadmin.
* **Baris 28 - 29** untuk mendefinisikan dan membuat volume yang digunakan oleh service.
* **Baris 31 - 32** untuk mendefinisikan dan membuat network yang digunakan oleh service.

## Menjalankan Docker Compose
Untuk menjalankan konfigurasi file `docker-compose.yml` maka cukup jalankan perintah berikut:
```shell
$ docker-compose up -d
```
![docker-compose-up-d](https://user-images.githubusercontent.com/32213421/97078984-959d3200-161a-11eb-81c3-3a7461c391b5.PNG)
*	**-d** digunakan untuk menjalankan container di mode detached (background).

Ditampilkan keterangan dari pembuatan `network`, `volume`, `service db` dan `phpmyadmin` bahwa statusnya berhasil.

Jika kalian ingin menjalankan sekaligus melihat log proses dari konfigurasi docker compose cukup hilangkan **-d** sehingga perintah nya menjadi:
```
$ docker-compose up
```
![docker-compose-up](https://user-images.githubusercontent.com/32213421/97078987-9635c880-161a-11eb-905b-b11cc50d3c4e.PNG)

Pastikan semua service sudah aktif, untuk mengechecknya gunakan perintah berikut:
```
$ docker-compose ps
```
![docker-compose-ps](https://user-images.githubusercontent.com/32213421/97078988-96ce5f00-161a-11eb-9f2c-d3d84153475c.PNG)

Ada dua service yang aktif, sesuai dengan konfigurasi file `docker-compose.yml`, satu untuk service **mysql** dan satu lagi untuk service **phpmyadmin**.
## Test phpMyAdmin pada Docker Host
Kemudian test akses service phpmyadmin melalui web browser dengan URL http://localhost:8080, maka akan ditampilkan halaman login phpmyadmin. Untuk login dapat menggunakan akun `root` atau `user` sesuai dengan konfigurasi `environment variable` pada service db.

![phpmyadmin-login](https://user-images.githubusercontent.com/32213421/97078989-9766f580-161a-11eb-9e16-98e056274ad5.PNG)

Pada halaman dashboard phpmyadmin ditampilkan nama database `app_db` sesuai dengan konfigurasi `environment variable` pada service db. Sampai di sini kita telah berhasil menggunakan docker compose untuk membuat multi container yang saling terhubung.

![phpmyadmin-dashboard](https://user-images.githubusercontent.com/32213421/97078990-9766f580-161a-11eb-9492-bfc5b1df2984.PNG)