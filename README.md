# Domain-Name-Server

Ubuntu Server 22.04 + bind9

Ketika hendak mengunjungi sebuah website maka wajib memasukan URL (Uniform Resource Locator) terlebih dahulu. alamat yang diketik merupakan hasil dari kerja DNS berupa alamat url, dengan begitu kita tidak peru negtik IP Address.
Mengkerucutkan pembasan terkait instalasi dan konfigurasi DNS Server kali ini, saya tidak jelaskan secara detail tentang URL.

Berikut merupakan struktur URL

![URL](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/9aa7aaa7-d24d-446b-9a67-f8a79f2fdb97)

Untuk lebih jelasnya mari kita bahas tentang DNS dan fungsinya.

Pengertian Domain Name System atau DNS server adalah sebuah sistem yang berfungsi untuk mengkonversi nama domain menjadi alamat IP. Server DNS ini bekerja sebagai jembatan antara nama domain dan alamat IP yang digunakan oleh komputer untuk menemukan lokasi situs web yang dituju.

Fungsi utama dari DNS Server adalah untuk memetakan nama domain ke alamat IP dan menyimpan informasi ini dalam cache untuk mempercepat pengaksesan situs web. Setiap kali komputer atau perangkat mobile mencoba mengakses situs web, permintaan akan dikirim ke DNS Server terdekat. Server ini akan mencari alamat IP dari situs yang diminta dan mengirimkan informasi kembali ke perangkat yang meminta.

Proses instalasi packet-packet yang dibutuhkan dalam mengimplementasi penerapan dns server.

1. Install bind9 dan dnsutils
   
   ```sudo apt install bind9 dnsutils```

   Menunggu hingga proses instalasi berakhir kemudian lanjut pada tahap konfig hostname sebelum lanjut konfig hostname

   ```hostnamectl set-hostname ns1.null.land #hostnamenya ns1```

   kemudian pada file config hosts di direktory ```sudo vim /etc/hosts``` seperti berikut
   
   ![hosts](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/19e07e4a-a5c2-491d-9337-d636d61e420d)

   lanjut ketahap berkutnya konfig resolve jika diperlukan ```sudo vim /etc/systemd/resolved.conf``` setelah itu restart service resolve ```sudo service systemd-resolved restart```
   \
   ![resolve](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/0d3f9af5-3eca-46ab-91bd-5f0a6ed7728c)
   \
3. Konfigurasi

   Ada 2 file yang perlu dimodifikasi terlebih dahulu sebelum mulai mengkonfigurasi zona dan record. Anda harus menyesuaikan perubahan agar sesuai dengan kebutuhan. File-file tersebut adalah sebagai berikut:

   ```sudo vim /etc/bind/named.conf.local```

  ![Local](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/3b4717b4-db3d-47bf-b99b-6d29e6659c1a)

    ```sudo vim /etc/bind/named.conf.options```

  ![option](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/76cb9c67-5462-438b-b42a-8c6f385bc657)

  kita perlu buat 2 file dengan nama yang sama persis pada directory /cte/bind/zones/. Sebelum server dapat menentukan nama apa pun, setiap file zona harus memiliki catatan yang ditambahkan untuk mewakili host yang ada di jaringan. Saya akan membahas setiap zona satu per satu dimulai dengan zona otoritatif db.null.fw (null.land). SANGAT penting untuk diingat untuk menambah Nomor Seri setiap kali Anda mengedit file zona apa pun atau memuat ulang zona serta transfer zona mungkin gagal.

  ```sudo vim /cte/bind/zones/db.null.fw```

  ![fw](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/85f8dc4a-c087-48e7-b4de-c78c49f9da9b)

  ```sudo vim /cte/bind/zones/db.null.rev```

  ![reverse](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/6f6d4676-3286-4716-9e46-3df734a5c650)

  Ceheck konfigurasi dan zones

  named-checkconfig
  
  ```sudo named-checkconf /etc/bind/named.conf.options```
  
  ```sudo named-checkconf /etc/bind/named.conf.local```

  named-checkzon

  ![checkzon](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/ea81d5b4-f1ba-4808-93ed-6221c4c631ee)

  Jika menampilkan error maka hasus dicek kembali syntax yang terdapat pada keddua file tersebut

  Restart bind9, named dan resolved service

  pastikan ketika menjalakan perintah ini tidak menghasilkan error
  
  ```sudo systemctl restart bind9; sudo systemctl restart named; sudo systemctl restart resolved```
  \
  cek status bind9, named dan resolved ```sudo systemctl status bind9; sudo systemctl status named; sudo systemctl status resolved```
  \
  ## Hasil Pengujian

  menggunakan Windown

  ```
   nslookup -query=any null.land
   nslookup -query=ns null.land
   nslookup -query=soa null.land
   nslookup -query=mx null.land
   nslookup -192.168.43.137 null.land
   nslookup -192.168.43.137 gmail.com
 ```

  ![Windows](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/83647fd9-44eb-41fc-912c-ee62418bba5e)

 Menggunakan Linux
 ```
   dig @127.0.0.1 ns1.null.land
   dig -x 192.168.43.137
   dig null.land @192.168.43.137 | grep -e "^host" -e ";; flags"
   dig +norec null.land @192.168.43.137 | grep -e "^host" -e ";; flags"
   dig +norec null.land @192.168.43.137 | sed -n -e '/;; flags/p' -e '/^;; AUTH/,/^$/p'
   dig +noall +answer SOA +multi null.land
 ```

 ![Linux](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/92431066-0617-4d42-ab11-992f218427f9)

