# Jarkom-Modul-1-2026-K-54
Membuat topology jaringan dengan GNS3.

1. Untuk mempersiapkan pembangunan The Wired, Lain yang berperan sebagai Router membuat tiga Switch/Gateway: Switch 1 menuju dua Entitas yaitu Alice dan Mika, Switch 2 menuju Chisa, sedangkan Switch 3 menuju Knights dan Eiri. Kelima Entitas tersebut dikonfigurasi sebagai Client di GNS3. [GUNAKAN PREFIX IP MASING-MASING KELOMPOK]

<img width="550" height="350" alt="image" src="https://github.com/user-attachments/assets/63a05aaa-c2f6-42d6-9c8a-3ca8e16c0a58" />

Setting client Alice  
<img width="550" height="350" alt="image" src="https://github.com/user-attachments/assets/55606689-b422-42a1-9b30-3a5a3c2974c7" />

Setting client Mika  
<img width="550" height="350" alt="image" src="https://github.com/user-attachments/assets/2489d52c-444b-4665-bb31-d9fe704e7452" />

Konfigurasi Router Lain   
<img width="450" height="110" alt="image" src="https://github.com/user-attachments/assets/07ff13c7-d747-4a52-8738-c836352ed009" />

Agar router bisa bertindak sebagai gateaway bagi setiap subnet.


2. Karena menurut Lain pada saat itu The Wired masih terisolasi dari dunia luar, konfigurasikan router Lain agar dapat tersambung langsung ke jaringan internet publik melalui NAT/DHCP pada interface eth0.


3. Setelah router Lain terhubung ke internet, pastikan seluruh Entitas (Client) di bawah Switch 1, Switch 2, dan Switch 3 dapat saling terhubung dan berkomunikasi satu sama lain melalui konfigurasi routing.
