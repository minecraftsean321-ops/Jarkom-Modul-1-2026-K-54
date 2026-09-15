# Jarkom-Modul-1-2026-K-54
Membuat topology jaringan dengan GNS3.

1. Untuk mempersiapkan pembangunan The Wired, Lain yang berperan sebagai Router membuat tiga Switch/Gateway: Switch 1 menuju dua Entitas yaitu Alice dan Mika, Switch 2 menuju Chisa, sedangkan Switch 3 menuju Knights dan Eiri. Kelima Entitas tersebut dikonfigurasi sebagai Client di GNS3. [GUNAKAN PREFIX IP MASING-MASING KELOMPOK]

<img width="550" height="350" alt="image" src="https://github.com/user-attachments/assets/63a05aaa-c2f6-42d6-9c8a-3ca8e16c0a58" />

Setting client Alice  
<img width="550" height="350" alt="image" src="https://github.com/user-attachments/assets/55606689-b422-42a1-9b30-3a5a3c2974c7" />

Setting client Mika  
<img width="550" height="350" alt="image" src="https://github.com/user-attachments/assets/2489d52c-444b-4665-bb31-d9fe704e7452" />

Setting client Chisa  
<img width="550" height="350" alt="WhatsApp Image 2026-09-15 at 12 46 07" src="https://github.com/user-attachments/assets/34bbee29-e16f-43f0-89cf-69a91dd107d8" />

Setting client Knights  
<img width="550" height="350" alt="WhatsApp Image 2026-09-15 at 12 46 07 (1)" src="https://github.com/user-attachments/assets/e5686ae3-57b5-4aac-ace2-0e53bd1dcc49" />

Setting client Eiri  
<img width="550" height="350" alt="WhatsApp Image 2026-09-15 at 12 46 08" src="https://github.com/user-attachments/assets/ea27d93b-051a-4e1e-932a-dce688b9dfbb" />

Konfigurasi Router Lain   
<img width="450" height="110" alt="image" src="https://github.com/user-attachments/assets/07ff13c7-d747-4a52-8738-c836352ed009" />

Agar router bisa bertindak sebagai gateaway bagi setiap subnet.


2. Karena menurut Lain pada saat itu The Wired masih terisolasi dari dunia luar, konfigurasikan router Lain agar dapat tersambung langsung ke jaringan internet publik melalui NAT/DHCP pada interface eth0.

Memastikan Router Lain sudah bisa akses internet
<img width="682" height="479" alt="Screenshot 2026-09-15 at 13 22 02" src="https://github.com/user-attachments/assets/f4b4f520-d674-42a9-a2ec-f882866079ff" />


3. Setelah router Lain terhubung ke internet, pastikan seluruh Entitas (Client) di bawah Switch 1, Switch 2, dan Switch 3 dapat saling terhubung dan berkomunikasi satu sama lain melalui konfigurasi routing.

Tes Ping Lintas Subnet dari Alice ke Chisa dan Eiri
<img width="681" height="477" alt="Screenshot 2026-09-15 at 13 32 15" src="https://github.com/user-attachments/assets/4f4bc2e4-aa0c-42b1-868e-27d75f46fad6" />

4. Lain ingin agar setiap Entitas (Client) memiliki kemandirian di The Wired. Konfigurasikan firewall/iptables (NAT Masquerade) dan DNS resolver agar setiap Client dapat terhubung ke internet secara mandiri (dapat melakukan ping ke 8.8.8.8 dan membuka domain web google.com).

- Konfigurasi NAT dan Masquerede & IP FOWARDING di Router Lain
<img width="1063" height="116" alt="image" src="https://github.com/user-attachments/assets/804a4d86-b0eb-4cf5-9894-b97298a0c9a5" />

- Konfigurasi DNS Resolver di semua Client
Kita perlu menjalankan kode dibawah ini ke setiap client. Agar mereka resolve domain google.com.

```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
echo "nameserver 8.8.8.8" >> /etc/resolv.conf
```

<img width="1059" height="109" alt="image" src="https://github.com/user-attachments/assets/01f1b229-f40f-46f0-a982-e30bfdb9b232" />

Pengujian:

- Tes ping ke IP public (8.8.8.8)
<img width="1063" height="385" alt="image" src="https://github.com/user-attachments/assets/eda25408-e7b7-486d-a1ec-dc3294522ac8" />

- Tes ping ke domain (google.com)  

<img width="1063" height="364" alt="image" src="https://github.com/user-attachments/assets/8063305a-649a-42be-a417-a11f5248d976" />


5. Eiri tetap berupaya menanamkan kekacauan ke dalam jaringan. Untuk mengantisipasi restart tiba-tiba, pastikan seluruh konfigurasi jaringan tidak hilang saat semua node di-restart. Buat script verifikasi di /root/cek_status.sh pada router Lain yang menampilkan ringkasan interface (ip -br a) dan status tabel NAT (iptables -t nat -L -v -n) setelah reboot.

- Buat Script Verifikasi /root/cek_status.sh di Router Lain
<img width="682" height="478" alt="Screenshot 2026-09-15 at 13 52 49" src="https://github.com/user-attachments/assets/9c9015e9-9fc4-4065-b604-a8f38fa700f3" />

- Konfigurasi Persistent (Auto-Load saat Startup)
<img width="680" height="478" alt="Screenshot 2026-09-15 at 13 54 39" src="https://github.com/user-attachments/assets/3b0ac0c8-f690-46ca-8ca1-45b4e731cf3b" />

- Pengujian Script & Simulasi Reboot
<img width="794" height="559" alt="Screenshot 2026-09-15 at 13 56 44" src="https://github.com/user-attachments/assets/40013ba3-f441-4192-b884-8f1adcced997" />


6. Mika mencurigai adanya anomali traffic pada segmen jaringannya. Jalankan generator traffic berikut (link file) pada node Mika, lalu lakukan packet sniffing menggunakan Wireshark pada interface node Mika. Terapkan display filter khusus untuk menyaring paket yang berprotokol DNS atau ICMP. Tunjukkan screenshot hasil filter beserta ringkasan paket yang lolos.

- Menjalankan paket sniffing (Wireshark) pada Mika.  
  
<img width="600" height="474" alt="image" src="https://github.com/user-attachments/assets/29ab3cb5-330e-4af1-87d6-038a37417df2" />

- Download Fila Generator Traffic dan beri izin eksekusi kepada file

<img width="1059" height="216" alt="image" src="https://github.com/user-attachments/assets/5c9ce0a8-7e1c-4f9b-b16e-4ce4f92d3a4c" />

<img width="674" height="84" alt="image" src="https://github.com/user-attachments/assets/df69a1c2-41c1-4a66-a5f6-62b0c341ceec" />

- Analisis hasil display filter di Wireshark   
<img width="1920" height="630" alt="image" src="https://github.com/user-attachments/assets/1b96567f-572a-4256-83e3-890c9e60433e" />

1. Paket DNS (Protocol: DNS):

- Traffic: Node Mika (192.238.1.3) melakukan query DNS (Standard query) ke DNS Server (192.168.122.1 dan 8.8.8.8) untuk mencari alamat IP dari domain drive.google.com dan drive.usercontent.google.com.

- Response: DNS Server memberikan jawaban (Standard query response) berisi alokasi IP Google (seperti 64.233.170.138, 142.250.4.132, dsb.).

2. Paket ICMP (Protocol: ICMP):

- Traffic: Terdapat paket ICMP dari 192.238.1.3 ke 8.8.8.8.

- Status: Muncul pesan Destination unreachable (Port unreachable) yang mengindikasikan adanya percobaan pengiriman pesan/paket ke port tertentu yang tidak aktif atau ditolak oleh perantara jaringan.

7. Chisa memutuskan mendirikan FTP Server pada node miliknya dengan shared folder di /var/wired/data. Terapkan kebijakan akses: user alice (hak akses read & write), user mika (dibatasi read-only), dan user eiri (dibatasi tanpa izin akses / blacklist). Buktikan konfigurasi dengan membuat file signal_alice.txt dari user alice, dan buktikan penolakan akses saat user eiri mencoba login.

- Kode konfigurasi utama vsftpd
  <img width="791" height="557" alt="Screenshot 2026-09-15 at 14 52 57" src="https://github.com/user-attachments/assets/91361eb4-eeac-4921-9bdd-24adc395379e" />
- Bukti 1: User alice (Read & Write). Buka console Alice, jalankan perintah untuk membuat dan upload file signal_alice.txt
  <img width="787" height="509" alt="Screenshot 2026-09-15 at 15 32 46" src="https://github.com/user-attachments/assets/ca71c7c2-c4f2-43cc-a993-4cf2e43029c9" />
- Bukti 2: User mika (Read-Only). Buka console Mika, coba upload file untuk membuktikan penolakan hak akses write:
  <img width="786" height="471" alt="Screenshot 2026-09-15 at 15 36 41" src="https://github.com/user-attachments/assets/546f59b5-7f29-44b4-80d6-c332c54f06e4" />
- Bukti 3: User eiri (Blacklist / Penolakan Akses). Buka console Eiri, coba login dengan akun eiri:
  <img width="788" height="553" alt="Screenshot 2026-09-15 at 15 38 44" src="https://github.com/user-attachments/assets/0103742a-96fb-41c9-952b-4e0e8d2b564e" />

8. Kelompok rahasia Knights perlu mengirimkan dokumen laporan intelijen ke FTP Server Chisa. Lakukan koneksi FTP client dari node Knights ke FTP Server Chisa menggunakan akun alice. Upload file berikut (link file). Analisis sesi Wireshark dan sebutkan: perintah FTP untuk upload (STOR), kode status sukses server (226), dan port data TCP yang dinegosiasikan pada mode PASV.

1. Menjalankan Wireshark Capture dan Download file laporan Knights.

  <img width="688" height="258" alt="image" src="https://github.com/user-attachments/assets/e7c4e3b9-f66a-4323-b97c-8029fbaca211" />

  <img width="1061" height="228" alt="image" src="https://github.com/user-attachments/assets/14b51af6-4c97-4eab-8ac7-e184d782819f" />

2. Mengupload file dari Knights ke Chisa

   - Koneksi ke FTP server Chisa
     <img width="1055" height="762" alt="image" src="https://github.com/user-attachments/assets/304fa9a4-edb9-4f0f-aaba-0561cbded5a8" />

     <img width="1050" height="208" alt="image" src="https://github.com/user-attachments/assets/f6d6e5f0-251c-4e13-8c67-3f869e3ca0d3" />

- Analisis Display Filter Wireshark
  
  <img width="1920" height="699" alt="image" src="https://github.com/user-attachments/assets/ecdd62b2-44ab-4417-83ef-df02a9875bd0" />


9. Mika mengakses dokumen Protokol Tujuh di (link file) dari FTP Server Chisa. Dari node Mika, unduh file tersebut menggunakan akun mika. Setelah itu, buktikan pembatasan read-only dengayahn mencoba mengunggah file baru dari akun mika, dan tunjukkan pesan error respon server (error 550 Permission denied) saat mika mencoba melakukan upload.

Step 1: Download File Menggunakan curl di Node Chisa
<img width="718" height="478" alt="Screenshot 2026-09-15 at 16 02 36" src="https://github.com/user-attachments/assets/ff1976f8-00c3-4d1c-8172-5ac8c0f80dfe" />

Step 2: Eksekusi & Bukti Pengujian Soal No. 9 di Node Mika
Download File (Membuktikan Akses Read) & Coba Upload File Baru (Membuktikan Pembatasan Read-Only)
<img width="717" height="253" alt="Screenshot 2026-09-15 at 16 05 05" src="https://github.com/user-attachments/assets/7873abb7-0c4a-4468-8504-2eb5a4a3406d" />


10. Knights melancarkan uji ketahanan koneksi ke server Chisa untuk menguji latensi jaringan The Wired. Kirimkan paket ping dari node Knights ke node Chisa dengan payload khusus 128 bytes dan interval 0.3 detik sebanyak 77 paket (ping -c 77 -s 128 -i 0.3 <IP_Chisa>). Buka Wireshark, catat nilai ICMP Type dan Code untuk Echo Request vs Echo Reply, serta analisis packet loss dan RTT (min/avg/max).

    1. Menjalankan Capture Wireshark di Node Knights
       <img width="689" height="266" alt="image" src="https://github.com/user-attachments/assets/b5394725-c458-4cde-9c15-2ab280494b83" />

    2. Menjalankan ping khusus
   
    ```
    ping -c 77 -s 128 -i 0.3 192.238.2.2
    ```

    <img width="963" height="473" alt="image" src="https://github.com/user-attachments/assets/8a34d302-ad49-488f-8cf9-7f5cda278e7d" />

    3. Catat hasil Statistik Ping dari Terminal Knights

    <img width="821" height="82" alt="image" src="https://github.com/user-attachments/assets/48ef5c43-13ea-40b2-9cf9-72795e37857c" />

    4. Analisis ICMP Type & Code di Wireshark

       Menggunakan display filter:
       
        ```
        icmp
        ```

    <img width="1920" height="790" alt="image" src="https://github.com/user-attachments/assets/487c775e-3da2-4967-a27e-ad3b013bd255" />

    5. Mengambil data type dan code di wireshark

    ICMP Type and Code (Echo Request):

    <img width="1539" height="883" alt="image" src="https://github.com/user-attachments/assets/c0528e96-7297-47f7-aaeb-9c048e869bc2" />


    ICMP Type and Code (Echo Reply):

    <img width="1538" height="890" alt="image" src="https://github.com/user-attachments/assets/f0483c8f-218e-4fb5-877b-dc473264e4f3" />

Analisis Hasil Capture ICMP (Soal 10):

1. Nilai Type dan Code ICMP:
- Echo Request (Knights $\rightarrow$ Chisa): Type = 8, Code = 0
- Echo Reply (Chisa $\rightarrow$ Knights): Type = 0, Code = 0
2. Spesifikasi Paket: Total panjang paket adalah 170 bytes (terdiri dari payload data 128 bytes ditambah header).
3. Performa & RTT: Latensi balasan paket pertama adalah 0.616 ms dengan tingkat packet loss 0%, mengindikasikan koneksi antara segmen Knights dan Chisa berjalan sangat stabil.
    
