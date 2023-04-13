Sampai saat ini kita telah belajar beberapa komponen utama pada Docker seperti container, image, volume dan network.
Pada exercise ini kita akan mempelajari salah satu tool populer yang digunakan bersama docker yaitu Docker Compose

#### Apa Itu Docker Compose

Docker Compose adalah sebuah alat yang dikembangkan untuk membantu mendefinisikan dan membagikan aplikasi multi-container. Dengan Compose, kita dapat membuat sebuah file YAML untuk mendefinisikan network, container dan komponen lainnya dan mengelola semua komponen tersebut dengan satu perintah.

Misal, kita ingin membangun dan menjalankan aplikasi web dengan basis data MySQL dan server web Nginx. Tanpa Docker Compose, kita harus melakukan konfigurasi manual untuk setiap layanan atau komponen, seperti membuat container MySQL, mengatur network, membuat volume untuk data basis data, dan lain sebagainya. Setelah semua layanan dikonfigurasi, kita harus menjalankan setiap layanan secara manual dengan beberapa perintah Docker. Dengan Docker Compose kita cukup mendefinisikan file YAML dengan semua komponen tadi didalamnya

Berikut contoh file YAML untuk setup diatas

```{.yaml}
version: "3.9"
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    networks:
      - my-network
  db:
    image: mysql:latest
    environment:
      MYSQL_ROOT_PASSWORD: root
    networks:
      - my-network
    volumes:
      - database:/var/lib/mysql
networks:
  my-network:
volumes:
  database:
```

Dengan Compose, kita dapat memulai dan menghentikan semua komponen di atas (seperti container dan network) dengan mudah hanya dengan menggunakan perintah `docker-compose up` dan `docker-compose stop`, serta memanfaatkan fungsi lain seperti `logs`, `rm`, `top`, `ls` dan fungsi lainnya.
