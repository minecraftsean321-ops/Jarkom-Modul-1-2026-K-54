# Jarkom - Modul 1 (2026) - Kelompok 54

Laporan praktikum pembangunan topologi jaringan **"The Wired"** menggunakan GNS3. Skenario mengikuti narasi Lain (Router) yang membangun infrastruktur jaringan untuk menghubungkan lima entitas (client): Alice, Mika, Chisa, Knights, dan Eiri.

## Daftar Isi

1. [Membangun Topologi Jaringan](#1-membangun-topologi-jaringan)
2. [Konfigurasi Internet Gateway (NAT/DHCP) pada Router Lain](#2-konfigurasi-internet-gateway-natdhcp-pada-router-lain)
3. [Konfigurasi Routing Antar Subnet](#3-konfigurasi-routing-antar-subnet)
4. [NAT Masquerade & DNS Resolver untuk Setiap Client](#4-nat-masquerade--dns-resolver-untuk-setiap-client)
5. [Persistensi Konfigurasi & Script Verifikasi](#5-persistensi-konfigurasi--script-verifikasi)
6. [Analisis Traffic dengan Wireshark di Node Mika](#6-analisis-traffic-dengan-wireshark-di-node-mika)
7. [FTP Server di Node Chisa dengan Kebijakan Akses](#7-ftp-server-di-node-chisa-dengan-kebijakan-akses)
8. [Upload File FTP dari Knights ke Chisa](#8-upload-file-ftp-dari-knights-ke-chisa)
9. [Pembatasan Read-Only Akun Mika di FTP Server](#9-pembatasan-read-only-akun-mika-di-ftp-server)
10. [Uji Ketahanan Koneksi (Ping Stress Test)](#10-uji-ketahanan-koneksi-ping-stress-test)

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

---

## 16. Eiri meletakkan file malware di server. Dari file capture wired_ftp_theft.pcap, lakukan analisis lalu lintas FTP untuk mengidentifikasi alamat IP server FTP penyerang, banner software FTP yang digunakan, kredensial login penyerang, serta ukuran (size in bytes) dari file malware knights_payload.exe yang diunduh. Validasi temuan kalian pada socket server:
	(link file) nc [IP_Group] 3403 

## 16.1 Filter paket Wireshark
Buka file wireshark_ftp_theft.pcap lalu filter menggunakan plain text:

```
ftp || ftp-data
```

<img width="1920" height="790" alt="image" src="https://github.com/user-attachments/assets/9be998b1-0430-405a-bcff-7a9b6a75c8cc" />

## 16.2 Identifikasi poin-poin yang diminta

1. Identifikasi alamat IP server FTP Penyerang
Kita bisa mengidentifikasinya dengan mencari paket yang berisi perintah login penyerang, seperti Request: USER <nama_user> dan Request: PASS <password>.

<img width="1844" height="96" alt="image" src="https://github.com/user-attachments/assets/5f97e975-eadf-41ad-84c3-5560cb99154d" />

Dari screenshot tersebut kita bisa melihat IP dari penyerang di kolom source yaitu 10.3.7.50.

2. Untuk melihat banner software yang digunakan kita bisa melihat text setelah kode 220:

<img width="1847" height="51" alt="image" src="https://github.com/user-attachments/assets/be3a4324-41cf-4c80-a89d-445c29b015f6" />

Dari situ kita bisa melihat versi banner software nya yaitu vsftpd 3.0.5

3. Melihat kredensial login penyerang

 <img width="1846" height="78" alt="image" src="https://github.com/user-attachments/assets/aa74048e-359e-462d-80a1-e7b763d116a8" />

  Berdasarkan screenshot tersebut kita bisa mendapatkan kredensial login dari penyerang:
  
  -USER: knights_agent, PASS: N4V1_s3cur3_2026

  4. Ukuran bytes dari file malware knights_payload.exe

<img width="1271" height="1079" alt="image" src="https://github.com/user-attachments/assets/98c2759e-0199-4a75-a54e-919abd42d48d" />

Dari kode 213 di screenshot tersebut kita bisa tahu ukuran bytes dari file malware knights_payload.exe itu 524288.

flag=KOMJAR26{FTP_Th3ft_5wLhhesTC1TqPmupl8X1hMWW4};

## 17. Alice membuat halaman web di node-nya. Eiri memanfaatkan celah untuk mengunduh payload berbahaya ke sistem Alice. Analisis file capture wired_http_c2.pcap untuk mengidentifikasi nama domain (Host) tempat malware diunduh, alamat IP server penyerang, nama file executable malware yang diunduh, serta kode status HTTP yang dikembalikan. Validasi temuan kalian pada socket server:
(link file) nc [IP_Group] 3404

## 17.1 Membuka file wireshark & menerapkan display Filter

```
http.request.method == "GET" || http.response
```

<img width="1920" height="444" alt="image" src="https://github.com/user-attachments/assets/8ac52cc4-5e13-43e0-bf36-0134a54851e8" />

## 17.2 Mengidentifikasi Poin-Poin Jawaban

1. Mengidentifikasi nama Domain (Host) tempat malware diunduh

   <img width="1538" height="881" alt="image" src="https://github.com/user-attachments/assets/dd612654-65e1-48d1-8a1f-9f99aca6d54f" />

Dari screenshot tersebut Domain (Host) tempat malware diunduh adalah: http://wired-update.net/navi_agent.exe.

2. Alamat IP server penyerang

   <img width="1920" height="54" alt="image" src="https://github.com/user-attachments/assets/360de884-7646-4862-85cd-322efa29812e" />

Dari screenshot tersebut kita bisa melihat source dari penyerang yang mendownload file malware itu: 10.7.1.50.

3. Nama file executable yang diunduh.
	Bisa kita lihat dari screenshotan yang tadi kalau nama file malware nya itu navi_agent.exe.

4. Pengecekan lewat

   ```
	nc 10.4.89.250 3404
   ```

   <img width="1341" height="766" alt="image" src="https://github.com/user-attachments/assets/31ff8ca5-e84d-4c92-9c00-7407909fcaf5" />

	flag: KOMJAR26{Navi_C2_D0wnl04d_GIV088ToG8640DwGn76JQ7gog}

## Kesimpulan

Melalui rangkaian konfigurasi di atas, topologi "The Wired" berhasil dibangun dengan Router Lain sebagai gateway utama yang menghubungkan lima entitas melalui tiga switch, terkoneksi ke internet publik melalui NAT/DHCP, memiliki DNS resolver mandiri, konfigurasi persisten pasca-reboot, kemampuan monitoring traffic via Wireshark, layanan FTP Server dengan kebijakan akses berjenjang (read-write, read-only, dan blacklist), serta terbukti memiliki latensi jaringan yang stabil dengan packet loss 0% pada uji ketahanan koneksi antar node.
