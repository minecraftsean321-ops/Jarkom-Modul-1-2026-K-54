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


5. Eiri tetap berupaya menanamkan kekacauan ke dalam jaringan. Untuk mengantisipasi restart tiba-tiba, pastikan seluruh konfigurasi jaringan tidak hilang saat semua node di-restart. Buat script verifikasi di /root/cek_status.sh pada router Lain yang menampilkan ringkasan interface (ip -br a) dan status tabel NAT (iptables -t nat -L -v -n) setelah reboot.


6. Mika mencurigai adanya anomali traffic pada segmen jaringannya. Jalankan generator traffic berikut (link file) pada node Mika, lalu lakukan packet sniffing menggunakan Wireshark pada interface node Mika. Terapkan display filter khusus untuk menyaring paket yang berprotokol DNS atau ICMP. Tunjukkan screenshot hasil filter beserta ringkasan paket yang lolos.

7. Chisa memutuskan mendirikan FTP Server pada node miliknya dengan shared folder di /var/wired/data. Terapkan kebijakan akses: user alice (hak akses read & write), user mika (dibatasi read-only), dan user eiri (dibatasi tanpa izin akses / blacklist). Buktikan konfigurasi dengan membuat file signal_alice.txt dari user alice, dan buktikan penolakan akses saat user eiri mencoba login.

8. Kelompok rahasia Knights perlu mengirimkan dokumen laporan intelijen ke FTP Server Chisa. Lakukan koneksi FTP client dari node Knights ke FTP Server Chisa menggunakan akun alice. Upload file berikut (link file). Analisis sesi Wireshark dan sebutkan: perintah FTP untuk upload (STOR), kode status sukses server (226), dan port data TCP yang dinegosiasikan pada mode PASV.

9. Mika mengakses dokumen Protokol Tujuh di (link file) dari FTP Server Chisa. Dari node Mika, unduh file tersebut menggunakan akun mika. Setelah itu, buktikan pembatasan read-only dengayahn mencoba mengunggah file baru dari akun mika, dan tunjukkan pesan error respon server (error 550 Permission denied) saat mika mencoba melakukan upload.

10. Knights melancarkan uji ketahanan koneksi ke server Chisa untuk menguji latensi jaringan The Wired. Kirimkan paket ping dari node Knights ke node Chisa dengan payload khusus 128 bytes dan interval 0.3 detik sebanyak 77 paket (ping -c 77 -s 128 -i 0.3 <IP_Chisa>). Buka Wireshark, catat nilai ICMP Type dan Code untuk Echo Request vs Echo Reply, serta analisis packet loss dan RTT (min/avg/max).
