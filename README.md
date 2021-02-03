# Install Elasticsearch, Logstash dan Kibana

- Menambahkan repository packages Elasticsearch untuk di install dan menginstall JDK 11 dengan beberapa perintah berikut pada server pod-elk dan pod-client:
  - `wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -`
  - `echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list`
  - `sudo apt update -y`
  - `sudo apt install elasticsearch -y`
  - `sudo apt install apt-transport-https openjdk-11-jdk`
  - `java --version`
  
![Screen Shot 2021-01-29 at 20 03 35](https://user-images.githubusercontent.com/45087061/106770140-3e264d80-6670-11eb-8310-b3599569a10b.png)
![Screen Shot 2021-01-29 at 20 04 24](https://user-images.githubusercontent.com/45087061/106770246-5ac28580-6670-11eb-9c57-008aad5b9ce7.png)
![Screen Shot 2021-01-29 at 20 05 39](https://user-images.githubusercontent.com/45087061/106770320-729a0980-6670-11eb-8017-c27711053ef6.png)
![Screen Shot 2021-01-29 at 20 10 17](https://user-images.githubusercontent.com/45087061/106770440-8e051480-6670-11eb-979c-1f81e5e140c5.png)
![Screen Shot 2021-01-29 at 20 07 49](https://user-images.githubusercontent.com/45087061/106770482-9c533080-6670-11eb-87fc-abadc78d056a.png)
![Screen Shot 2021-01-29 at 20 08 25](https://user-images.githubusercontent.com/45087061/106770525-a7a65c00-6670-11eb-8ef9-fa21d283c35e.png)

- Sesuaikan konfigurasi berikut di server pod-elk dan pod-client pada file `nano /etc/elasticsearch/elasticsearch.yml`
![Screen Shot 2021-01-29 at 22 29 43](https://user-images.githubusercontent.com/45087061/106771185-48951700-6671-11eb-9b9f-88ca61291b1a.png)
![Screen Shot 2021-01-29 at 22 34 50](https://user-images.githubusercontent.com/45087061/106771202-4c289e00-6671-11eb-8046-9cd2c4f84d39.png)

### Noted:
### node.name: elk-node-1 (untuk memberi masing-masing node atau server)
### network.host: 10.10.2.10 (di isi dengan ip private dari setiap node)
### http.port: 9200 (port default untuk mmbuka elasticsearchnya)
### discovery.seed.hosts: ["10.10.2.10", "10.10.2.20"] (ip dari setiap node yang ingin tergabung pada elasticsearch)
### cluster.initial_master_nodes: ["10.10.2.10"] (untuk menentukan node sebagai masternya dengan ip nodenya)

- Kemudian enable dan start service elasticsearchnya, restart juga service daemonnya lalu cek status elasticsearchnya dengan perintah berikut:
  - sudo su
  - systemctl daemon-reload
  - systemctl enable elasticsearch
  - systemctl start elasticsearch
  - systemctl status elasticsearch
![Screen Shot 2021-01-29 at 22 40 12](https://user-images.githubusercontent.com/45087061/106773081-46cc5300-6673-11eb-9f49-b4d52b7f05b7.png)

- Pastikan elasticsearch bisa diakses dengan perintah `curl ip-server:9200
![Screen Shot 2021-01-29 at 22 42 51](https://user-images.githubusercontent.com/45087061/106773323-85faa400-6673-11eb-9e76-5de76a83f950.png)

### Karna bersifat private tidak bisa diakses langsung menggunakan IP Public, untuk mengaksesnya lewat browser host atau local bisa menggunakan ssh tunnel/port forwading sebagai berikut
- Menggunakan ssh tunnel pada terminal dengan perintah `ssh -D 9090 student@138.201.60.4 -p 10210` -D adalah Dynamic port proxy private
![Screen Shot 2021-02-01 at 14 18 09](https://user-images.githubusercontent.com/45087061/106774083-57c99400-6674-11eb-842c-6492e47ad78d.png)

- Kemudian pada settingan jaringan Browser hostnya juga seperti ini
![Screen Shot 2021-02-01 at 14 17 48](https://user-images.githubusercontent.com/45087061/106774177-6f088180-6674-11eb-9bc2-ffba95bd85e1.png)
![Screen Shot 2021-02-01 at 14 18 36](https://user-images.githubusercontent.com/45087061/106774398-a8d98800-6674-11eb-8f51-e8874b4e5832.png)

## Install Kibana

- Install Kibana pada server pod-elk (master) dengan perintah berikut:
  - wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
  - sudo apt-get install apt-transport-https
  - echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
  - sudo apt-get update && sudo apt-get install kibana
![Screen Shot 2021-02-01 at 14 45 58](https://user-images.githubusercontent.com/45087061/106774681-f524c800-6674-11eb-9f90-b4dfb7cbdf7f.png)

- Ubah konfigurasi kibana seperti ini dengan `sudo su` lalu `nano /etc/kibana/kibana.yml`
![Screen Shot 2021-02-01 at 14 49 51](https://user-images.githubusercontent.com/45087061/106775029-52b91480-6675-11eb-953d-08d4bc6891d9.png)
`server.port: 5601
server.host: “0.0.0.0”
server.name: “cist-elk”
elasticsearch.hosts: [“https://elk-node-1:9200”]
kibana.index: “.kibana”`

- Kemudian restart service Kibana dan cek statusnya
`systemctl restart kibana
systemctl status kibana`
![Screen Shot 2021-02-01 at 14 50 32](https://user-images.githubusercontent.com/45087061/106775865-1a660600-6676-11eb-8f7b-67b52b92a5e0.png)

- Akses lagi menggunakan ssh tunnel untuk Kibana di browser host lalu ketik dengan `ip-master:5601`
![Screen Shot 2021-02-01 at 14 55 03](https://user-images.githubusercontent.com/45087061/106775940-2ce03f80-6676-11eb-8c6f-5c74ed1cb1ba.png)

## Install Metricbeat untuk monitoring node klien

- Install metricbeat pada server pod-client `sudo apt install metricbeat -y`
![Screen Shot 2021-02-01 at 16 34 24](https://user-images.githubusercontent.com/45087061/106776318-8a748c00-6676-11eb-8e3b-45eaa6aa2148.png)

- Ubah konfigurasi metricbeat pada `/etc/metricbeat/metricbeat.yml` sebagai berikut
![Screen Shot 2021-02-01 at 16 36 31](https://user-images.githubusercontent.com/45087061/106776673-e0e1ca80-6676-11eb-9517-a3ce4f75f206.png)
![Screen Shot 2021-02-01 at 16 36 55](https://user-images.githubusercontent.com/45087061/106776680-e3442480-6676-11eb-9a52-a89e0cbba443.png)
- Ubah konfigurasi module pada `/etc/metricbeat/modules.d/elasticsearch-xpack.yml`
![Screen Shot 2021-02-01 at 16 41 59](https://user-images.githubusercontent.com/45087061/106776689-e4755180-6676-11eb-8b9c-fc28a5288dbf.png)

- Tambahkan sintaks berikut pada server pod-elk dan pod-client di `/etc/elasticsearch/elasticsearch.yml` untuk mengaktifkan plugin monitoring
![Screen Shot 2021-02-01 at 19 06 29](https://user-images.githubusercontent.com/45087061/106776695-e50de800-6676-11eb-8ed4-5a742bc71dbe.png)
![Screen Shot 2021-02-01 at 19 07 04](https://user-images.githubusercontent.com/45087061/106776698-e63f1500-6676-11eb-8b8a-1845d9cb081c.png)

- Kemudian akses web elasticsearchnya dan cek pada bagian Stack Monitoring
![Screen Shot 2021-02-01 at 19 08 19](https://user-images.githubusercontent.com/45087061/106778239-54d0a280-6678-11eb-8bd7-597dc599b662.png)

## Visualisasi penggunaan resource pod-client
- Pada dashboard web kibana buka bagian `Visualize` lalu klik `Create visualization` dan pilih saja `Gauge`
![Screen Shot 2021-02-01 at 19 14 50](https://user-images.githubusercontent.com/45087061/106778537-aa0cb400-6678-11eb-9aca-74937f78a913.png)

- Dan buat seperti ini untuk monitoring CPU, Memory, Disk dan Network
![Screen Shot 2021-02-03 at 21 07 30](https://user-images.githubusercontent.com/45087061/106778817-f22bd680-6678-11eb-8747-abd365f19af9.png)

## Install Logstash pada node master untuk Visualisasi Parsing Log

- Install Logstash pada server pod-elk dengan perintah berikut `sudo apt install logstash`
![Screen Shot 2021-02-01 at 15 06 20](https://user-images.githubusercontent.com/45087061/106779234-623a5c80-6679-11eb-9133-7849cc9d850a.png)

- Download juga hasil parsing lognya dengan `wget`
![Screen Shot 2021-02-03 at 23 44 58](https://user-images.githubusercontent.com/45087061/106779713-d8d75a00-6679-11eb-9181-5d261ff0763b.png)

- Buat file konfigurasi logstashnya ddengan bebas di `/etc/logstash/`. Disini saya memberi nama `logstash.conf` dan sesuaikan seperti ini
![Screen Shot 2021-02-03 at 23 48 55](https://user-images.githubusercontent.com/45087061/106780266-65821800-667a-11eb-8e86-e1f58564f1e6.png)

- Lalu jalankan dengan perintah `sudo -u logstash /usr/share/logstash/bin/logstash -f logstash.conf` ketika berada pada directory `/etc/logstash` dan ketika berhasil akan muncul log seperti ini yang berarti hasilnya sudah berhasil dipindahkan ke elasticsearch
![Screen Shot 2021-02-03 at 20 12 37](https://user-images.githubusercontent.com/45087061/106780466-9cf0c480-667a-11eb-961f-a9da3fbc3903.png)

- Cek pada web kibana pada bagian `Index Patterns`. Akan muncul nama index log yang sudah dibuat pada konfigurasi file `logstash.conf`
![Screen Shot 2021-02-03 at 20 16 04](https://user-images.githubusercontent.com/45087061/106780705-d75a6180-667a-11eb-8697-f1f1ea3ed91a.png)

- Kemudan copy dan pastekan nama indexnya pada `Index pattern name` lalu klik `Next step`
![Screen Shot 2021-02-03 at 20 17 53](https://user-images.githubusercontent.com/45087061/106780987-22747480-667b-11eb-8bfc-f27a11ad318d.png)

- Ubah Time field dengan `@timestamp` kemudian klik `Create index pattern`
![Screen Shot 2021-02-03 at 20 18 07](https://user-images.githubusercontent.com/45087061/106781171-594a8a80-667b-11eb-817d-a66f224445e0.png)

- Setelah berhasil akan seperti ini dan bisa dicek pada bagian `Discover`
![Screen Shot 2021-02-03 at 20 20 31](https://user-images.githubusercontent.com/45087061/106781425-a2024380-667b-11eb-8dd5-e129837ec545.png)
![Screen Shot 2021-02-03 at 20 35 13](https://user-images.githubusercontent.com/45087061/106781439-a595ca80-667b-11eb-85f7-db3c5976cea8.png)
![Screen Shot 2021-02-03 at 20 36 23](https://user-images.githubusercontent.com/45087061/106781445-a6c6f780-667b-11eb-80da-d1e91df35e11.png)
![Screen Shot 2021-02-03 at 20 39 32](https://user-images.githubusercontent.com/45087061/106781450-a75f8e00-667b-11eb-8386-0723af652571.png)

- Kemudian `Save` dan beri nama
![Screen Shot 2021-02-03 at 20 40 33](https://user-images.githubusercontent.com/45087061/106781576-c9f1a700-667b-11eb-8948-418b0636667e.png)

## Membuat Visualisasi dan Dashboard untuk hasil parsing log

- `Create visualization` pada bagian Visualize dan pilih `Area`
![Screen Shot 2021-02-03 at 20 43 26](https://user-images.githubusercontent.com/45087061/106781761-fc030900-667b-11eb-8a5c-b7c10f72442b.png)

- Pilih nama dari hasil save sebelumnya
![Screen Shot 2021-02-03 at 20 43 35](https://user-images.githubusercontent.com/45087061/106781779-03c2ad80-667c-11eb-93a5-c0a53812617f.png)

- Buat visualisasi dengan Buckets seperti ini
![Screen Shot 2021-02-03 at 20 48 37](https://user-images.githubusercontent.com/45087061/106781926-2ead0180-667c-11eb-9cfb-67be06dd5646.png)

- Buat Visualize satu lagi dan pilih `Metric` kemudian atur Buckets nya seperti ini
![Screen Shot 2021-02-03 at 20 52 51](https://user-images.githubusercontent.com/45087061/106781995-44222b80-667c-11eb-838d-8e911001ee5b.png)

- Kemudian pindah ke bagian Dashboard lalu klik `Add an existing` dan pilih dari hasil Visualize yang sudah dibuat
![Screen Shot 2021-02-03 at 20 55 01](https://user-images.githubusercontent.com/45087061/106782483-c9a5db80-667c-11eb-9fd1-2ce47b63b018.png)

- Dan hasilnya akan seperti ini
![Screen Shot 2021-02-03 at 20 56 29](https://user-images.githubusercontent.com/45087061/106782494-cd396280-667c-11eb-8b66-03e43160f827.png)

