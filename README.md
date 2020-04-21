# Tugas 4 Big Data - Kafka Infrastruktur
Tugas 4 untuk melakukan Implementasi Kafka dengan menggunakan Docker dan pengujian menggunakan Conduktor.

## 1. Kebutuhan
- Docker for Windows
- Conduktor
- Bitnami/Kafka
- Bitnami/Zookeeper


## 2. Kafka With Docker Compose
Kali ini, saya akan menggunakan docker-compose dalam instalasi infrastruktur kafka.

### 2.1 Docker Compose Configuration
Berikut adalah konfigurasi ``yml`` pada ``docker-compose`` yang nanti akan dijalankan.

```
version: '2'

networks:
  kafka-net:
    driver: bridge

services:
  zookeeper-server:
    image: 'bitnami/zookeeper:latest'
    networks:
      - kafka-net
    ports:
      - '2181:2181'
    environment:
      - ALLOW_ANONYMOUS_LOGIN=yes
      
  kafka-server1:
    image: 'bitnami/kafka:latest'
    networks:
      - kafka-net    
    ports:
      - '9092:9092'
    environment:
      - KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper-server:2181
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092
      - ALLOW_PLAINTEXT_LISTENER=yes
    depends_on:
      - zookeeper-server
      
  kafka-server2:
    image: 'bitnami/kafka:latest'
    networks:
      - kafka-net    
    ports:
      - '9093:9092'
    environment:
      - KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper-server:2181
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9093
      - ALLOW_PLAINTEXT_LISTENER=yes
    depends_on:
      - zookeeper-server
      
  kafka-server3:
    image: 'bitnami/kafka:latest'
    networks:
      - kafka-net    
    ports:
      - '9094:9092'
    environment:
      - KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper-server:2181
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9094
      - ALLOW_PLAINTEXT_LISTENER=yes
    depends_on:
      - zookeeper-server
```

Pertama, saya membangun sebuah docker network, dimana networknya bernama kafka-net, dibangun dengan driver bridge

```
networks:
  kafka-net:
    driver: bridge
```

Lalu saya membuat Zookeeper server sebagai Cluster, dimana Zookeeper ini dibutuhkan untuk menjalankan kafka. Kali ini saya menggunakan zookeeper yang berasal dari bitnami, dengan port 2181.

```
  zookeeper-server:
    image: 'bitnami/zookeeper:latest'
    networks:
      - kafka-net
    ports:
      - '2181:2181'
    environment:
      - ALLOW_ANONYMOUS_LOGIN=yes
```

Selanjutnya adalah membangun 3 broker, pada kasus ini saya akan mengambil salah satu contoh broker, yaitu kafka-server1 akan diinstall pada container docker dengan menggunakan kafka(terbaru) dari bitnami, dengan port 9092 serta tcp port 9092.
``environtment`` berisi broker terhubung pada cluster zookeper-server yaitu zookeeper yang telah dibuat sebelumnya.

```
  kafka-server1:
    image: 'bitnami/kafka:latest'
    networks:
      - kafka-net    
    ports:
      - '9092:9092'
    environment:
      - KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper-server:2181
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092
      - ALLOW_PLAINTEXT_LISTENER=yes
    depends_on:
      - zookeeper-server
```

Untuk 2 broker lainnya konfigurasinya hampir sama, hanya saja menggunakan port yang berbeda.

### 2.2 Running

1. Jalankan docker, maka akan terlihat tidak ada container.

![](/screenshoot/2.png)

2. Masuk kedalam cmd.

3. Masuk kedalam direktori dimana docker-compose.yaml/yml berada.

4. Jalankan ``docker-compose up``. 

![](/screenshoot/3.png)

5. setelah itu buka kembali kedalam docker, maka container akan bertambah.

![](/screenshoot/4.png)

3 Broker dan 1 Cluster telah berhasil dibuat dan berhasil dijalankan.


### 2.3 Testing
Selanjutnya adalah melakukan testing dengan menggunakan conduktor, untuk memastikan kafka telah terbuat dan dapat digunakan.

![](/screenshoot/5.png)

Buat konfigurasi cluster, kemudian tekan save, setelah itu hubungkan dengan cluster yang telah dibuat.

![](/screenshoot/6.png)
         
