# Jarkom - Modul 1 (2026) - Kelompok 54

Laporan praktikum pembangunan topologi jaringan **"The Wired"** menggunakan GNS3. Skenario mengikuti narasi Lain (Router) yang membangun infrastruktur jaringan untuk menghubungkan lima entitas (client): Alice, Mika, Chisa, Knights, dan Eiri.

## Daftar Isi

*  [1. Membangun Topologi Jaringan](#1-membangun-topologi-jaringan)
*  [2. Konfigurasi Internet Gateway (NAT/DHCP) pada Router Lain](#2-konfigurasi-internet-gateway-natdhcp-pada-router-lain)
*  [3. Konfigurasi Routing Antar Subnet](#3-konfigurasi-routing-antar-subnet)
*  [4. NAT Masquerade & DNS Resolver untuk Setiap Client](#4-nat-masquerade--dns-resolver-untuk-setiap-client)
*  [5. Persistensi Konfigurasi & Script Verifikasi](#5-persistensi-konfigurasi--script-verifikasi)
*  [6. Analisis Traffic dengan Wireshark di Node Mika](#6-analisis-traffic-dengan-wireshark-di-node-mika)
*  [7. FTP Server di Node Chisa dengan Kebijakan Akses](#7-ftp-server-di-node-chisa-dengan-kebijakan-akses)
*  [8. Upload File FTP dari Knights ke Chisa](#8-upload-file-ftp-dari-knights-ke-chisa)
*  [9. Pembatasan Read-Only Akun Mika di FTP Server](#9-pembatasan-read-only-akun-mika-di-ftp-server)
*  [10. Uji Ketahanan Koneksi (Ping Stress Test)](#10-uji-ketahanan-koneksi-ping-stress-test)
*  [16. Analisis FTP Credential Theft (wired_ftp_theft.pcap)](#16-analisis-ftp-credential-theft-wired_ftp_theftpcap)
*  [17. Analisis HTTP C2 Payload Download (wired_http_c2.pcap)](#17-analisis-http-c2-payload-download-wired_http_c2pcap)
*  [18. Analisis SMB Malware Transfer (wired_smb_transfer.pcapng)](#18-analisis-smb-malware-transfer-wired_smb_transferpcapng)
*  [19. Analisis SMTP Extortion Email (wired_smtp_threat.pcap)](#19-analisis-smtp-extortion-email-wired_smtp_threatpcap)
*  [20. Analisis TLS Decryption (wired_tls_decrypt.pcapng)](#20-analisis-tls-decryption-wired_tls_decryptpcapng)

> **Catatan:** Penomoran bagian 16–20 mengikuti nomor soal pada modul praktikum asli (soal 11–15 tidak termasuk dalam cakupan kelompok ini).

---

## 1. Membangun Topologi Jaringan

Lain berperan sebagai **Router** yang menghubungkan tiga Switch/Gateway ke lima entitas (Client):

| Switch | Terhubung ke Entitas |
|--------|----------------------|
| Switch 1 | Alice, Mika |
| Switch 2 | Chisa |
| Switch 3 | Knights, Eiri |

Seluruh entitas dikonfigurasi sebagai Client di GNS3 menggunakan prefix IP milik kelompok masing-masing.

**Topologi Jaringan**

<img width="550" height="350" alt="Topologi Jaringan" src="https://github.com/user-attachments/assets/63a05aaa-c2f6-42d6-9c8a-3ca8e16c0a58" />

**Konfigurasi Client**

| Client | Screenshot |
|--------|------------|
| Alice | <img width="400" alt="Setting client Alice" src="https://github.com/user-attachments/assets/55606689-b422-42a1-9b30-3a5a3c2974c7" /> |
| Mika | <img width="400" alt="Setting client Mika" src="https://github.com/user-attachments/assets/2489d52c-444b-4665-bb31-d9fe704e7452" /> |
| Chisa | <img width="400" alt="Setting client Chisa" src="https://github.com/user-attachments/assets/34bbee29-e16f-43f0-89cf-69a91dd107d8" /> |
| Knights | <img width="400" alt="Setting client Knights" src="https://github.com/user-attachments/assets/e5686ae3-57b5-4aac-ace2-0e53bd1dcc49" /> |
| Eiri | <img width="400" alt="Setting client Eiri" src="https://github.com/user-attachments/assets/ea27d93b-051a-4e1e-932a-dce688b9dfbb" /> |

**Konfigurasi Router Lain** (agar router bertindak sebagai gateway bagi setiap subnet):

<img width="450" height="110" alt="Konfigurasi Router Lain" src="https://github.com/user-attachments/assets/07ff13c7-d747-4a52-8738-c836352ed009" />

---

## 2. Konfigurasi Internet Gateway (NAT/DHCP) pada Router Lain

Router Lain dikonfigurasi agar dapat tersambung langsung ke jaringan internet publik melalui NAT/DHCP pada interface `eth0`.

**Verifikasi Router Lain sudah dapat mengakses internet:**

<img width="682" height="479" alt="Verifikasi akses internet Router Lain" src="https://github.com/user-attachments/assets/f4b4f520-d674-42a9-a2ec-f882866079ff" />

---

## 3. Konfigurasi Routing Antar Subnet

Seluruh entitas (Client) di bawah Switch 1, Switch 2, dan Switch 3 dipastikan dapat saling terhubung dan berkomunikasi satu sama lain melalui konfigurasi routing pada Router Lain.

**Tes ping lintas subnet dari Alice ke Chisa dan Eiri:**

<img width="681" height="477" alt="Tes ping lintas subnet" src="https://github.com/user-attachments/assets/4f4bc2e4-aa0c-42b1-868e-27d75f46fad6" />

---

## 4. NAT Masquerade & DNS Resolver untuk Setiap Client

Agar setiap Client memiliki kemandirian akses internet, dilakukan konfigurasi firewall/iptables (NAT Masquerade) di Router Lain serta DNS resolver di setiap Client.

### 4.1 Konfigurasi NAT, Masquerade, dan IP Forwarding di Router Lain

<img width="1063" height="116" alt="Konfigurasi NAT dan Masquerade" src="https://github.com/user-attachments/assets/804a4d86-b0eb-4cf5-9894-b97298a0c9a5" />

### 4.2 Konfigurasi DNS Resolver di Semua Client

Perintah berikut dijalankan di setiap client agar dapat me-resolve domain seperti `google.com`:

```bash
echo "nameserver 192.168.122.1" > /etc/resolv.conf
echo "nameserver 8.8.8.8" >> /etc/resolv.conf
```

<img width="1059" height="109" alt="Konfigurasi DNS Resolver" src="https://github.com/user-attachments/assets/01f1b229-f40f-46f0-a982-e30bfdb9b232" />

### 4.3 Pengujian

**Tes ping ke IP public (8.8.8.8):**

<img width="1063" height="385" alt="Tes ping ke 8.8.8.8" src="https://github.com/user-attachments/assets/eda25408-e7b7-486d-a1ec-dc3294522ac8" />

**Tes ping ke domain (google.com):**

<img width="1063" height="364" alt="Tes ping ke google.com" src="https://github.com/user-attachments/assets/8063305a-649a-42be-a417-a11f5248d976" />

---

## 5. Persistensi Konfigurasi & Script Verifikasi

Untuk mengantisipasi gangguan (Eiri) berupa restart mendadak, seluruh konfigurasi jaringan dipastikan tidak hilang saat node di-restart. Sebuah script verifikasi dibuat di `/root/cek_status.sh` pada Router Lain untuk menampilkan ringkasan interface dan status tabel NAT.

### 5.1 Isi Script `/root/cek_status.sh`

```bash
#!/bin/bash
echo "=== Ringkasan Interface ==="
ip -br a
echo "=== Status Tabel NAT ==="
iptables -t nat -L -v -n
```

<img width="682" height="478" alt="Script verifikasi cek_status.sh" src="https://github.com/user-attachments/assets/9c9015e9-9fc4-4065-b604-a8f38fa700f3" />

### 5.2 Konfigurasi Persistent (Auto-Load saat Startup)

<img width="680" height="478" alt="Konfigurasi persistent startup" src="https://github.com/user-attachments/assets/3b0ac0c8-f690-46ca-8ca1-45b4e731cf3b" />

### 5.3 Pengujian Script & Simulasi Reboot

<img width="794" height="559" alt="Pengujian script dan simulasi reboot" src="https://github.com/user-attachments/assets/40013ba3-f441-4192-b884-8f1adcced997" />

---

## 6. Analisis Traffic dengan Wireshark di Node Mika

Untuk menyelidiki dugaan anomali traffic, sebuah traffic generator dijalankan pada node Mika, kemudian dilakukan packet sniffing menggunakan Wireshark dengan display filter khusus untuk protokol **DNS** atau **ICMP**.

### 6.1 Menjalankan Packet Sniffing (Wireshark) di Mika

<img width="600" height="474" alt="Wireshark capture di Mika" src="https://github.com/user-attachments/assets/29ab3cb5-330e-4af1-87d6-038a37417df2" />

### 6.2 Download & Eksekusi Traffic Generator

<img width="1059" height="216" alt="Download traffic generator" src="https://github.com/user-attachments/assets/5c9ce0a8-7e1c-4f9b-b16e-4ce4f92d3a4c" />

<img width="674" height="84" alt="Pemberian izin eksekusi" src="https://github.com/user-attachments/assets/df69a1c2-41c1-4a66-a5f6-62b0c341ceec" />

### 6.3 Analisis Hasil Display Filter Wireshark

<img width="1920" height="630" alt="Hasil display filter Wireshark" src="https://github.com/user-attachments/assets/1b96567f-572a-4256-83e3-890c9e60433e" />

**Ringkasan Analisis:**

**Paket DNS (Protocol: DNS)**
- **Traffic:** Node Mika (`192.238.1.3`) melakukan *Standard query* ke DNS Server (`192.168.122.1` dan `8.8.8.8`) untuk mencari alamat IP domain `drive.google.com` dan `drive.usercontent.google.com`.
- **Response:** DNS Server mengembalikan *Standard query response* berisi alokasi IP Google (mis. `64.233.170.138`, `142.250.4.132`, dst).

**Paket ICMP (Protocol: ICMP)**
- **Traffic:** Terdapat paket ICMP dari `192.238.1.3` menuju `8.8.8.8`.
- **Status:** Muncul pesan *Destination Unreachable (Port Unreachable)*, yang mengindikasikan adanya percobaan pengiriman paket ke port yang tidak aktif atau ditolak oleh perangkat jaringan perantara.

---

## 7. FTP Server di Node Chisa dengan Kebijakan Akses

Chisa mendirikan FTP Server (`vsftpd`) dengan shared folder di `/var/wired/data`, dengan kebijakan akses sebagai berikut:

| User | Hak Akses |
|------|-----------|
| `alice` | Read & Write |
| `mika` | Read-only |
| `eiri` | Blacklist (tanpa izin akses) |

### 7.1 Konfigurasi Utama `vsftpd`

<img width="791" height="557" alt="Konfigurasi vsftpd" src="https://github.com/user-attachments/assets/91361eb4-eeac-4921-9bdd-24adc395379e" />

### 7.2 Bukti 1 — User `alice` (Read & Write)

Dari console Alice, dibuat dan diunggah file `signal_alice.txt`:

<img width="787" height="509" alt="Bukti akses read-write Alice" src="https://github.com/user-attachments/assets/ca71c7c2-c4f2-43cc-a993-4cf2e43029c9" />

### 7.3 Bukti 2 — User `mika` (Read-Only)

Dari console Mika, percobaan upload file gagal, membuktikan pembatasan hak akses read-only:

<img width="786" height="471" alt="Bukti pembatasan read-only Mika" src="https://github.com/user-attachments/assets/546f59b5-7f29-44b4-80d6-c332c54f06e4" />

### 7.4 Bukti 3 — User `eiri` (Blacklist / Penolakan Akses)

Dari console Eiri, percobaan login dengan akun `eiri` ditolak sepenuhnya:

<img width="788" height="553" alt="Bukti penolakan akses Eiri" src="https://github.com/user-attachments/assets/0103742a-96fb-41c9-952b-4e0e8d2b564e" />

---

## 8. Upload File FTP dari Knights ke Chisa

Knights melakukan koneksi FTP dari node-nya ke FTP Server Chisa menggunakan akun `alice` untuk mengunggah dokumen laporan intelijen.

### 8.1 Wireshark Capture & Download File Laporan

<img width="688" height="258" alt="Wireshark capture Knights" src="https://github.com/user-attachments/assets/e7c4e3b9-f66a-4323-b97c-8029fbaca211" />

<img width="1061" height="228" alt="Download file laporan Knights" src="https://github.com/user-attachments/assets/14b51af6-4c97-4eab-8ac7-e184d782819f" />

### 8.2 Upload File dari Knights ke Chisa

**Koneksi ke FTP Server Chisa:**

<img width="1055" height="762" alt="Koneksi FTP ke Chisa" src="https://github.com/user-attachments/assets/304fa9a4-edb9-4f0f-aaba-0561cbded5a8" />

<img width="1050" height="208" alt="Proses upload file" src="https://github.com/user-attachments/assets/f6d6e5f0-251c-4e13-8c67-3f869e3ca0d3" />

### 8.3 Analisis Display Filter Wireshark

<img width="1920" height="699" alt="Analisis filter FTP Wireshark" src="https://github.com/user-attachments/assets/ecdd62b2-44ab-4417-83ef-df02a9875bd0" />

**Temuan Analisis Sesi FTP:**
- **Perintah upload:** `STOR` (perintah FTP untuk mengirim/mengunggah file ke server).
- **Kode status sukses server:** `226` (Transfer complete).
- **Mode data transfer:** PASV (Passive Mode), dengan port data TCP dinegosiasikan secara dinamis melalui respons `227 Entering Passive Mode`.

---

## 9. Pembatasan Read-Only Akun Mika di FTP Server

Mika mengakses dokumen "Protokol Tujuh" dari FTP Server Chisa menggunakan akun `mika`, kemudian mencoba mengunggah file baru untuk membuktikan pembatasan read-only.

### 9.1 Download File Menggunakan `curl` di Node Chisa

<img width="718" height="478" alt="Download file dengan curl" src="https://github.com/user-attachments/assets/ff1976f8-00c3-4d1c-8172-5ac8c0f80dfe" />

### 9.2 Eksekusi & Bukti Pengujian di Node Mika

Download file (membuktikan akses *read*) dan percobaan upload file baru (membuktikan pembatasan *read-only*):

<img width="717" height="253" alt="Bukti download dan penolakan upload Mika" src="https://github.com/user-attachments/assets/7873abb7-0c4a-4468-8504-2eb5a4a3406d" />

**Hasil:** Server FTP mengembalikan respons **`550 Permission denied`** saat akun `mika` mencoba melakukan upload, membuktikan kebijakan read-only diterapkan dengan benar.

---

## 10. Uji Ketahanan Koneksi (Ping Stress Test)

Knights melancarkan uji ketahanan koneksi ke node Chisa untuk menguji latensi jaringan The Wired, dengan parameter:

```bash
ping -c 77 -s 128 -i 0.3 192.238.2.2
```

- Jumlah paket: **77**
- Ukuran payload: **128 bytes**
- Interval: **0.3 detik**
- Target: `192.238.2.2` (IP Chisa)

### 10.1 Menjalankan Capture Wireshark di Node Knights

<img width="689" height="266" alt="Wireshark capture di Knights" src="https://github.com/user-attachments/assets/b5394725-c458-4cde-9c15-2ab280494b83" />

### 10.2 Menjalankan Ping Khusus

<img width="963" height="473" alt="Eksekusi ping stress test" src="https://github.com/user-attachments/assets/8a34d302-ad49-488f-8cf9-7f5cda278e7d" />

### 10.3 Hasil Statistik Ping dari Terminal Knights

<img width="821" height="82" alt="Statistik hasil ping" src="https://github.com/user-attachments/assets/48ef5c43-13ea-40b2-9cf9-72795e37857c" />

### 10.4 Analisis ICMP Type & Code di Wireshark

Menggunakan display filter:

```
icmp
```

<img width="1920" height="790" alt="Display filter ICMP di Wireshark" src="https://github.com/user-attachments/assets/487c775e-3da2-4967-a27e-ad3b013bd255" />

**ICMP Type dan Code — Echo Request:**

<img width="1539" height="883" alt="Detail ICMP Echo Request" src="https://github.com/user-attachments/assets/c0528e96-7297-47f7-aaeb-9c048e869bc2" />

**ICMP Type dan Code — Echo Reply:**

<img width="1538" height="890" alt="Detail ICMP Echo Reply" src="https://github.com/user-attachments/assets/f0483c8f-218e-4fb5-877b-dc473264e4f3" />

### 10.5 Analisis Hasil Capture ICMP

| Arah Paket | ICMP Type | ICMP Code |
|------------|-----------|-----------|
| Echo Request (Knights → Chisa) | 8 | 0 |
| Echo Reply (Chisa → Knights) | 0 | 0 |

- **Spesifikasi paket:** total panjang paket adalah **170 bytes** (payload data 128 bytes + header).
- **Performa & RTT:** latensi balasan paket pertama tercatat **0.616 ms**, dengan tingkat **packet loss 0%**.
- **Kesimpulan:** koneksi antara segmen Knights dan Chisa berjalan **sangat stabil**, tanpa ada paket yang hilang selama 77 kali pengiriman dengan interval 0.3 detik.

## 11. Test kelemahan protokol telnet

### Konfigurasi Server Telnet di Node Chisa
<img width="519" height="73" alt="Screenshot 2026-09-16 at 17 37 31" src="https://github.com/user-attachments/assets/44cb001b-6c4e-49a1-96d5-9fb23fd03e25" />

### Melakukan Capture Wireshark & Login dari Node Eiri
<img width="727" height="633" alt="Screenshot 2026-09-16 at 22 34 31" src="https://github.com/user-attachments/assets/ba125d52-b108-45ef-93fe-0309e31ffe0d" />
<img width="1048" height="789" alt="Screenshot 2026-09-16 at 22 37 06" src="https://github.com/user-attachments/assets/2fed6571-a38f-4882-b7d6-cd18e61ceb57" />

### Mengapa setiap karakter terkirim dalam paket TCP terpisah?

Protokol Telnet beroperasi menggunakan mode interaktif (character-at-a-time / NVT). Setiap kali pengguna menekan satu tombol pada keyboard di sisi klien (Eiri), klien Telnet langsung membungkus 1 byte karakter tersebut ke dalam satu segmen/paket TCP tersendiri untuk dikirimkan secara langsung ke server (Chisa), agar server bisa langsung memproses dan menampilkan balasannya (echo) ke layar klien. Hal inilah yang menyebabkan setiap huruf dari username dan password terkirim dalam paket TCP individual yang terpisah, sehingga sangat rentan terhadap penyadapan (sniffing).

---

## 12. Pemindaian Port (Port Scanning) dengan Netcat (nc)

### Setup Service di Node Knights (Target)
<img width="749" height="550" alt="Screenshot 2026-09-16 at 22 49 20" src="https://github.com/user-attachments/assets/bb0916de-6d80-4ff1-92cd-204213658932" />
### Start Wireshark & Pemindaian dari Node Alice
<img width="739" height="535" alt="Screenshot 2026-09-16 at 22 57 48" src="https://github.com/user-attachments/assets/92d1a0e2-c691-45ae-8717-a808a1740eff" />
<img width="974" height="790" alt="Screenshot 2026-09-16 at 22 57 36" src="https://github.com/user-attachments/assets/4922be99-31aa-48cd-9cd1-421d61358d35" />

### Analisis Perbedaan TCP Flag (Port Terbuka vs Port Tertutup):

Port Terbuka (Port 22 & 80):
Saat Alice mengirimkan paket pembuka [SYN], node Knights membalasnya dengan TCP Flag [SYN, ACK]. Hal ini menandakan bahwa port tersebut aktif, memiliki layanan (service listener) yang siap melayani koneksi, dan melanjutkan ke proses TCP 3-Way Handshake.

Port Tertutup (Port 7777):
Saat Alice mengirimkan paket [SYN], node Knights membalasnya dengan TCP Flag [RST, ACK] (Reset). Flag RST dikirimkan secara otomatis oleh sistem operasi Knights karena tidak ada daemon/layanan yang mendengarkan (listening) di port 7777, sehingga permintaan koneksi langsung ditolak.

---
## 13. Konfigurasi Autentikasi SSH Berbasis Kunci (Key-Based Authentication), Pengujian Koneksi & Analisis Lalu Lintas di Wireshark, Analisis Keamanan Data (SSH vs Telnet)

### Langkah 1: Setup SSH Server di Node Knights
<img width="735" height="378" alt="Screenshot 2026-09-16 at 23 57 47" src="https://github.com/user-attachments/assets/e370dbc4-e738-470f-93e0-06167bd9c490" />

### Langkah 2: Generate Keypair di Node Mika & Pasang Public Key
<img width="738" height="491" alt="Screenshot 2026-09-16 at 23 58 30" src="https://github.com/user-attachments/assets/7257cae6-c3dd-4794-a691-146ab334b791" />
<img width="738" height="189" alt="Screenshot 2026-09-16 at 23 58 54" src="https://github.com/user-attachments/assets/92962bdb-a0a7-4d5b-8da3-f3b46f5f5a06" />

### Langkah 3: Start Wireshark & Pengujian Login SSH
<img width="732" height="97" alt="Screenshot 2026-09-16 at 23 59 07" src="https://github.com/user-attachments/assets/f6ee3dcd-04e1-4d08-8e07-4fe2ff3ea673" />
<img width="739" height="533" alt="Screenshot 2026-09-16 at 23 59 45" src="https://github.com/user-attachments/assets/87822cea-c8fb-46a0-be2b-c414d9932a23" />
<img width="1038" height="393" alt="Screenshot 2026-09-17 at 00 03 33" src="https://github.com/user-attachments/assets/32b091d0-b293-478b-9f33-bd0ae430538f" />

## Mengapa kredensial tidak terlihat dalam bentuk teks terbuka seperti Telnet?
- Proses Key Exchange (KEX): Sebelum autentikasi dimulai, SSH melakukan pertukaran kunci menggunakan algoritma kriptografi (seperti Diffie-Hellman). Proses ini menghasilkan session key simetris yang menyandikan seluruh saluran komunikasi.

- Tanpa Pengiriman Password: Pada autentikasi berbasis kunci (Public Key Authentication), password tidak pernah dikirimkan ke jaringan. Klien hanya membuktikan kepemilikan private key dengan mengirimkan tanda tangan digital (digital signature).

- Kerahasiaan Data (Confidentiality): Karena seluruh payload diacak setelah proses KEX selesai, penyadap di Wireshark hanya dapat melihat paket berlabel Encrypted packet tanpa bisa membaca isi perintah maupun kredensialnya.

---

14.

---

## 16. Analisis FTP Credential Theft (wired_ftp_theft.pcap)

**Soal:** Eiri meletakkan file malware di server. Dari file capture `wired_ftp_theft.pcap`, lakukan analisis lalu lintas FTP untuk mengidentifikasi alamat IP server FTP penyerang, banner software FTP yang digunakan, kredensial login penyerang, serta ukuran (size in bytes) dari file malware `knights_payload.exe` yang diunduh. Validasi temuan pada socket server:

```
nc [IP_Group] 3403
```

### 16.1 Filter Paket Wireshark

Buka file `wired_ftp_theft.pcap` lalu filter menggunakan plain text:

```
ftp || ftp-data
```

<img width="1920" height="790" alt="Filter FTP theft Wireshark" src="https://github.com/user-attachments/assets/9be998b1-0430-405a-bcff-7a9b6a75c8cc" />

### 16.2 Identifikasi Poin-Poin yang Diminta

**1. Alamat IP server FTP penyerang**

Diidentifikasi dengan mencari paket yang berisi perintah login penyerang, yaitu `Request: USER <nama_user>` dan `Request: PASS <password>`.

<img width="1844" height="96" alt="Paket login FTP penyerang" src="https://github.com/user-attachments/assets/5f97e975-eadf-41ad-84c3-5560cb99154d" />

Dari screenshot tersebut, IP penyerang terlihat pada kolom *source*: **`10.3.7.50`**.

**2. Banner software FTP**

Terlihat pada teks setelah kode respons `220`:

<img width="1847" height="51" alt="Banner FTP server" src="https://github.com/user-attachments/assets/be3a4324-41cf-4c80-a89d-445c29b015f6" />

Banner software: **`vsftpd 3.0.5`**.

**3. Kredensial login penyerang**

 <img width="1846" height="78" alt="image" src="https://github.com/user-attachments/assets/aa74048e-359e-462d-80a1-e7b763d116a8" />

- **USER:** `knights_agent`
- **PASS:** `N4V1_s3cur3_2026`

**4. Ukuran file malware `knights_payload.exe`**

<img width="1271" height="1079" alt="Ukuran file malware" src="https://github.com/user-attachments/assets/98c2759e-0199-4a75-a54e-919abd42d48d" />

Dari kode `213` pada screenshot, ukuran file adalah **524288 bytes**.

**Flag:** `KOMJAR26{FTP_Th3ft_5wLhhesTC1TqPmupl8X1hMWW4}`

---

## 17. Analisis HTTP C2 Payload Download (wired_http_c2.pcap)

**Soal:** Alice membuat halaman web di node-nya. Eiri memanfaatkan celah untuk mengunduh payload berbahaya ke sistem Alice. Analisis file capture `wired_http_c2.pcap` untuk mengidentifikasi nama domain (Host) tempat malware diunduh, alamat IP server penyerang, nama file executable malware yang diunduh, serta kode status HTTP yang dikembalikan. Validasi temuan pada socket server:

```
nc [IP_Group] 3404
```

### 17.1 Membuka File Wireshark & Menerapkan Display Filter

```
http.request.method == "GET" || http.response
```

<img width="1920" height="444" alt="Filter HTTP GET/response" src="https://github.com/user-attachments/assets/8ac52cc4-5e13-43e0-bf36-0134a54851e8" />

### 17.2 Mengidentifikasi Poin-Poin Jawaban

**1. Domain (Host) tempat malware diunduh**

<img width="1538" height="881" alt="Host domain malware" src="https://github.com/user-attachments/assets/dd612654-65e1-48d1-8a1f-9f99aca6d54f" />

Domain (Host): **`http://wired-update.net/navi_agent.exe`**

**2. Alamat IP server penyerang**

<img width="1920" height="54" alt="IP server penyerang" src="https://github.com/user-attachments/assets/360de884-7646-4862-85cd-322efa29812e" />

Terlihat pada kolom *source* dari paket yang mendownload file malware: **`10.7.1.50`**.

**3. Nama file executable yang diunduh**

Berdasarkan screenshot sebelumnya, nama file malware adalah **`navi_agent.exe`**.

**4. Pengecekan validasi**

```
nc 10.4.89.250 3404
```

<img width="1341" height="766" alt="Validasi socket server" src="https://github.com/user-attachments/assets/31ff8ca5-e84d-4c92-9c00-7407909fcaf5" />

**Flag:** `KOMJAR26{Navi_C2_D0wnl04d_GIV088ToG8640DwGn76JQ7gog}`

---

## 18. Analisis SMB Malware Transfer (wired_smb_transfer.pcapng)

**Soal:** Eiri mengubah taktik penyerangan dengan menanamkan file malware menggunakan protokol file sharing SMB. Analisis file capture `wired_smb_transfer.pcapng` untuk mengidentifikasi nama protokol jaringan yang dieksploitasi, IP pengirim dan penerima, folder tujuan penyimpanan malware pada sistem korban, serta nama file executable malware yang ditransfer. Validasi temuan pada socket server:

```
nc [IP_Group] 3405
```

### 18.1 Membuka File Wireshark & Menerapkan Display Filter

```
smb || smb2
```

<img width="1918" height="481" alt="Filter SMB Wireshark" src="https://github.com/user-attachments/assets/57dbb5b8-8161-4306-8793-25875d2a0881" />

### 18.2 Mengidentifikasi Poin-Poin Jawaban

**1. Protokol jaringan yang dieksploitasi**

Berdasarkan screenshot pada 18.1, protokol yang dieksploitasi adalah **SMB2 (Server Message Block Version 2)**.

**2. IP pengirim & IP penerima**

<img width="1920" height="52" alt="IP pengirim dan penerima SMB" src="https://github.com/user-attachments/assets/7bb43ada-b094-4d4c-bbf4-3b15b200872f" />

Berdasarkan paket *Create Request File*, IP pengirim (source) dan IP penerima (destination):
- **IP Pengirim:** `10.7.3.100`
- **IP Penerima:** `10.7.1.50`

**3. Folder tujuan & nama file executable malware**

<img width="1538" height="887" alt="Folder tujuan dan file malware SMB" src="https://github.com/user-attachments/assets/2d881913-2585-463d-a43f-38a2370e5677" />

- **Folder tujuan:** `\\10.7.1.50\ADMIN$` — folder default/tersembunyi pada Windows yang biasanya mengarah ke folder `C:\Windows`.
- **Nama file executable malware:** `wired_trojan_payload.exe`

**4. Pengujian**

<img width="1344" height="768" alt="Validasi socket server SMB" src="https://github.com/user-attachments/assets/589242af-2771-4611-b99e-b3c44e00e765" />

**Flag:** `KOMJAR26{SMB_Tr4nsf3r_W1w5jGADRzrLoJjLQxtSYJGtg}`

---

## 19. Analisis SMTP Extortion Email (wired_smtp_threat.pcap)

**Soal:** Eiri meneror jaringan dengan mengirimkan email pemerasan melalui protokol SMTP tanpa enkripsi. Analisis file capture `wired_smtp_threat.pcap` pada stream TCP terkait, identifikasi alamat email korban yang ditargetkan, password korban yang diklaim bocor oleh penyerang, jenis malware yang diinfeksikan, batas waktu (dalam hari) yang diberikan, serta MailClientID yang tercantum pada pesan.

### 19.1 Filter File Wireshark & Follow TCP Stream

```
smtp
```

<img width="1920" height="788" alt="Filter SMTP Wireshark" src="https://github.com/user-attachments/assets/8c1937aa-e821-4208-8f2e-896414317517" />

Melakukan *follow* ke TCP Stream `RCPT TO:<victim@protocol7.co.jp>`.

<img width="1282" height="1080" alt="TCP Stream SMTP" src="https://github.com/user-attachments/assets/e2f81821-1688-45f8-b3e3-f797d36cd128" />

### 19.2 Mengidentifikasi Poin-Poin Jawaban

**1. Alamat email korban:** `victim@protocol7.co.jp`

**2. Password korban yang bocor**

<img width="1235" height="861" alt="Pesan pemerasan SMTP" src="https://github.com/user-attachments/assets/365e2296-ab54-4d60-8129-d675eb41b396" />

Berdasarkan pesan attacker, password korban yang bocor adalah `pr0tocol_7_user`.

**3. Jenis malware:** Private Ransomware

**4. Batas waktu yang diberikan:** 72 hours (3 days)

**5. MailClientID:** `7719980706`

### 19.3 Pengujian

<img width="1345" height="771" alt="Validasi socket server SMTP" src="https://github.com/user-attachments/assets/f1c3df06-83aa-44fc-8038-24a5123d2018" />

**Flag:** `KOMJAR26{SMTP_Ext0rt10n_IvT7tpjjObB04a0DJB8FEzv2S}`

---

## 20. Analisis TLS Decryption (wired_tls_decrypt.pcapng)

**Soal:** Untuk rencana pamungkasnya, Eiri menyembunyikan komunikasi malware di balik saluran terenkripsi TLS. Namun Alice telah menyediakan file keylog untuk mendekripsi lalu lintas data tersebut. Analisis file capture `wired_tls_decrypt.pcapng` bersama `keyslogfile.txt` untuk mengidentifikasi versi protokol TLS yang dinegosiasikan, nama domain (SNI) yang diakses, alamat IP server HTTPS penyerang, User-Agent yang digunakan, serta HTTP request method dan path yang tersembunyi di dalam sesi dekripsi.

### 20.1 Memuat SSL Keylog File di Wireshark

1. Membuka file `wired_tls_decrypt.pcapng` di Wireshark.
2. Masuk ke menu **Edit → Preferences**.
3. Di panel sebelah kiri, buka **Protocols → TLS** (atau SSL pada Wireshark versi lama).
4. Cari kolom **(Pre)-Master-Secret log filename**.
5. Klik tombol **Browse...**, lalu pilih file `keyslogfile.txt`.
6. Klik **OK**.

### 20.2 Mengidentifikasi Poin-Poin Jawaban

**1. Versi protokol TLS yang dinegosiasikan**

Filter Wireshark: `tls.handshake.type == 2`, lalu klik paket *Server Hello*.

<img width="1534" height="887" alt="Server Hello TLS" src="https://github.com/user-attachments/assets/9779d2bd-57a3-4798-8f52-6fdd16361fe0" />

Versi TLS: **TLS 1.2**

**2. Nama domain (SNI) yang diakses**

Filter Wireshark: `tls.handshake.extensions_server_name`, lalu klik paket *Client Hello*.

<img width="1541" height="883" alt="SNI Client Hello" src="https://github.com/user-attachments/assets/3cc09dac-76aa-4323-a19c-8849d8c5b46e" />

Domain (SNI): **`example.com`**

**3. Alamat IP server HTTPS penyerang**

<img width="1918" height="106" alt="IP destination Client Hello" src="https://github.com/user-attachments/assets/09059e6b-355e-4874-a049-2b2cd539cfb3" />

Berdasarkan *destination* dari paket *Client Hello*, IP server HTTPS penyerang: **`93.184.216.34`**

**4. User-Agent, HTTP request method, dan path**

Filter Wireshark: `http`

<img width="1920" height="135" alt="Filter HTTP hasil dekripsi" src="https://github.com/user-attachments/assets/8059a31f-22c5-40ba-bd7d-ecb4ad541cce" />

<img width="1538" height="885" alt="Detail HTTP request hasil dekripsi" src="https://github.com/user-attachments/assets/a88ddf66-44a2-468e-a628-5f7f29a90878" />

- **User-Agent:** `curl/7.62.0`
- **HTTP Request Method:** `HEAD`
- **HTTP Path:** `/` (request lengkap: `https://example.com/`)

### 20.3 Pengujian

<img width="455" height="341" alt="Validasi socket server TLS" src="https://github.com/user-attachments/assets/90f0144e-3318-473d-9aeb-f1d724ea3702" />

**Flag:** `KOMJAR26{TLS_D3crypt_7QBYpo1QhCGxlBA5nZtB5ghYb}`

---

## Kesimpulan

Melalui rangkaian konfigurasi di atas, topologi "The Wired" berhasil dibangun dengan Router Lain sebagai gateway utama yang menghubungkan lima entitas melalui tiga switch, terkoneksi ke internet publik melalui NAT/DHCP, memiliki DNS resolver mandiri, konfigurasi persisten pasca-reboot, kemampuan monitoring traffic via Wireshark, layanan FTP Server dengan kebijakan akses berjenjang (read-write, read-only, dan blacklist), serta terbukti memiliki latensi jaringan yang stabil dengan packet loss 0% pada uji ketahanan koneksi antar node. Analisis forensik jaringan lanjutan (FTP, HTTP, SMB, SMTP, dan TLS) juga berhasil mengungkap seluruh jejak serangan Eiri beserta flag terkait pada tiap skenario.
