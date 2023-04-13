Kita akan melakukan setup compose untuk menjalankan aplikasi web, database mysql dan phpmyadmin.

#### Clone Aplikasi

Pertama, kita akan men-clone aplikasi yang ada di github pada direktori `/home/admin`.

```{.bash .copy}
cd /home/admin && git clone https://github.com/cloudtutor-io/fastapi-app.git
```

Perintah di atas akan meng-clone repository fastapi-app ke local.

> Update `editor` disebelah kanan dengan meng-klik icon refresh untuk memunculkan direktori aplikasi

#### Menyiapkan Dockerfile

Pastikan posisi direktori sama dengan aplikasi yang sudah di clone.

```{.bash .copy}
cd fastapi-app
```

Kedua, buat file Dockerfile didalam direktori aplikasi.
gunakan perintah dibawah atau gunakan editor yang berada diatas terminal

```{.bash .copy}
touch Dockerfile
```

Lalu ubah isi dari file `Dockerfile` menjadi seperti setup dibawah

```{.bash .copy}
FROM python:3.10-alpine

# set a directory for the app
WORKDIR /app

# copy all the files to the container
COPY . .

# install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# define the port number the container should expose
EXPOSE 8080

# run the command
CMD ["python", "./main.py"]
```

Kita sudah men-dockerize aplikasi `fastapi-app`, selanjutnya adalah menyiapkan file compose.yml

#### Menyiapkan compose.yml

Buat file compose.yml di dalam direktori aplikasi dengan menggunakan perintah

```{.bash .copy}
touch compose.yml
```

atau gunakan editor disebelah kanan.

Selanjutnya tentukan versi definisi compose yang digunakan. Tambahkan versi seperti pada contoh dibawah

```{.yml .copy}
version: '3'
```

Versi diatas digunakan menentukan format penulisan dari compose file.

Selanjutnya tambahkan network & volumes yang ingin digunakan. Tambahkan seperti pada contoh dibawah

```{.yml .copy}
volumes:
  db-data:
networks:
  db-net:
```

Setelah itu kita bisa memulai menambahkan konfigurasi untuk container. Kita mulai dari mysql & phpmyadmin.

Tambahkan `services` untuk tempat konfigurasi container dan tambahkan service `db` dan `phpmyadmin` seperti pada contoh dibawah

```{.yml .copy}
services:
  db:
    image: mysql
    environment:
      MYSQL_DATABASE: example
      MYSQL_USER: user
      MYSQL_PASSWORD: password
      MYSQL_ROOT_PASSWORD: rootpassword
    volumes:
      - db-data:/var/lib/mysql
    networks:
      - db-net
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      PMA_HOST: db
      PMA_PORT: 3306
    ports:
      - 8080:80
    depends_on:
      - db
    networks:
      - db-net
```

Konfigurasi diatas didapatkan dari dokumentasi mysql dan phpmyadmin pada https://hub.docker.com/_/mysql dan https://hub.docker.com/r/phpmyadmin/phpmyadmin/

Perhatikan penulisan `volumes` dan `networks` yang menggunakan list. Hal ini menandakan bahwa kedua komponen tersebut dapat memiliki lebih dari satu objek untuk `networks` ataupun `volumes`.

contoh penulisan object volume lebih dari satu

```{.yml .copy}
    volumes:
      - db-data:/var/lib/mysql
      - app-config:/app/config
```

Kita sudah menambahkan konfigurasi untuk mysql dan phpmyadmin, selanjutnya kita perlu menambahkan konfigurasi untuk aplikasi yang sudah di dockerize sebelumnya.

Tambahkan konfigurasi compose untuk aplikasi seperti dibawah, tempatkan sejajar dengan `db` dan `phpmyadmin`

```{.yml .copy}
  app:
    image: fastapi-app
    build: .
    ports:
      - 80:8080
    depends_on:
      - db
    networks:
      - db-net
```

Hasil akhir dari file `compose.yml` adalah seperti berikut

```{.yml .copy}
version: '3'
volumes:
  db-data:
networks:
  db-net:
services:
  db:
    image: mysql
    environment:
      MYSQL_DATABASE: example
      MYSQL_USER: user
      MYSQL_PASSWORD: password
      MYSQL_ROOT_PASSWORD: rootpassword
    volumes:
      - db-data:/var/lib/mysql
    networks:
      - db-net
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      PMA_HOST: db
      PMA_PORT: 3306
    ports:
      - 8080:80
    depends_on:
      - db
    networks:
      - db-net
  app:
    image: fastapi-app
    build: .
    ports:
      - 80:8080
    depends_on:
      - db
    networks:
      - db-net
```

#### Mengunakan Compose

Kita sudah berhasil menyiapkan dockerfile dan compose file, selanjutnya kita akan mencoba menjalankannya menggunakan perintah compose

Pertama lakukan build image menggunakan perintah compose build seperti dibawah

```{.bash .copy}
docker compose build
```

Setelah build berhasil, jalankan aplikasi dengan perintah

```{.bash .copy}
docker compose up -d
```

Container akan berjalan dengan mode --detach atau berjalan dibackground.

Kita bisa mengecek aplikasi yang sudah berjalan dengan mengecek `localhost:80` menggunakan perintah

```{.sh .copy}
curl localhost:80
```

atau membuka di url

```{.sh .copy}
https://#ID#.host.cloudtutor.io
```

Logs container bisa dicek menggunakan perintah `docker compose logs`

Berhasil, kita sudah menjalankan aplikasi menggunakan Docker Compose
