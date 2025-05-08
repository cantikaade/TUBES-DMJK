**Dokumen Desain Jaringan: Perancangan & Implementasi Jaringan Enterprise PT. Nusantara Network**

**Deliverable Pekan 11 - Mata Kuliah Desain & Manajemen Jaringan Komputer**

**Disusun oleh Kelompok [Kelompok 1]:**

1.  **Nadhilah Nur Amalina** - Network Architect
2.  **Raditya Yudianto** - Network Engineer
3.  **Irwan Maulana** - Network Services Specialist
4.  **Cantika Ade Qutnindra Maharani** - Security & Documentation Specialist

**Tanggal Pengumpulan:** Jumat, [Tanggal Pekan 10], [2025]

---

**Daftar Isi**

1.  Pendahuluan
    1.1. Latar Belakang Singkat
    1.2. Tujuan Dokumen
    1.3. Ruang Lingkup Desain
2.  Diagram Topologi Jaringan
    2.1. Diagram Topologi Fisik
    2.2. Diagram Topologi Logis (Termasuk VLAN & OSPF Areas)
    2.2.1 VLAN & OSPF
3.  Skema Pengalamatan IP (Subnetting)
    3.1. Ringkasan Alokasi Blok Alamat
    3.2. Tabel Pengalamatan IP Rinci
4.  Rencana Penerapan VLAN
    4.1. Daftar VLAN dan Tujuannya
    4.2. Strategi Trunking
    4.3. Konfigurasi VLAN dan Trunking
    4.4. Implementasi Routing Antar-VLAN
    4.5. Uji Konektivitas
5.  Daftar Kebutuhan Perangkat
6.  Kesimpulan
    6.1. Kendala
    6.2. Solusi
7.  Lampiran (Jika ada - Misal: File diagram asli)

---

**1. Pendahuluan**

**1.1. Latar Belakang Singkat**
Dokumen ini merupakan kelanjutan dari Dokumen Perencanaan Proyek (Pekan 9). Berdasarkan analisis kebutuhan PT. Nusantara Network, tahap ini fokus pada perancangan detail topologi jaringan, skema pengalamatan IP, dan rencana implementasi VLAN untuk kedua lokasi (Gedung A dan Gedung B).

**1.2. Tujuan Dokumen**
Tujuan utama dokumen ini adalah:
*   Menyajikan desain topologi fisik dan logis yang final untuk jaringan PT. Nusantara Network.
*   Mendefinisikan skema pengalamatan IP yang terstruktur dan efisien untuk seluruh jaringan, termasuk subnetting untuk setiap VLAN dan link antar router.
*   Merinci rencana implementasi VLAN, termasuk ID, nama, dan peruntukannya.
*   Mendaftar jenis dan jumlah perangkat jaringan utama yang dibutuhkan untuk merealisasikan desain ini dalam simulasi.

**1.3. Ruang Lingkup Desain**
Desain ini mencakup:
*   Infrastruktur jaringan inti (Core Router).
*   Infrastruktur jaringan di Gedung A (Router A, Access Switches, VLAN 10, 20, 30, 40).
*   Infrastruktur jaringan di Gedung B (Router B, Access Switches, VLAN 50, 60).
*   Konektivitas WAN antar Core Router, Router A, dan Router B.
*   Konektivitas ke Internet melalui ISP.
*   Pengalamatan IP untuk semua segmen jaringan dan perangkat utama.

---

**2. Diagram Topologi Jaringan**

*(gambar sketsa awal).*

**2.1. Diagram Topologi Fisik**

*   **Internet Cloud:** Merepresentasikan jaringan eksternal.
*   **ISP Router:** Terhubung ke Internet Cloud. Terhubung ke `Core Router` melalui koneksi WAN (misalnya, Ethernet atau Serial).
*   **Core Router:** Perangkat router pusat. Memiliki koneksi ke:
    *   ISP Router (Interface WAN)
    *   Router A (Interface LAN/WAN - untuk link OSPF)
    *   Router B (Interface LAN/WAN - untuk link OSPF)
*   **Gedung A:**
    *   **Router A:** Terhubung ke `Core Router` melalui link WAN/OSPF. Terhubung ke 4 Access Switch (`SW-IT`, `SW-FIN`, `SW-HR`, `SW-SRV`) melalui port Trunk GigabitEthernet.
    *   **SW-IT:** Switch L2, terhubung ke `Router A` (Trunk). Port Access terhubung ke PC/Workstation di Departemen IT (VLAN 10).
    *   **SW-FIN:** Switch L2, terhubung ke `Router A` (Trunk). Port Access terhubung ke PC/Workstation di Departemen Keuangan (VLAN 20).
    *   **SW-HR:** Switch L2, terhubung ke `Router A` (Trunk). Port Access terhubung ke PC/Workstation di Departemen SDM (VLAN 30).
    *   **SW-SRV:** Switch L2, terhubung ke `Router A` (Trunk). Port Access terhubung ke Server Farm (VLAN 40), termasuk Server DHCP/DNS.
*   **Gedung B:**
    *   **Router B:** Terhubung ke `Core Router` melalui link WAN/OSPF. Terhubung ke 2 Access Switch (`SW-MKT`, `SW-OPS`) melalui port Trunk GigabitEthernet.
    *   **SW-MKT:** Switch L2, terhubung ke `Router B` (Trunk). Port Access terhubung ke PC/Workstation di Departemen Marketing (VLAN 50).
    *   **SW-OPS:** Switch L2, terhubung ke `Router B` (Trunk). Port Access terhubung ke PC/Workstation di Departemen Operasional (VLAN 60).

**2.2. Diagram Topologi Logis (Termasuk VLAN & OSPF Areas)**

*   **OSPF Area 0 (Backbone):** Mencakup `Core Router`, `Router A`, dan `Router B` beserta link interkoneksi antar ketiganya (Subnet `192.168.99.0/24`). `Core Router` akan menjadi pusat backbone.
*   **OSPF Area 1 (Gedung A):** Mencakup semua subnet yang terhubung langsung ke `Router A` (VLAN 10, 20, 30, 40). `Router A` akan bertindak sebagai Area Border Router (ABR) antara Area 0 dan Area 1.
    *   VLAN 10: Subnet `192.168.10.0/24`
    *   VLAN 20: Subnet `192.168.11.0/24`
    *   VLAN 30: Subnet `192.168.12.0/24`
    *   VLAN 40: Subnet `192.168.13.0/24`
*   **OSPF Area 2 (Gedung B):** Mencakup semua subnet yang terhubung langsung ke `Router B` (VLAN 50, 60). `Router B` akan bertindak sebagai Area Border Router (ABR) antara Area 0 dan Area 2.
    *   VLAN 50: Subnet `192.168.20.0/24`
    *   VLAN 60: Subnet `192.168.21.0/24`
*   **NAT Boundary:** Terletak di `Core Router`, mentranslasikan alamat dari semua subnet internal (`192.168.10.0/24` - `192.168.21.0/24`) ke alamat IP publik pada interface WAN (`203.0.113.1`).
*   **Inter-VLAN Routing:** Dilakukan oleh `Router A` untuk VLAN di Gedung A dan oleh `Router B` untuk VLAN di Gedung B (menggunakan sub-interface pada koneksi trunk ke switch).
*   **DHCP/DNS Server:** Berlokasi di VLAN 40 (Server Farm) dengan alamat IP statis (`192.168.13.10` untuk DHCP/DNS). `Router A` dan `Router B` akan dikonfigurasi sebagai DHCP Relay Agent (ip helper-address) untuk meneruskan request DHCP dari VLAN lain ke server ini.

**2.3. Strategi Redudansi Jaringan**
*   **Redudansi Jaringan Inti***
    *   Menggunakan OSPF Multi-Area, jika salah satu link antara `Core Router` dan `Router A/B` gagal, routing otomatis akan menemukan jalur alternatif. 
*   **Redudansi Pada VLAN***
    *   VLAN sengaja dibuat terpisah untuk setiap departemen sehingga jika satu VLAN mengalami masalah, tidak akan memengaruhi VLAN lainnya. 
*   **Back-Up Perangkat***
    *   Pada Server Farm di `VLAN 40`, perlu disiapkan server cadangan untuk DHCP/DNS.


**2.4. Desain Diagram Topologi Fisik**

- Keseluruhan Topologi Jaringan 

![Image](https://github.com/user-attachments/assets/61e5d3e0-f964-4cca-93b5-65ce406f8e5e)

- Gedung  A

![Image](https://github.com/user-attachments/assets/7c394d6e-aad0-4a0a-b534-df65a5a90bdf)

- Gedung B

![Image](https://github.com/user-attachments/assets/62fa3463-dc56-471f-b95e-342b24ce37d9)

- WAN

![Image](https://github.com/user-attachments/assets/87a24e91-07c6-4e4b-b720-73802d9fff21)



---

**3. Skema Pengalamatan IP (Subnetting)**

**3.1. Ringkasan Alokasi Blok Alamat**
*   **Gedung A (Kantor Pusat):** Menggunakan blok `192.168.10.0` - `192.168.13.255` (Prefix `/22` jika digabungkan, tapi dipecah per /24 untuk setiap VLAN).
*   **Gedung B (Kantor Cabang):** Menggunakan blok `192.168.20.0` - `192.168.21.255` (Prefix `/23` jika digabungkan, tapi dipecah per /24 untuk setiap VLAN).
*   **Interkoneksi Router (Backbone):** Menggunakan blok `192.168.99.0/24`.
*   **Koneksi ke ISP:** Menggunakan alamat yang diberikan ISP.

**3.2. Tabel Pengalamatan IP Rinci**

| Lokasi   | Tujuan / VLAN Name | VLAN ID | Network Address | Prefix Length | Subnet Mask   | Gateway Address (Router Interface) | Usable IP Range (DHCP/Static) | Broadcast Address | Notes                                           |
| :------- | :----------------- | :------ | :-------------- | :------------ | :------------ | :--------------------------------- | :---------------------------- | :---------------- | :---------------------------------------------- |
| Gedung A | IT Department      | 10      | 192.168.10.0    | /24           | 255.255.255.0 | 192.168.10.1 (Router A Gi0/0.10) | 192.168.10.2 - 192.168.10.254 | 192.168.10.255    | 40 Komputer + Buffer                            |
| Gedung A | Finance Department | 20      | 192.168.11.0    | /24           | 255.255.255.0 | 192.168.11.1 (Router A Gi0/0.20) | 192.168.11.2 - 192.168.11.254 | 192.168.11.255    | 25 Komputer + Buffer                            |
| Gedung A | HR Department      | 30      | 192.168.12.0    | /24           | 255.255.255.0 | 192.168.12.1 (Router A Gi0/0.30) | 192.168.12.2 - 192.168.12.254 | 192.168.12.255    | 20 Komputer + Buffer                            |
| Gedung A | Server Farm        | 40      | 192.168.13.0    | /24           | 255.255.255.0 | 192.168.13.1 (Router A Gi0/0.40) | 192.168.13.2 - 192.168.13.254 | 192.168.13.255    | 10 Server + Buffer. DHCP/DNS: 192.168.13.10     |
| Gedung B | Marketing Dept     | 50      | 192.168.20.0    | /24           | 255.255.255.0 | 192.168.20.1 (Router B Gi0/0.50) | 192.168.20.2 - 192.168.20.254 | 192.168.20.255    | 30 Komputer + Buffer                            |
| Gedung B | Operations Dept    | 60      | 192.168.21.0    | /24           | 255.255.255.0 | 192.168.21.1 (Router B Gi0/0.60) | 192.168.21.2 - 192.168.21.254 | 192.168.21.255    | 35 Komputer + Buffer                            |
| Backbone | Core <-> Rtr A/B   | N/A     | 192.168.99.0    | /24           | 255.255.255.0 | N/A                                | N/A                           | 192.168.99.255    | OSPF Area 0. Core: .1, Rtr A: .2, Rtr B: .3 |

*Catatan: Gateway address diasumsikan menggunakan sub-interface pada Router A/B (misal Gi0/0.10 untuk VLAN 10).*

---

**4. Rencana Penerapan VLAN**

**4.1. Daftar VLAN dan Tujuannya**

| VLAN ID | VLAN Name        | Lokasi   | Departemen/Tujuan      | Subnet IP Assigned | Default Gateway |
| :------ | :--------------- | :------- | :--------------------- | :----------------- | :-------------- |
| 10      | IT_Dept          | Gedung A | Departemen IT          | 192.168.10.0/24    | 192.168.10.1    |
| 20      | Finance_Dept     | Gedung A | Departemen Keuangan    | 192.168.11.0/24    | 192.168.11.1    |
| 30      | HR_Dept          | Gedung A | Departemen SDM         | 192.168.12.0/24    | 192.168.12.1    |
| 40      | Server_Farm      | Gedung A | Server Internal        | 192.168.13.0/24    | 192.168.13.1    |
| 50      | Marketing_Dept   | Gedung B | Departemen Marketing   | 192.168.20.0/24    | 192.168.20.1    |
| 60      | Operations_Dept  | Gedung B | Departemen Operasional | 192.168.21.0/24    | 192.168.21.1    |
| *99*    | *(Management)*   | *All*    | *(Optional: Mgmt)*     | *(Optional)*       | *(Optional)*    |

*Catatan: VLAN 99 dapat ditambahkan jika manajemen perangkat jaringan terpisah diinginkan.*

**4.2. Strategi Trunking**
*   Koneksi antara Router A dan setiap Access Switch di Gedung A (`SW-IT`, `SW-FIN`, `SW-HR`, `SW-SRV`) akan dikonfigurasi sebagai Trunk (IEEE 802.1Q) untuk membawa traffic dari VLAN 10, 20, 30, dan 40.
*   Koneksi antara Router B dan setiap Access Switch di Gedung B (`SW-MKT`, `SW-OPS`) akan dikonfigurasi sebagai Trunk (IEEE 802.1Q) untuk membawa traffic dari VLAN 50 dan 60.
*   Native VLAN pada trunk sebaiknya tidak menggunakan VLAN 1 (misalnya, menggunakan VLAN yang tidak terpakai seperti 999).
*   Hanya VLAN yang diperlukan yang akan diizinkan melintasi trunk (VLAN pruning).

**4.3. Konfigurasi VLAN dan trunking**
```markdown
### Konfigurasi VLAN Gedung A ke dalam *Main Switch* (Penamaan dan Segmentasi Lalu Lintas Jaringan)
```

```bash
# VLAN Creation
Switch(config)# vlan 10
Switch(config-vlan)# name IT
Switch(config-vlan)# exit
# Create other VLANs potentially passing through trunk
Switch(config)# vlan 20
Switch(config-vlan)# name Finance
Switch(config-vlan)# exit
Switch(config)# vlan 30
Switch(config-vlan)# name HR
Switch(config-vlan)# exit
Switch(config)# vlan 40
Switch(config-vlan)# name Server
Switch(config-vlan)# exit
```
```markdown
### Konfigurasi VLAN Gedung B ke dalam *Main Switch* (Penamaan dan Segmentasi Lalu Lintas Jaringan)
```

```bash
Switch(config)# vlan 50
Switch(config-vlan)# name Marketing
Switch(config-vlan)# exit
Switch(config)# vlan 60
Switch(config-vlan)# name Operations
Switch(config-vlan)# exit
```

```markdown
### Mengatur *Port Switch* untuk VLAN & Trunking Setiap
```

```bash
interface FastEthernet0/1
 description IT_1
 switchport mode access
 switchport access vlan 10
 no shutdown
 exit

interface FastEthernet0/2
 description IT_2
 switchport mode access
 switchport access vlan 10
 no shutdown
 exit

interface FastEthernet0/3
 description Keuangan_1
 switchport mode access
 switchport access vlan 20
 no shutdown
 exit

interface FastEthernet0/4
 description Keuangan_2
 switchport mode access
 switchport access vlan 20
 no shutdown
 exit

interface FastEthernet0/5
 description SDM
 switchport mode access
 switchport access vlan 30
 no shutdown
 exit

interface FastEthernet0/6
 description Router_A
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40
 no shutdown
 exit

interface FastEthernet0/7
 description Server
 switchport mode access
 switchport access vlan 40
 no shutdown
 exit
 ```

**4.4. Implementasi routing antar-VLAN**

```markdown
### Mengatur Mode Trunk untuk Setiap Port di Gedung A yang Terhubung Pada Router/Switch Lain
```

```bash
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# switchport trunk encapsulation dot1q
Switch(config-if)# switchport mode trunk
Switch(config-if)# no shutdown
Switch(config-if)# exit

Router(config)# interface GigabitEthernet0/0/0.10
Router(config-subif)# description Gateway_VLAN10_IT
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
Router(config-subif)# no shutdown
Router(config-subif)# exit

Router(config)# interface GigabitEthernet0/0/0.20
Router(config-subif)# description Gateway_VLAN20_Finance
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.11.1 255.255.255.0
Router(config-subif)# no shutdown
Router(config-subif)# exit

Router(config)# interface GigabitEthernet0/0/0.30
Router(config-subif)# description Gateway_VLAN30_HR
Router(config-subif)# encapsulation dot1Q 30
Router(config-subif)# ip address 192.168.12.1 255.255.255.0
Router(config-subif)# no shutdown
Router(config-subif)# exit

Router(config)# interface GigabitEthernet0/0/0.40
Router(config-subif)# description Gateway_VLAN40_Servers
Router(config-subif)# encapsulation dot1Q 40
Router(config-subif)# ip address 192.168.13.1 255.255.255.0
Router(config-subif)# no shutdown
Router(config-subif)# exit

Router(config)# interface GigabitEthernet0/0/0
Router(config-if)# description Router_A
Router(config-if)# no shutdown
Router(config-if)# exit
```

```markdown
### Mengatur Mode Trunk untuk Setiap Port di Gedung B yang Terhubung Pada Router/Switch Lain
```

```bash
interface GigabitEthernet0/0/0.50
 description Gateway_VLAN50_Marketing
 encapsulation dot1Q 50
 ip address 192.168.20.1 255.255.255.0
 no shutdown
 exit

interface GigabitEthernet0/0/0.60
 description Gateway_VLAN60_Operational
 encapsulation dot1Q 60
 ip address 192.168.21.1 255.255.255.0
 no shutdown
 exit

Router(config)# interface GigabitEthernet0/0/0
Router(config-if)# description Router_B
Router(config-if)# no shutdown
Router(config-if)# exit
```

**4.5. Uji Konektivitas Antar-VLAN**
```markdown
### Konfirmasi Awal Menggunakan CLI Pada Switch
```

``bash
show vlan brief
show interfaces trunk
```

``markdown
### Konfirmasi Awal Menggunakan CLI Pada Router
```

![Image](https://github.com/user-attachments/assets/9e5a18bf-e430-4ea3-b55f-e4db901b063b)


```bash
show running-config
show ip interface brief

```
![Image](https://github.com/user-attachments/assets/c3405d85-323d-4c0f-867b-c09f4ae7993a)


---

**5. Daftar Kebutuhan Perangkat (Untuk Simulasi)**

| Tipe Perangkat         | Model (Contoh Packet Tracer/GNS3) | Jumlah | Lokasi/Peran Utama                                                                 | Keterangan                                      |
| :--------------------- | :-------------------------------- | :----- | :--------------------------------------------------------------------------------- | :---------------------------------------------- |
| Router                 | Cisco 1941/2911 atau Generic      | 1      | Core Router (NAT, OSPF Area 0, Gateway Internet)                                   | Membutuhkan min. 3 interface (ISP, Rtr A, Rtr B) |
| Router                 | Cisco 1941/2911 atau Generic      | 1      | Router A (Gedung A - Inter-VLAN, OSPF ABR Area 1, DHCP Relay)                      | Membutuhkan min. 2 interface (Core, Switches)   |
| Router                 | Cisco 1941/2911 atau Generic      | 1      | Router B (Gedung B - Inter-VLAN, OSPF ABR Area 2, DHCP Relay)                      | Membutuhkan min. 2 interface (Core, Switches)   |
| Switch (Layer 2)       | Cisco 2960 atau Generic           | 4      | Gedung A Access Switches (SW-IT, SW-FIN, SW-HR, SW-SRV)                             | Mendukung VLAN, Trunking                        |
| Switch (Layer 2)       | Cisco 2960 atau Generic           | 2      | Gedung B Access Switches (SW-MKT, SW-OPS)                                          | Mendukung VLAN, Trunking                        |
| Server                 | Generic Server                    | 1+     | Server Farm (VLAN 40) - Minimal 1 untuk DHCP & DNS                               | IP Statis 192.168.13.10                         |
| PC/Workstation         | Generic PC                        | 150 | Representasi klien di setiap VLAN (IT, FIN, HR, MKT, OPS)                          | Untuk pengujian konektivitas & layanan         |
| ISP Router (Simulasi)  | Generic Router                    | 1      | Simulasi gateway ISP                                                               | Hanya untuk menyediakan konektivitas WAN        |

*Catatan: Jumlah PC/Workstation hanya representatif untuk keperluan simulasi dan pengujian, tidak sesuai jumlah riil.*

---

**6. Kesimpulan**

Dokumen ini telah memfinalisasi desain topologi fisik dan logis untuk jaringan PT. Nusantara Network, menetapkan skema pengalamatan IP yang terstruktur menggunakan subnetting /24 untuk setiap VLAN, dan merinci rencana implementasi VLAN. Daftar perangkat yang dibutuhkan untuk simulasi juga telah diidentifikasi. Desain ini menjadi landasan konkret untuk memulai implementasi konfigurasi pada perangkat jaringan (Router dan Switch) di Pekan 11 dan seterusnya. Penggunaan OSPF multi-area dan penempatan layanan sentral (DHCP/DNS) di Server Farm diharapkan memenuhi kebutuhan skalabilitas, manajemen, dan fungsionalitas perusahaan.

**6.1. Kendala**
*   Pada minggu  ke 11 dari perancangan Tugas Besar DMJK Studi Kasus Perancangan & Implementasi Jaringan Enterprise PT. Nusantara Network, di mana pada pengisian IP Address untuk setiap PC di tiap departemen gedung belum terisi sepenuhnya menyebabkan uji konektivitas dilakukan secara otomatis menggunakan fitur yang ada pada Cisco Packet Tracker.

**6.2. Solusi**
*   Untuk memudahkan dalam pengisian IP address pada setiap PC, akan digunakan konfigurasi DHCP untuk setiap router-nya agar IP dapat terisi secara otomatis.


---

**7. Lampiran**

*  [ (Link ke file pkt).](https://github.com/cantikaade/TUBES-DMJK/commit/9db1e8e691c4326dc6603edcdfe2fba3ef893224)

---