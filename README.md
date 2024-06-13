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

   Menunggu hingga proses instalasi berakhir kemudian lanjut pada tahap konfigurasi bind

2. Konfigurasi

   Ada 2 file yang perlu dimodifikasi terlebih dahulu sebelum mulai mengkonfigurasi zona dan record. Anda harus menyesuaikan perubahan agar sesuai dengan kebutuhan. File-file tersebut adalah sebagai berikut:

   ```sudo vim /etc/bind/named.conf.local```

  ![Local](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/3b4717b4-db3d-47bf-b99b-6d29e6659c1a)

    ```sudo vim /etc/bind/named.conf.options```

  ![Option](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/d492b5a5-a9c7-4236-b18c-700fa0d88f86)


  kita perlu buat 2 file dengan nama yang sama persis pada directory /cte/bind/zones/. Sebelum server dapat menentukan nama apa pun, setiap file zona harus memiliki catatan yang ditambahkan untuk mewakili host yang ada di jaringan. Saya akan membahas setiap zona satu per satu dimulai dengan zona otoritatif db.null.fw (null.land). SANGAT penting untuk diingat untuk menambah Nomor Seri setiap kali Anda mengedit file zona apa pun atau memuat ulang zona serta transfer zona mungkin gagal.

  ```sudo vim /cte/bind/zones/db.null.fw```

  ![fw](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/7de52322-8ad4-415d-ad3f-75ad116e5b94)

  ```sudo vim /cte/bind/zones/db.null.rev```

  ![rev](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/e6f8b8c7-c0f8-480f-a9f1-a0b21fb11290)

  Ceheck konfigurasi dari file  menggunakan named-checkconf

  ```sudo named-checkconf /etc/bind/named.conf.options```
  
  ```sudo named-checkconf /etc/bind/named.conf.local```

  Jika menampilkan error maka hasus dicek kembali syntax yang terdapat pada keddua file tersebut

  Restart bind service

  pastikan ketika menjalakan perintah ini tidak menghasilkan error
  
  ```sudo systemctl restart bind9```

  Mengeck status bind9

  ```sudo systemctl restart bind9```

  berikut merupakan hasil yang ditampilkan

  ![Status bind](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/b7b5fe79-e9a2-4ddd-91c8-f2498c067e36)


  ## Hasil Pengujian

  ```dig -x 192.168.43.137```

  ![dig-x](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/0d00adcd-5726-451f-b85c-cc621bffbfda)


  ```dig www.null.land```

  ![dig-www](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/96b90b19-1ef0-48c6-877c-6ccafbbaf43a)


  ```dig NS null.land```

  ![dig-NS](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/cf29a909-42a4-4260-96e5-e5153e198e19)


  ```nslookup```

  ![nslookup](https://github.com/anwarmuhamma/Domain-Name-Server/assets/32747959/00e568b6-f203-4820-9e29-3ba3d97ba7b9)




  

  




