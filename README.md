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

8. Kelompok rahasia Knights perlu mengirimkan dokumen laporan intelijen ke FTP Server Chisa. Lakukan koneksi FTP client dari node Knights ke FTP Server Chisa menggunakan akun alice. Upload file berikut (link file). Analisis sesi Wireshark dan sebutkan: perintah FTP untuk upload (STOR), kode status sukses server (226), dan port data TCP yang dinegosiasikan pada mode PASV.

9. Mika mengakses dokumen Protokol Tujuh di (link file) dari FTP Server Chisa. Dari node Mika, unduh file tersebut menggunakan akun mika. Setelah itu, buktikan pembatasan read-only dengayahn mencoba mengunggah file baru dari akun mika, dan tunjukkan pesan error respon server (error 550 Permission denied) saat mika mencoba melakukan upload.

10. Knights melancarkan uji ketahanan koneksi ke server Chisa untuk menguji latensi jaringan The Wired. Kirimkan paket ping dari node Knights ke node Chisa dengan payload khusus 128 bytes dan interval 0.3 detik sebanyak 77 paket (ping -c 77 -s 128 -i 0.3 <IP_Chisa>). Buka Wireshark, catat nilai ICMP Type dan Code untuk Echo Request vs Echo Reply, serta analisis packet loss dan RTT (min/avg/max).
