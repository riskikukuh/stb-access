# Access STB

Menjelaskan apa saja yang digunakan untuk mengakses STB ( Home Server )

## Kendala

- Tidak memiliki akses LAN
- Tidak memiliki monitor untuk melihat console



## Solusi

Mengkoneksikan STB ( Home server ) dengan WiFi. Namun untuk mengkoneksikan perlu menjalankan command line, solusinya ada 2 cara

- Menggunakan adb ( Android Debug Bridge )
  - Syaratnya STB sudah memiliki adb server dan sudah menjalankan adb server
  - Kemudian Melakukan reverse port dari 22 ( pada STB ) ke Android device port 2222
    - Masih terkendala pada akses reverse port yang perlu connected device ketika booting ( sudah ditanamkan auto reverse pada systemd )
- Menggunaakn USB Tethering dari Smartphone ( **RECOMMENDED** )
  - Perlu scan IP untuk mendapatkan IP dari STB



## How to Scan IP Address with Termux

- Install aplikasi Termux dari PlayStore

- Install `nmap`

  - Jalankan command instalasinya

    ```bash
    pkg install nmap -y
    ```

- Cek IP dengan interface USB ( yang ditethering )

  ```bash
  ifconfig # List semua network interface pada Android
  ```

  - Cari interface `USB`, dan kemudian catat IPnya. Yang kita butuhkan hanya IP Broadcastnya dengan cara mengganti octet terakhir menjadi `0`. Misal IP yang didapatkan adalah `192.168.252.122`, maka IP Broadcastnya adalah **`192.168.252.0`** dan ditambahkan subnet masknya. Karena IP nya adalah tipe C maka subnet masknya adalah `255` atau `24` kalau dalam notasi CIDR

- Kemudian kita scan semua user yang terhubung dengan IP range `192.168.252.1 - 192.168.252.255` menggunakan tool nmap, dengan menjalankan command

  ```bash
  nmap -sn 192.168.252.0/24
  ```

  - Hasil dari nmap adalah IP apa saja yang mengembalikan response. Seharusnya ada 2 IP yang mengembalikan response, yaitu IP Android device itu sendiri dan IP dari STB. Catat IP tersebut, IP tersebutlah yang digunakan untuk akses SSH ke STB untuk mengkoneksikan ke WiFi.



## How to Connect WiFi

- List WiFi yang tersedia

  ```bash
  nmcli d wifi list
  ```

  ![Output list WiFi](https://github.com/riskikukuh/stb-access/blob/master/images/image-20241205151014402.png?raw=true)

- Disconnect WiFi ( jika sudah pernah terhubung )

  ```bash
  nmcli d disconnect iface <WifiInterface> # wlan0
  ```

- Connect WiFi 

  ```bash
  nmcli d wifi connect <WiFiSSID> password <WiFiPassword> iface <WifiInterface>
  ```

- Cek apakah WiFi berhasil terhubung dengan command `List WiFi` lalu lihat apakah ada tanda bintang ( * ) pada kolom IN-USE. Artinya WiFi dengan SSID tersebut sudah terhubung

  ![Result](https://github.com/riskikukuh/stb-access/blob/master/images/image-20241205151418300.png?raw=true)



## Connect STB dengan SSH

- Jalankan ssh dengan IP STB

  ```bash
  ssh <user>@<ip-address>
  ```

- Selamat anda sudah berhasil terkoneksi dengan STB melalui SSH over USB Tethering

- Tinggal lakukan untuk mengkonfigurasikan STB untuk connect ke WiFi terdekat.