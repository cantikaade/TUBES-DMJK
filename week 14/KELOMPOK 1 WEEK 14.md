**Deliverable Pekan 14 - Keamanan (ACL) & Pengujian Menyeluruh**

**Disusun oleh Kelompok [Kelompok 1]:**

1.  **Nadhilah Nur Amalina** - Network Architect
2.  **Raditya Yudianto** - Network Engineer
3.  **Irwan Maulana** - Network Services Specialist
4.  **Cantika Ade Qutnindra Maharani** - Security & Documentation Specialist

**Tanggal Pengumpulan:** Jumat, 16 Mei 2025

---

### 1. Link File Simulasi Final

*   **File Simulasi Final:** `https://github.com/cantikaade/TUBES-DMJK/blob/main/week%2014/KELOMPOK%201%20WEEK%2014.pkt`
*   **Platform:** Cisco Packet Tracer Versi [Sebutkan Versi, misal: 8.2.1]
*   **Github:** `https://github.com/cantikaade/TUBES-DMJK/tree/main/week%2014`

---

### 2. Dokumentasi Konfigurasi CLI Lengkap (Implementasi ACL)

**Kebijakan Keamanan (Contoh - Sesuaikan dengan Kebijakan Tim Anda):**

*   **IT Department (VLAN 10 - 192.168.10.0/24):**
    *   Boleh akses ke semua departemen lain (VLAN 20, 30, 50, 60) dan Server Farm (VLAN 40).
    *   Boleh akses ke Internet.
*   **Finance Department (VLAN 20 - 192.168.11.0/24):**
    *   Boleh akses ke HR (VLAN 30).
    *   Boleh akses ke Server Farm (VLAN 40)
    *   **TIDAK BOLEH** akses ke Marketing (VLAN 50) dan Operations (VLAN 60).
    *   Boleh akses ke Internet.
    *   Hanya boleh diakses oleh IT.
*   **HR Department (VLAN 30 - 192.168.12.0/24):**
    *   Boleh akses ke semua departemen lain untuk koordinasi.
    *   Akses terbatas ke Server Farm (misal, hanya ke server HR jika ada, atau akses umum untuk simulasi ini).
    *   Boleh akses ke Internet.
*   **Server Farm (VLAN 40 - 192.168.13.0/24):**
    *   Secara umum, hanya boleh diakses dari departemen yang diizinkan.
    *   Server boleh inisiasi koneksi ke Internet (misal untuk update, DNS query).
*   **Marketing Department (VLAN 50 - 192.168.20.0/24):**
    *   Boleh akses terbatas ke Server Farm (misal, server web/data marketing).
    *   **TIDAK BOLEH** akses ke Finance (VLAN 20).
    *   Boleh akses ke Internet.
    *   Boleh akses ke Operations (VLAN 60) dan diakses oleh Operations.
*   **Operations Department (VLAN 60 - 192.168.21.0/24):**
    *   Boleh akses terbatas ke Server Farm.
    *   **TIDAK BOLEH** akses ke Finance (VLAN 20).
    *   Boleh akses ke Internet.
    *   Boleh akses ke Marketing (VLAN 50) dan diakses oleh Marketing.

**Implementasi ACL:**
ACL akan diterapkan pada sub-interface di `RouterA` dan `RouterB` pada arah `in` (untuk traffic yang masuk ke sub-interface dari VLAN tersebut) atau `out` (untuk traffic yang keluar dari sub-interface menuju VLAN tersebut). Strategi yang umum adalah menerapkan ACL sedekat mungkin dengan sumber traffic yang ingin dikontrol.

```markdown
### ACL Configuration on RouterA
```

```bash
RouterA> enable
RouterA# configure terminal

# --- ACL untuk VLAN 20 (Finance) ---
# Mencegah Finance mengakses Marketing (VLAN 50) dan Operations (VLAN 60)
# Mengizinkan akses ke HR (VLAN 30) dan Server Farm (VLAN 40)
# Mengizinkan akses ke Internet (melalui default route yang akan dipelajari atau dikonfigurasi)
# (Implisit deny all di akhir ACL)

RouterA(config)# access-list 120 permit udp any any eq bootps  # Izinkan DHCP Discover/Request
RouterA(config)# access-list 120 deny   ip 192.168.11.0 0.0.0.255 192.168.20.0 0.0.0.255  # Deny Finance to Marketing
RouterA(config)# access-list 120 deny   ip 192.168.11.0 0.0.0.255 192.168.21.0 0.0.0.255  # Deny Finance to Operations
RouterA(config)# access-list 120 permit ip 192.168.11.0 0.0.0.255 192.168.12.0 0.0.0.255  # Permit Finance to HR
RouterA(config)# access-list 120 permit ip 192.168.11.0 0.0.0.255 192.168.13.0 0.0.0.255  # Permit Finance to Server Farm
RouterA(config)# access-list 120 permit ip 192.168.11.0 0.0.0.255 any                    # Permit Finance to any (termasuk Internet & VLAN lain yg tidak di-deny)

# Terapkan ACL 120 ke sub-interface VLAN 20 (Finance) arah MASUK
RouterA(config)# interface GigabitEthernet0/0.20
RouterA(config-subif)# ip access-group 120 in
RouterA(config-subif)# exit

# --- ACL untuk membatasi akses KE Finance (VLAN 20) ---
# Hanya IT (VLAN 10) dan HR (VLAN 30) yang boleh inisiasi ke Finance
RouterA(config)# access-list 121 permit ip 192.168.10.0 0.0.0.255 192.168.11.0 0.0.0.255  # Permit IT to Finance
RouterA(config)# access-list 121 permit ip 192.168.12.0 0.0.0.255 192.168.11.0 0.0.0.255  # Permit HR to Finance
RouterA(config)# access-list 121 deny   ip any 192.168.11.0 0.0.0.255                    # Deny all other to Finance
RouterA(config)# access-list 121 permit ip any any                                      # Permit all other traffic (dibutuhkan agar traffic keluar dari Finance tidak diblok)

# Terapkan ACL 121 ke sub-interface VLAN 20 (Finance) arah KELUAR
# Ini akan memfilter traffic yang ditujukan KE jaringan Finance
RouterA(config)# interface GigabitEthernet0/0.20
RouterA(config-subif)# ip access-group 121 out
RouterA(config-subif)# exit

# Catatan:
# - IT Dept (VLAN 10) tidak dibatasi secara eksplisit, jadi secara default boleh akses ke mana saja.
# - HR Dept (VLAN 30) boleh akses ke semua departemen (tidak ada ACL 'in' yang membatasi traffic keluar dari HR).
# - Server Farm (VLAN 40) tidak memiliki ACL 'in' yang membatasi traffic keluarnya, namun akses KEPADANYA akan diatur oleh ACL 'in' pada VLAN lain.

RouterA(config)# end
RouterA# write memory
```

```markdown
### ACL Configuration on RouterB
```

```bash
RouterB> enable
RouterB# configure terminal

# --- ACL untuk VLAN 50 (Marketing) ---
# Mencegah Marketing mengakses Finance (VLAN 20)
# Mengizinkan akses ke Server Farm (VLAN 40 - terbatas, tapi kita izinkan semua untuk simulasi)
# Mengizinkan akses ke Operations (VLAN 60)
# Mengizinkan akses ke Internet

RouterB(config)# access-list 150 permit udp any any eq bootps  # Izinkan DHCP Discover/Request
RouterB(config)# access-list 150 deny   ip 192.168.20.0 0.0.0.255 192.168.11.0 0.0.0.255  # Deny Marketing to Finance
RouterB(config)# access-list 150 permit ip 192.168.20.0 0.0.0.255 192.168.13.0 0.0.0.255  # Permit Marketing to Server Farm
RouterB(config)# access-list 150 permit ip 192.168.20.0 0.0.0.255 192.168.21.0 0.0.0.255  # Permit Marketing to Operations
RouterB(config)# access-list 150 permit ip 192.168.20.0 0.0.0.255 any                    # Permit Marketing to any (Internet & other allowed)

# Terapkan ACL 150 ke sub-interface VLAN 50 (Marketing) arah MASUK
RouterB(config)# interface GigabitEthernet0/0.50
RouterB(config-subif)# ip access-group 150 in
RouterB(config-subif)# exit

# --- ACL untuk VLAN 60 (Operations) ---
# Mencegah Operations mengakses Finance (VLAN 20)
# Mengizinkan akses ke Server Farm (VLAN 40)
# Mengizinkan akses ke Marketing (VLAN 50)
# Mengizinkan akses ke Internet

RouterB(config)# access-list 160 permit udp any any eq bootps  # Izinkan DHCP Discover/Request
RouterB(config)# access-list 160 deny   ip 192.168.21.0 0.0.0.255 192.168.11.0 0.0.0.255  # Deny Operations to Finance
RouterB(config)# access-list 160 permit ip 192.168.21.0 0.0.0.255 192.168.13.0 0.0.0.255  # Permit Operations to Server Farm
RouterB(config)# access-list 160 permit ip 192.168.21.0 0.0.0.255 192.168.20.0 0.0.0.255  # Permit Operations to Marketing
RouterB(config)# access-list 160 permit ip 192.168.21.0 0.0.0.255 any                    # Permit Operations to any (Internet & other allowed)

# Terapkan ACL 160 ke sub-interface VLAN 60 (Operations) arah MASUK
RouterB(config)# interface GigabitEthernet0/0.60
RouterB(config-subif)# ip access-group 160 in
RouterB(config-subif)# exit

RouterB(config)# end
RouterB# write memory
```

**Penting:**
*   Urutan baris dalam ACL sangat penting. Baris pertama yang cocok akan diterapkan.
*   Setiap ACL memiliki `deny any any` implisit di akhir jika tidak ada `permit any any` eksplisit. Untuk ACL yang diterapkan pada arah `in` yang bertujuan membatasi traffic *keluar* dari VLAN tersebut, `permit ip <source_vlan_subnet> any` di akhir (setelah semua `deny`) penting agar traffic yang diizinkan bisa keluar.
*   ACL `out` pada sub-interface memfilter traffic yang *ditujukan* ke subnet VLAN tersebut.
*   Desain ACL bisa sangat kompleks. Contoh di atas adalah dasar. Pertimbangkan penggunaan ACL *extended* untuk kontrol port spesifik jika diperlukan (misalnya, hanya izinkan HTTP ke server tertentu).

---

### 3. Matriks Pengujian (Semua Fitur)

| Fitur yang Diuji                     | Skenario Pengujian                                                                 | Perangkat Sumber (IP/VLAN)        | Perangkat Tujuan (IP/VLAN/URL)          | Hasil yang Diharapkan | Hasil Aktual | Screenshot (Link/Embed) | Catatan                                                                 |
| :------------------------------------- | :--------------------------------------------------------------------------------- | :------------------------------ | :-------------------------------------- | :-------------------- | :----------- | :---------------------- | :---------------------------------------------------------------------- |
| **Konektivitas Dasar & DHCP**        | PC IT dapat IP DHCP                                                              | PC-IT0 (VLAN 10)                | (DHCP Server 192.168.13.10)           | IP Valid Diterima     | Berhasil     | ![Image](https://github.com/user-attachments/assets/a84e2527-1511-4132-9b71-7cdea26e1c14)            | Memverifikasi DHCP server & relay agent                                 |
|                                        | PC Finance dapat IP DHCP                                                           | PC-FIN0 (VLAN 20)               | (DHCP Server 192.168.13.10)           | IP Valid Diterima     | Berhasil     |         ![Image](https://github.com/user-attachments/assets/bec29d4f-b2ec-492e-a5fd-11d7f9e5c83b)|                                                
|                                        | PC Marketing dapat IP DHCP                                                         | PC-MKT0 (VLAN 50)               | (DHCP Server 192.168.13.10)           | IP Valid Diterima     | Berhasil     | ![Image](https://github.com/user-attachments/assets/9a1f994e-b20b-4100-b1b3-11a6e9b417bd)                                                                         |
| **Inter-VLAN Routing (Gedung A)**    | PC IT ping PC Finance                                                              | PC-IT0 (192.168.10.x / VLAN 10) | PC-FIN0 (192.168.11.x / VLAN 20)      | Berhasil              | Berhasil     | ![Image](https://github.com/user-attachments/assets/c7c75365-5139-4cfb-88fc-88fe7769cf9f)        | RouterA inter-VLAN                                                      |
|                                        | PC Finance ping PC HR                                                              | PC-FIN0 (192.168.11.x / VLAN 20) | PC-HR0 (192.168.12.x / VLAN 30)       | Berhasil              | Berhasil     | ![Image](https://github.com/user-attachments/assets/3a36039e-e2f7-40e7-ac68-7b31b836cfc4)        | Sesuai kebijakan (ACL 120 permit)                                       |
| **Inter-VLAN Routing (Gedung B)**    | PC Marketing ping PC Operations                                                    | PC-MKT0 (192.168.20.x / VLAN 50) | PC-OPS0 (192.168.21.x / VLAN 60)      | Berhasil              | Berhasil     | ![Image](https://github.com/user-attachments/assets/6e8f8c70-8bc3-42c6-aca5-1171ee154795)       | RouterB inter-VLAN & ACL 150 permit                                     |
| **Routing Antar Gedung (OSPF)**      | PC IT ping PC Marketing                                                            | PC-IT0 (VLAN 10)                | PC-MKT0 (VLAN 50)                     | Berhasil              | Berhasil     | ![Image](https://github.com/user-attachments/assets/cb524ed3-80ef-45f2-b1dd-95b5d9d4f02a)       | OSPF antar RouterA, Core, RouterB                                       |
|                                        | Server ping PC Operations                                                          | Server0 (192.168.13.10 / VLAN 40) | PC-OPS0 (VLAN 60)                     | Berhasil              | Berhasil     | ![Image](https://github.com/user-attachments/assets/b9c2d05f-5bf6-4255-8f53-3ba129074283)       |                                                                         |
| **Layanan DNS Internal**             | PC HR nslookup server0.nusantara-network.local                                     | PC-HR0 (VLAN 30)                | server0.nusantara-network.local       | Resolusi ke 192.168.13.10 | Berhasil     | ![Image](https://github.com/user-attachments/assets/f7df817d-413e-45bf-83c2-45f2c83236ee)     | DNS Server (Server0) berfungsi                                          |
|                                        | PC IT ping router-b.nusantara-network.local                                        | PC-IT0 (VLAN 10)                | router-b.nusantara-network.local      | Ping ke 192.168.99.3 Berhasil | Berhasil     | ![Image](https://github.com/user-attachments/assets/32a808e1-5daa-4af1-a737-fdeb285e8632)    |                                                                         |
| **Akses Internet (NAT)**             | Cek NAT Translation di CoreRouter                                                  | CoreRouter CLI                  | `show ip nat translations`              | Ada entri untuk PC Finance | Berhasil     | ![Image](https://github.com/user-attachments/assets/36f68a6b-fb8a-40c6-a34c-f38b3b9afa61)        |   NAT Core Router Berfungsi                                                                      |
| **Implementasi ACL - Finance**       | PC Finance ping PC Marketing                                                       | PC-FIN0 (VLAN 20)               | PC-MKT0 (VLAN 50)                     | Gagal (Unreachable)   | Berhasil     | ![Image](https://github.com/user-attachments/assets/32e56d59-2d5b-4d81-9eae-f2b4d14da2de) | ACL 120 deny Finance to Marketing                                       |
| **Implementasi ACL - Marketing**     | PC Marketing ping PC Finance                                                       | PC-MKT0 (VLAN 50)               | PC-FIN0 (VLAN 20)                     | Gagal (Unreachable)   | Berhasil     | ![Image](https://github.com/user-attachments/assets/55b1fbac-b46c-4e54-8226-ca8b7a2cabf5) | ACL 150 deny Marketing to Finance                                     |
|                                        | PC Marketing ping Server0                                                          | PC-MKT0 (VLAN 50)               | Server0 (192.168.13.10 / VLAN 40)     | Berhasil              | Berhasil     | ![Image](https://github.com/user-attachments/assets/3e3fc225-1079-42f1-b9ce-850a954a2118)   | ACL 150 permit Marketing to ServerFarm                                  |
| **Implementasi ACL - Umum**          | Cek log ACL (jika mungkin di PT) atau `show access-list [no_acl]` untuk `matches` | RouterA/RouterB CLI             | `show access-list 120` dsb.           | Ada `matches` pada rule | Sesuai       | ![Image](https://github.com/user-attachments/assets/6d9625a5-ff0a-4587-85c7-016113671719) ![Image](https://github.com/user-attachments/assets/92b70783-a4d0-46c4-9ecf-c084542df837)    | Untuk memverifikasi ACL di-trigger                                      |


---

### 4. Hasil Troubleshooting (Jika Ada)

**(Contoh Bagian Troubleshooting - Isi sesuai pengalaman Anda)**

*   **Masalah 1: Kegagalan NAT Awal (Pekan 13)**
    *   **Deskripsi:** PC klien tidak bisa ping ke server eksternal (8.8.8.8), dan `show ip nat statistics` di CoreRouter menunjukkan `Misses` yang tinggi tanpa `Hits` atau `Translations`.
    *   **Langkah-langkah Dilakukan:**
        1.  Verifikasi konektivitas PC klien ke gateway (`RouterA`) dan dari `RouterA` ke `CoreRouter` (interface inside NAT) – Berhasil.
        2.  Verifikasi default route di `CoreRouter` (`S* 0.0.0.0/0 via 203.0.113.254`) – Benar.
        3.  Verifikasi `show ip route 0.0.0.0` di `CoreRouter`. Awalnya, output tidak secara eksplisit menyebutkan interface keluar, hanya next-hop.
        4.  Dilakukan pengecekan lebih lanjut dengan `show ip route 203.0.113.254`, yang mengkonfirmasi bahwa next-hop tersebut terjangkau via `GigabitEthernet0/0/1` (interface `outside`).
        5.  Konfigurasi `ip nat inside`, `ip nat outside`, `access-list 1`, dan `ip nat inside source list 1 interface ... overload` diperiksa ulang dan tampak benar secara sintaks.
        6.  **Solusi Ditemukan:** Setelah menyederhanakan `access-list 1` menjadi `permit any`, NAT masih tidak berfungsi. Output `show ip nat statistics` tetap menunjukkan `Misses`. Setelah diskusi dan pemeriksaan ulang yang sangat mendalam, ditemukan bahwa ada kesalahan interpretasi pada output `show ip route 0.0.0.0` sebelumnya di mana interface keluar tidak terdaftar secara eksplisit. Masalah sebenarnya (jika masih ada setelah default route dipastikan benar) kemungkinan besar adalah *kesalahan pengetikan yang sangat halus pada ACL atau perintah NAT*, atau bug pada Packet Tracer. Jika setelah Opsi 1 (menyederhanakan ACL) tidak berhasil, maka perlu debug lebih lanjut.
        *(Catatan: Jika masalah NAT sudah teratasi, jelaskan solusi spesifiknya di sini. Jika belum, jelaskan bahwa ini masih menjadi kendala yang perlu investigasi lebih lanjut atau diatasi dengan cara lain, misal, jika `show ip nat translations` tidak berfungsi, maka verifikasi NAT hanya bisa dari keberhasilan ping dan statistik `Hits`)*.
*   **Masalah 2: ACL Terlalu Restriktif Awalnya**
    *   **Deskripsi:** Setelah menerapkan ACL awal, beberapa koneksi yang seharusnya diizinkan ternyata terblokir.
    *   **Langkah-langkah Dilakukan:**
        1.  Menggunakan `show access-list [nomor_acl]` di router untuk melihat `matches` pada setiap baris ACL.
        2.  Menyadari bahwa urutan baris ACL penting, dan `deny` statement yang terlalu umum di awal bisa memblokir traffic yang seharusnya diizinkan oleh `permit` statement di bawahnya.
        3.  Perlu ditambahkan `permit ip <source_subnet> any` atau `permit ip any any` di akhir ACL (tergantung tujuan ACL) untuk mengizinkan traffic lain yang tidak secara eksplisit di-deny, karena ada `deny any any` implisit.
    *   **Solusi:** Mengatur ulang urutan baris ACL, membuat `deny` statement lebih spesifik, dan memastikan ada `permit` yang sesuai untuk traffic yang diizinkan. Menguji setiap rule ACL secara terpisah.

---

### 5. Analisis Keamanan Jaringan yang Telah Diimplementasikan

Infrastruktur jaringan PT. Nusantara Network kini telah ditingkatkan dengan beberapa lapisan keamanan dasar:

1.  **Segmentasi Jaringan dengan VLAN:**
    *   **Keuntungan:** Setiap departemen berada dalam broadcast domain terpisah. Ini membatasi dampak dari broadcast storm, meningkatkan performa, dan yang terpenting, menyediakan isolasi traffic antar departemen sebagai dasar keamanan. Komunikasi antar VLAN harus melalui router, di mana kebijakan keamanan (ACL) dapat diterapkan.
    *   **Analisis:** Implementasi VLAN 10 (IT), 20 (Finance), 30 (HR), 40 (Servers) di Gedung A, serta VLAN 50 (Marketing) dan 60 (Operations) di Gedung B telah berhasil memisahkan traffic departemen.

2.  **Access Control Lists (ACLs):**
    *   **Keuntungan:** ACL yang diterapkan pada `RouterA` dan `RouterB` memberikan kontrol granular atas traffic yang diizinkan atau ditolak antar VLAN (departemen). Ini membantu menerapkan prinsip "least privilege", di mana departemen hanya diberikan akses yang mereka butuhkan.
    *   **Analisis:**
        *   Kebijakan seperti membatasi akses Finance ke departemen Marketing/Operations telah diimplementasikan.
        *   IT diberikan akses luas untuk keperluan manajemen.
        *   Akses ke Server Farm juga dapat dikontrol lebih lanjut (meskipun contoh kita masih umum, bisa diperketat dengan ACL extended berbasis port).
        *   Penerapan ACL pada arah `in` di sub-interface efektif memfilter traffic yang berasal dari VLAN tersebut sebelum dirutekan. Penerapan ACL `out` juga digunakan untuk mengontrol akses *ke* suatu VLAN.

3.  **Network Address Translation (NAT):**
    *   **Keuntungan:** NAT Overload (PAT) pada `CoreRouter` menyembunyikan struktur alamat IP internal dari jaringan eksternal (Internet). Ini memberikan lapisan keamanan dengan membuat host internal tidak dapat dijangkau secara langsung dari Internet (kecuali ada konfigurasi port forwarding spesifik).
    *   **Analisis:** Semua departemen yang diizinkan oleh ACL 1 di `CoreRouter` dapat mengakses Internet menggunakan satu alamat IP publik, meningkatkan keamanan dan menghemat penggunaan alamat IP publik.

**Potensi Peningkatan Keamanan di Masa Depan (Tidak Termasuk dalam Proyek Ini):**

*   **Firewall Stateful:** Menerapkan firewall stateful di `CoreRouter` atau sebagai perangkat terpisah untuk inspeksi paket yang lebih canggih dan perlindungan terhadap ancaman eksternal.
*   **Intrusion Detection/Prevention System (IDS/IPS):** Untuk mendeteksi dan mencegah aktivitas mencurigakan di jaringan.
*   **Keamanan Switchport:** Implementasi fitur seperti port security, DHCP snooping, ARP inspection untuk melindungi dari serangan di Layer 2.
*   **VPN:** Untuk koneksi aman antar gedung (jika link WAN tidak aman) atau untuk akses remote karyawan.
*   **Manajemen Akses Perangkat Jaringan:** Menggunakan AAA (Authentication, Authorization, Accounting) untuk mengelola siapa yang bisa mengakses dan mengkonfigurasi router dan switch.
*   **ACL Extended yang Lebih Granular:** Membatasi akses berdasarkan port dan protokol (misalnya, Finance hanya boleh akses ke Server Akuntansi di port TCP tertentu).

**Kesimpulan Analisis Keamanan:**
Implementasi VLAN dan ACL telah secara signifikan meningkatkan postur keamanan jaringan PT. Nusantara Network dibandingkan jaringan datar tanpa segmentasi. NAT memberikan lapisan perlindungan tambahan dari ancaman eksternal. Meskipun demikian, keamanan adalah proses berkelanjutan, dan ada banyak area untuk peningkatan lebih lanjut seiring pertumbuhan dan perubahan kebutuhan perusahaan.

---