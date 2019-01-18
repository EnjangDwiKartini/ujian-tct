# DOCKER FILE 
===
Dockerfile merupaka script yang berisi atau terdiri dari serangkaian perintah, intruksi (argumen) yang akan dieksekusi secara otomatisasi dan berurutan untuk membangun sebuah image. Docker images adalah sebuah template yang bersifat read only. Template ini sebenarnya adalah sebuah OS atau OS yang telah diinstall berbagai aplikasi. Docker images berfungsi untuk membuat docker container, dengan hanya 1 docker images kita dapat membuat banyak docker container. Jadi pada intinya Docker dapat membuat image secara otomatis dengan membaca instruksi dari Dockerfile. 

## Penggunaan Docker File 

Perintah ~~~docker buid~~~  akan membangun image dari Docker File dan konteks. Yang dimaksud konteks disini adalah kumpulan file di PATH atau URL lokasi yang ditentukan. PATH adalah direktori pada sistem file lokal sedangkan URL adalah lokasi repositori Git.
Konteks diproses secara rekursif. Jadi, PATH menyertakan subdirektori dan URL menyertakan repositori dan submodulnya. Contoh ini menunjukkan perintah build yang menggunakan direktori saat ini sebagai konteks:
~~~
$ docker build .
Sending build context to Docker daemon  6.51 MB
...
~~~

Build dijalankan oleh daemon Docker. Hal pertama yang dilakukan proses build adalah mengirimkan seluruh konteks (secara rekursif) ke daemon. Docker daemon berfungsi untuk membangun, mendistribusikan dan menjalankan container docker. User tidak dapat langsung menggunakan docker daemon, akan tetapi untuk menggunakan docker daemon maka user menggunakan docker client sebagai perantara atau cli. Sesuai dengan dokumentasi yang saya baca sebaiknya mulai dengan direktori kosong sebagai konteks dan simpan Dockerfile kita di direktori itu. Tambahkan hanya file yang diperlukan untuk membangun Dockerfile.
Untuk menggunakan file dalam konteks build, Dockerfile merujuk ke file yang ditentukan dalam instruksi, misalnya, instruksi COPY. Untuk meningkatkan kinerja pembangunan, untuk pengecualian  file dan direktori dapat dengan menambahkan file .dockerignore ke direktori konteks.
Dockerfile terletak di akar konteksnya, pada dokumentasi ini menggunakan flag -f dengan docker build  untuk menunjuk ke Dockerfile di sistem file kita

~~~
$ docker build -f /path/to/a/Dockerfile .
~~~ 

Kita dapat menentukan repositori dan tag tempat menyimpan image baru jika build berhasil:

~~~
$ docker build -t shykes/myapp .
~~~

Untuk memberi tag pada image ke dalam beberapa repositori setelah build, tambahkan beberapa parameter -t  saat menjalankan perintah build:

~~~
$ docker build -t shykes/myapp:1.0.2 -t shykes/myapp:latest .
~~~

Sebelum daemon Docker menjalankan instruksi di Dockerfile, ia melakukan validasi awal terhadap Dockerfile dan mengembalikan kesalahan jika sintaksnya salah seperti berikut :

~~~
$ docker build -t test/myapp .
Sending build context to Docker daemon 2.048 kB
Error response from daemon: Unknown instruction: RUNCMD
~~~

Docker daemon menjalankan instruksi di Dockerfile satu-per-satu, menghasilkan output dari setiap instruksi image baru , sebelum akhirnya mengeluarkan image ID baru. Daemon Docker akan secara otomatis membersihkan konteks yang dikirim.
Setiap instruksi dijalankan secara independen, dan menyebabkan image baru dibuat sehingga ~~~ RUN cd / tmp~~~  tidak akan berpengaruh pada instruksi selanjutnya.
Docker akan menggunakan kembali  perantara image  (cache), untuk mempercepat proses docker build secara signifikan. Ini ditunjukkan oleh pesan Menggunakan cache di output konsol. 

~~~
$ docker build -t svendowideit/ambassador .
Sending build context to Docker daemon 15.36 kB
Step 1/4 : FROM alpine:3.2
 ---> 31f630c65071
Step 2/4 : MAINTAINER SvenDowideit@home.org.au
 ---> Using cache
 ---> 2a1c91448f5f
Step 3/4 : RUN apk update &&      apk add socat &&        rm -r /var/cache/
 ---> Using cache
 ---> 21ed6e7fbb73
Step 4/4 : CMD env | grep _TCP= | (sed 's/.*_PORT_\([0-9]*\)_TCP=tcp:\/\/\(.*\):\(.*\)/socat -t 100000000 TCP4-LISTEN:\1,fork,reuseaddr TCP4:\2:\3 \&/' && echo wait) | sh
 ---> Using cache
 ---> 7ea8aef582cc
Successfully built 7ea8aef582cc
~~~

Cache build hanya digunakan dari image yang memiliki rantai induk lokal. Ini berarti bahwa image - image ini dibuat oleh perintah build sebelumnya atau seluruh rantai image dimuat dengan perintah docker load. 