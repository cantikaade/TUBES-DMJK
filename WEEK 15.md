**Dokumen Desain Jaringan: Perancangan & Implementasi Jaringan Enterprise PT. Nusantara Network**

**Deliverable Pekan 15 - Mata Kuliah Desain & Manajemen Jaringan Komputer**

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
6.  Implementeasi Routing Dan WAN
    6.1. Konfigurasi Routing Statis Intra-Gedung
    6.2. Routing Dinamis (OSPF) Antar Gedung
    6.3. Koneksi WAN Antar Gedung
7. Implementasi Layanan Jaringan
    7.1. Konfigurasi DHCP Server untuk setiap departemen
        7.1.1. Konfigurasi DHCP Server Pada Gedung A
        7.1.2. Konfigurasi DHCP Server Pada Gedung B
    7.2. Implementasi DNS Server untuk resolusi nama internal
    7.3. Konfigurasi NAT untuk akses internet
        7.3.1. Konfigurasi Interface NAT
        7.3.2. Pembuatan Access List Untuk NAT
        7.3.3. Konfigurasi NAT Dinamis & Statis
        7.3.4. Pengujian Konfigurasi NAT
8. Implementasi Keamanan & Pengujian
    8.1. Dokumentasi Konfigurasi CLI Sesuai Kebijakan Keamanan
        8.1.1. Kebijakan Keamanan
        8.1.2. Implementasi ACL
    8.2. Matriks Pengujian
    8.3. Troubleshooting    
        8.3.1. Kegagalan NAT Awal (Week  13)
        8.3.2. ACL Restriktif
    8.4. Analisis Keamanan Jaringan yang Telah Diimplementasikan
9.  Kesimpulan
    9.1. Kendala
    9.2. Solusi
    9.3. Catatan
10.  Lampiran (Jika ada - Misal: File diagram asli)

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
**6. Implementeasi Routing Dan WAN**

**6.1. Konfigurasi Routing Statis Intra-Gedung**

![Image](https://github.com/user-attachments/assets/778660d3-39f2-4baf-9a65-d66bac7ffee1)

![Image](https://github.com/user-attachments/assets/2c2f69f6-63cf-418b-b94f-3e1ab3cbb324)

Pada kedua gambar di atas, merupakann proses konfigurasi secara statis pada `core router`, pada proses ini, konfigurasi antar rute dilakukan secara manual dengan menuliskan IP route yang akan dituju. Pada gambar pertama, rute difokuskan kepada IP 192.168.98.1 sebagai next-hop. Sedangkan gambar kedua fokus untuk menuju IP 192.168.99.1 sebagai next-hop-nya.

**6.2. Routing Dinamis (OSPF) Antar Gedung**

```markdown
### Konfigurasi OSPF Pada Core Router
```

```bash
ROUTER CORE  

! Konfigurasi OSPF
RouterCore(config)# router ospf 1
RouterCore(config-router)# router-id 1.1.1.1
RouterCore(config-router)# log-adjacency-changes
RouterCore(config-router)# network 192.168.99.0 0.0.0.255 area 0
RouterCore(config-router)# network 192.168.98.0 0.0.0.255 area 0
RouterCore(config-router)# exit

RouterCore(config)# end
RouterCore# write memory
```

```markdown
### Konfigurasi OSPF Pada Router Gedung A
```

```bash
! Konfigurasi OSPF
RouterA(config)# router ospf 1
RouterA(config-router)# router-id 2.2.2.2
RouterA(config-router)# log-adjacency-changes
RouterA(config-router)# network 192.168.99.0 0.0.0.255 area 0
! Area 1 untuk jaringan internal Gedung A
RouterA(config-router)# network 192.168.10.0 0.0.0.255 area 1
RouterA(config-router)# network 192.168.11.0 0.0.0.255 area 1
RouterA(config-router)# network 192.168.12.0 0.0.0.255 area 1
RouterA(config-router)# network 192.168.13.0 0.0.0.255 area 1
RouterA(config-router)# exit

RouterA(config)# end
RouterA# write memory
```

```markdown
### Konfigurasi OSPF Pada Router Gedung B
```

```bash
! Konfigurasi OSPF
RouterB(config)# router ospf 1
RouterB(config-router)# router-id 3.3.3.3
RouterB(config-router)# log-adjacency-changes
RouterB(config-router)# network 192.168.98.0 0.0.0.255 area 0
! Area 2 untuk jaringan internal Gedung B
RouterB(config-router)# network 192.168.20.0 0.0.0.255 area 2
RouterB(config-router)# network 192.168.21.0 0.0.0.255 area 2
RouterB(config-router)# exit

RouterB(config)# end
RouterB# write memory
```

```markdown
### Show IP Route Pada Core Router
```

![Image](https://github.com/user-attachments/assets/29a2a8f8-e05f-49ce-8210-ee78af13cb61)


```markdown
### Show IP Route Pada Gedung A
```

![Image](https://github.com/user-attachments/assets/a2510dcf-5bc5-4c48-819d-be8180d3c7bd)



```markdown
### Show IP Route Pada Gedung B
```

![Image](https://github.com/user-attachments/assets/33231033-79ca-4c29-8f95-707a713c4b5a)

**6.3. Koneksi WAN Antar Gedung**

```markdown
### Konfigurasi Interface WAN Ke Gedung A Pada Core router
```

```bash
! Konfigurasi Interface WAN ke Gedung A
RouterCore(config)# interface GigabitEthernet0/0/0
RouterCore(config-if)# description Link_to_GedungA
RouterCore(config-if)# ip address 192.168.99.1 255.255.255.0
RouterCore(config-if)# no shutdown
RouterCore(config-if)# exit
```

```markdown
### Konfigurasi Interface WAN Ke Gedung B Pada Core router
```

```bash
! Konfigurasi Interface WAN ke Gedung B
RouterCore(config)# interface GigabitEthernet0/0/1
RouterCore(config-if)# description Link_to_GedungB
RouterCore(config-if)# ip address 192.168.98.1 255.255.255.0
RouterCore(config-if)# no shutdown
RouterCore(config-if)# exit
```

```markdown
### Konfigurasi Interface WAN Ke Core router Pada Gedung A
```

```bash
! Konfigurasi Interface WAN ke Router Core
RouterA(config)# interface GigabitEthernet0/0/1
RouterA(config-if)# description Link_to_RouterCore
RouterA(config-if)# ip address 192.168.99.2 255.255.255.0
RouterA(config-if)# no shutdown
RouterA(config-if)# exit
```


```markdown
### Konfigurasi Interface WAN Ke Core router Pada Gedung B
```

```bash
! Konfigurasi Interface WAN ke Router Core
RouterB(config)# interface GigabitEthernet0/0/1
RouterB(config-if)# description Link_to_RouterCore
RouterB(config-if)# ip address 192.168.98.2 255.255.255.0
RouterB(config-if)# no shutdown
RouterB(config-if)# exit

```

```markdown
### Uji Konektivitas Antar Gedung
```

![Image](https://github.com/user-attachments/assets/e5174945-264c-44ed-8169-6b95f6b89f27)

---
Berdasarkan percobaan uji konektivitas antar gedung menggunakan routing statis dan dinamis (OSPF) yang telah dilakukan, dapat diambil kesimpulan bahwasannya kedua routing tersebut sama-sama memiliki keunggulan dan kekurangan. Yang mana Meliputi:

**Routing Statis**
Memili keunggulan saat menerapkan kegunaannya pada jaringan kecil/sederhana, routing ini tidak mememerlukan proses tambahan dari  router, hingga membantu dalam menghemat sumberdaya, routing statis juga tidak bergantung pada protokol routing yang dapat berubah.

Selain keunggulan tersebut, terdapat beberapa kelemahan yang berupa kurangnya flesibelitas, di mana administrator harus memperbarui routing secara manual apabila terjadi perubahan pada jaringan, serta kendala saat banyak jaringan yang perlu dikonfigurasi, menyebabkan tingkat kompleksitas dan tata kelola sulit dilakukan.

**Routing Dinamis menggunakan OSPF**
Memiliki keterbalikan dari statis, routing secara dinamis sangat adaprif, di saat slaah satu jalur gagal, OSPF secara otomatis akan mencari alternatif terbaik. Lalu dengan routing ini, efisiensi jalur semakin terjamin, tidak hanya berdasarkan next-hop, akan tetapu biaya link, dengan kecocokan penggunaan terkhusus pada jaringan yang besar dengan banyak subnet.

Akan tetapi, routing ini juga memiliki kekurangan, yang berupa kompleksitas yang lebih kebanding routing statis dan memerlukn pemrosesan tambahan dari router, dikarenakan setiap router harus menghitung jalur terbaik.

---

**7.1. Konfigurasi DHCP Server Untuk Setiap Departemen.**

**7.1.1. Konfigurasi DHCP Server Pada Gedung A**

Pada proses konfigurasi kali ini, dilakukan konfigurasi untuk 4 VLAN, meliputi VLAN 10, 20, 30, dan 40 yang ada pada router gedung A dan 2 VLAN, meliputi VLAN 50 dan 60 yang ada pada router gedung B.


```markdown
### Mengatur konfigurasi Gedung A
```

```bash
! Enter global configuration mode
Router> enable
Router# configure terminal

! Create DHCP excluded addresses (typically used for servers, printers, etc.)
Router(config)# ip dhcp excluded-address 10.0.10.1 10.0.10.10
Router(config)# ip dhcp excluded-address 10.0.11.1 10.0.11.10
Router(config)# ip dhcp excluded-address 10.0.12.1 10.0.12.10
Router(config)# ip dhcp excluded-address 10.0.13.1 10.0.13.10

! Configure DHCP pool for VLAN 10
Router(config)# ip dhcp pool VLAN10-POOL
Router(dhcp-config)# network 10.0.10.0 255.255.255.0
Router(dhcp-config)# default-router 10.0.10.1
Router(dhcp-config)# domain-name gedung-a.local
Router(dhcp-config)# lease 7
Router(dhcp-config)# exit

! Configure DHCP pool for VLAN 20
Router(config)# ip dhcp pool VLAN20-POOL
Router(dhcp-config)# network 10.0.11.0 255.255.255.0
Router(dhcp-config)# default-router 10.0.11.1
Router(dhcp-config)# dns-server 8.8.8.8 8.8.4.4
Router(dhcp-config)# domain-name gedung-a.local
Router(dhcp-config)# lease 7
Router(dhcp-config)# exit

! Configure DHCP pool for VLAN 30
Router(config)# ip dhcp pool VLAN30-POOL
Router(dhcp-config)# network 10.0.12.0 255.255.255.0
Router(dhcp-config)# default-router 10.0.12.1
Router(dhcp-config)# dns-server 8.8.8.8 8.8.4.4
Router(dhcp-config)# domain-name gedung-a.local
Router(dhcp-config)# lease 7
Router(dhcp-config)# exit

! Configure DHCP pool for VLAN 40
Router(config)# ip dhcp pool VLAN40-POOL
Router(dhcp-config)# network 10.0.13.0 255.255.255.0
Router(dhcp-config)# default-router 10.0.13.1
Router(dhcp-config)# dns-server 8.8.8.8 8.8.4.4
Router(dhcp-config)# domain-name gedung-a.local
Router(dhcp-config)# lease 7
Router(dhcp-config)# exit

! Save configuration
Router(config)# end
Router# write memory
```

**7.1.2. Konfigurasi DHCP Server Pada Gedung B**

```markdown
### Mengatur konfigurasi Gedung B
```

```bash
! Enter global configuration mode
Router> enable
Router# configure terminal

! Create DHCP excluded addresses
Router(config)# ip dhcp excluded-address 10.0.20.1 10.0.20.10
Router(config)# ip dhcp excluded-address 10.0.21.1 10.0.21.10

! Configure DHCP pool for VLAN 50
Router(config)# ip dhcp pool VLAN50-POOL
Router(dhcp-config)# network 10.0.20.0 255.255.255.0
Router(dhcp-config)# default-router 10.0.20.1
Router(dhcp-config)# dns-server 8.8.8.8 8.8.4.4
Router(dhcp-config)# domain-name gedung-b.local
Router(dhcp-config)# lease 7
Router(dhcp-config)# exit

! Configure DHCP pool for VLAN 60
Router(config)# ip dhcp pool VLAN60-POOL
Router(dhcp-config)# network 10.0.21.0 255.255.255.0
Router(dhcp-config)# default-router 10.0.21.1
Router(dhcp-config)# dns-server 8.8.8.8 8.8.4.4
Router(dhcp-config)# domain-name gedung-b.local
Router(dhcp-config)# lease 7
Router(dhcp-config)# exit

! Save configuration
Router(config)# end
Router# write memory
```

---

**7.2. Implementasi DNS Server untuk resolusi nama internal**
Konfigurasi DNS internal dapat dilakukan pada router atau server DNS. Pada contoh ini, konfigurasi DNS biasanya mengaktifkan resolusi nama dan mengarahkan ke server DNS internal. Berdasarkan hasil konfirgurasi DHCP di atas, dapat disimpulkan bahwa
- Alamat IP Dinamis sesuai dengan rentang yang didefinisikan pada masing-masing DHCP pool.
- Default Gateway, yang mengarahkan trafik keluar dari subnet.
- DNS Server, yaitu Google Public DNS (8.8.8.8 dan 8.8.4.4). Jika diperlukan resolusi internal, sebaiknya gunakan alamat DNS internal.
- Domain Name, yaitu gedung-a.local bagi client di Gedung A dan gedung-b.local bagi client di Gedung B.

Dengan konfigurasi ini, client tidak hanya mendapatkan alamat IP, tetapi juga informasi domain yang memudahkan mereka dalam mengakses sumber daya internal menggunakan nama daripada alamat IP. Ini sangat berguna dalam lingkungan jaringan dengan pengelolaan nama domain internal yang terstruktur.



---

**7.3. Konfigurasi NAT untuk akses internet**

**7.3.1. Konfigurasi Interface NAT**
Pertama, pada router kantor yang menghubungkan jaringan internal (Gedung A dan Gedung B) ke internet, ditentukan interface mana yang akan dianggap sebagai sisi inside (jaringan internal) dan sisi outside (jaringan eksternal).

Ketika client di masing-masing gedung memperoleh konfigurasi melalui DHCP, mereka akan mendapatkan:

- Alamat IP Dinamis sesuai dengan rentang yang didefinisikan pada masing-masing DHCP pool.
- Default Gateway, yang mengarahkan trafik keluar dari subnet.
- DNS Server, yaitu Google Public DNS (8.8.8.8 dan 8.8.4.4). Jika diperlukan resolusi internal, sebaiknya gunakan alamat DNS internal.
- Domain Name, yaitu gedung-a.local bagi client di Gedung A dan gedung-b.local bagi client di Gedung B.

Dengan konfigurasi ini, client tidak hanya mendapatkan alamat IP, tetapi juga informasi domain yang memudahkan mereka dalam mengakses sumber daya internal menggunakan nama daripada alamat IP. Ini sangat berguna dalam lingkungan jaringan dengan pengelolaan nama domain internal yang terstruktur.


```markdown
Interface NAT Inside
```
```bash
Router(config)# interface g0/0/0
Router(config-if)# ip address 192.168.99.1 255.255.255.0
Router(config-if)# ip nat inside
```
Perintah ini menandai interface tersebut sebagai bagian dari jaringan internal yang akan diterjemahkan (NAT).

```markdown
Interface NAT Outside
```
```bash
Router(config)# interface g0/0/1
Router(config-if)# ip address 203.0.113.1 255.255.255.0
Router(config-if)# ip nat outside
```
Interface ini dianggap berada di sisi luar (innternet) dan akan menerima trakif NAT dari jaringan internal

**7.3.2. Pembuatan Access List Untuk NAT**

Hal ini dilakukan agar router hanya menerjemahkan alamat IP dari jaringan internal yang diijinkan, pada kasus kali ini, `Access List` yang menyatakan jaringan mana yang akan menggunakan NAT.

```bash
Router(config)# access-list 1 permit 192.168.10.0 0.0.0.255
Router(config)# access-list 1 permit 192.168.11.0 0.0.0.255
Router(config)# access-list 1 permit 192.168.12.0 0.0.0.255
Router(config)# access-list 1 permit 192.168.13.0 0.0.0.255
Router(config)# access-list 1 permit 192.168.20.0 0.0.0.255
Router(config)# access-list 1 permit 192.168.21.0 0.0.0.255
```

**7.3.3. Konfigurasi NAT Dinamis & Statis**
Untuk memungkinkan semua host di jaringan internal berbagi satu alamat IP publik saat melakukan koneksi ke Internet, digunakan NAT dinamis dengan fitur overload (sering disebut Port Address Translation/PAT).Selain NAT dinamis, kadang diperlukan NAT statis untuk memungkinkan akses langsung ke server internal dari luar.

```bash
Router(config)# ip nat inside source list 1 interface g0/0/1 overload
Router(config)# ip nat inside source static 192.168.13.10 203.0.113.10
Router(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.254
Router(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.1
```
Dengan konfigurasi dinamis, setiap host internal yang melakukan koneksi ke internet akan diterjemahkan ke alamat IP 203.0.113.1, dengan penambahan nomor port sebagai identifikasi sesi masing-masing. Sedangkan konfigurasi statis memungkinkan klien dari internet mengakses server internal melalui 203.0.113.10, sementara trafik masuk secara otomatis ditranslasikan ke alamat internal 192.168.13.10. Lalu terdapat Routing Default agar trafik yang tidak memiliki rute spesifik (misalnya, trafik ke internet) dapat diarahkan ke router yang menghadap ke internet.

**7.3.4. Pengujian Konfigurasi NAT**

![Image](https://github.com/user-attachments/assets/1e1f5568-ebb7-438e-b521-08de3e5ca1c6)
- Akses ke Server Internal dari Luar: PC di jaringan luar melakukan ping ke 203.0.113.10, di mana trafik ini secara resmi ditranslasikan ke alamat internal 192.168.13.10.

![Image](https://github.com/user-attachments/assets/c5746d42-d633-446e-adf3-01a2698102d7)

- Ping dari Jaringan Internal ke IP NAT (203.0.113.1): Client di Gedung B melakukan ping ke alamat 203.0.113.1 dan menerima balasan yang cepat.

![Image](https://github.com/user-attachments/assets/1100b22e-a8e1-4e23-be7d-2a41b4bb9c42)

- Verifikasi dengan show ip nat translations: Perintah ini digunakan untuk memastikan bahwa alamat internal telah diterjemahkan sesuai aturan NAT yang ditetapkan

---
**8. Implementasi Keamanan & Pengujian**

**8.1. Dokumentasi Konfigurasi CLI Sesuai Kebijakan Keamanan**
**8.1.1. Kebijakan Keamanan**
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

**8.1.2. Implementasi ACL**
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

**8.2. Matriks Pengujian**
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

**8.3. Troubleshooting**

**8.3.1. Kegagalan NAT Awal (Week  13)**
   *   **Deskripsi:** PC klien tidak bisa ping ke server eksternal (8.8.8.8), dan `show ip nat statistics` di CoreRouter menunjukkan `Misses` yang tinggi tanpa `Hits` atau `Translations`.
    *   **Langkah-langkah Dilakukan:**
        1.  Verifikasi konektivitas PC klien ke gateway (`RouterA`) dan dari `RouterA` ke `CoreRouter` (interface inside NAT) – Berhasil.
        2.  Verifikasi default route di `CoreRouter` (`S* 0.0.0.0/0 via 203.0.113.254`) – Benar.
        3.  Verifikasi `show ip route 0.0.0.0` di `CoreRouter`. Awalnya, output tidak secara eksplisit menyebutkan interface keluar, hanya next-hop.
        4.  Dilakukan pengecekan lebih lanjut dengan `show ip route 203.0.113.254`, yang mengkonfirmasi bahwa next-hop tersebut terjangkau via `GigabitEthernet0/0/1` (interface `outside`).
        5.  Konfigurasi `ip nat inside`, `ip nat outside`, `access-list 1`, dan `ip nat inside source list 1 interface ... overload` diperiksa ulang dan tampak benar secara sintaks.
        6.  **Solusi Ditemukan:** Setelah menyederhanakan `access-list 1` menjadi `permit any`, NAT masih tidak berfungsi. Output `show ip nat statistics` tetap menunjukkan `Misses`. Setelah diskusi dan pemeriksaan ulang yang sangat mendalam, ditemukan bahwa ada kesalahan interpretasi pada output `show ip route 0.0.0.0` sebelumnya di mana interface keluar tidak terdaftar secara eksplisit. Masalah sebenarnya (jika masih ada setelah default route dipastikan benar) kemungkinan besar adalah *kesalahan pengetikan yang sangat halus pada ACL atau perintah NAT*, atau bug pada Packet Tracer. Jika setelah Opsi 1 (menyederhanakan ACL) tidak berhasil, maka perlu debug lebih lanjut.
        *(Catatan: Jika masalah NAT sudah teratasi, jelaskan solusi spesifiknya di sini. Jika belum, jelaskan bahwa ini masih menjadi kendala yang perlu investigasi lebih lanjut atau diatasi dengan cara lain, misal, jika `show ip nat translations` tidak berfungsi, maka verifikasi NAT hanya bisa dari keberhasilan ping dan statistik `Hits`)*.

**8.3.2. ACL Restriktif**
*   **Deskripsi:** Setelah menerapkan ACL awal, beberapa koneksi yang seharusnya diizinkan ternyata terblokir.
    *   **Langkah-langkah Dilakukan:**
        1.  Menggunakan `show access-list [nomor_acl]` di router untuk melihat `matches` pada setiap baris ACL.
        2.  Menyadari bahwa urutan baris ACL penting, dan `deny` statement yang terlalu umum di awal bisa memblokir traffic yang seharusnya diizinkan oleh `permit` statement di bawahnya.
        3.  Perlu ditambahkan `permit ip <source_subnet> any` atau `permit ip any any` di akhir ACL (tergantung tujuan ACL) untuk mengizinkan traffic lain yang tidak secara eksplisit di-deny, karena ada `deny any any` implisit.
    *   **Solusi:** Mengatur ulang urutan baris ACL, membuat `deny` statement lebih spesifik, dan memastikan ada `permit` yang sesuai untuk traffic yang diizinkan. Menguji setiap rule ACL secara terpisah.

**8.4. Analisis Keamanan Jaringan yang Telah Diimplementasikan**
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


---
**9. Kesimpulan**

Dokumen ini telah memfinalisasi desain topologi fisik dan logis untuk jaringan PT. Nusantara Network, menetapkan skema pengalamatan IP yang terstruktur menggunakan subnetting /24 untuk setiap VLAN, dan merinci rencana implementasi VLAN. Daftar perangkat yang dibutuhkan untuk simulasi juga telah diidentifikasi. Desain ini menjadi landasan konkret untuk memulai implementasi konfigurasi pada perangkat jaringan (Router dan Switch) di Pekan 11 dan seterusnya. Penggunaan OSPF multi-area dan penempatan layanan sentral (DHCP/DNS) di Server Farm diharapkan memenuhi kebutuhan skalabilitas, manajemen, dan fungsionalitas perusahaan.

**9.1. Kendala**
*   Pada minggu ke 11 dari perancangan Tugas Besar DMJK Studi Kasus Perancangan & Implementasi Jaringan Enterprise PT. Nusantara Network, di mana pada pengisian IP Address untuk setiap PC di tiap departemen gedung belum terisi sepenuhnya menyebabkan uji konektivitas dilakukan secara otomatis menggunakan fitur yang ada pada Cisco Packet Tracker.
*   Pada minggu ke 12, terdapat kendala saat ingin melakukan uji konektivitas antar gedung yang ada setelah dilakukan `IP Routing`-nya.

**9.2. Solusi**
*   Untuk memudahkan dalam pengisian IP address pada setiap PC, akan digunakan konfigurasi DHCP untuk setiap router-nya agar IP dapat terisi secara otomatis.
*   Melalui hasil percobaan menggunakan `routing statis` dan `routing dinamis menggunnakan OSPF`, dapat disimpulkan apabila Jaringan Enterprise PT. Nusantara Network, lebih baik dikelola menggunakan routing dinamis untuk dapat memudahkan proses konektivitas antar gedung dikarenakan koneksi jaringan pada PT. Nusantara Network memiliki skala yang cukup besar.

**9.3. Catatan**
*   Urutan baris dalam ACL sangat penting. Baris pertama yang cocok akan diterapkan.
*   Setiap ACL memiliki `deny any any` implisit di akhir jika tidak ada `permit any any` eksplisit. Untuk ACL yang diterapkan pada arah `in` yang bertujuan membatasi traffic *keluar* dari VLAN tersebut, `permit ip <source_vlan_subnet> any` di akhir (setelah semua `deny`) penting agar traffic yang diizinkan bisa keluar.
*   ACL `out` pada sub-interface memfilter traffic yang *ditujukan* ke subnet VLAN tersebut.
*   Desain ACL bisa sangat kompleks. Contoh di atas adalah dasar. Pertimbangkan penggunaan ACL *extended* untuk kontrol port spesifik jika diperlukan (misalnya, hanya izinkan HTTP ke server tertentu).

---

**10. Lampiran**

*  [ (Link ke file pkt).](https://github.com/cantikaade/TUBES-DMJK/blob/main/week%2014/KELOMPOK%201%20WEEK%2014.pkt)
*  [ (Github).](https://github.com/cantikaade/TUBES-DMJK)
*  [ (Video Demo Cisco).](https://drive.google.com/drive/folders/1ORQ1iEB4OzZFA2VVbxIIMYOfxUoRF4-a)
*  [ (PPT).](https://www.canva.com/design/DAGoK2ScWc4/ZwjXhyd7pozJ0RGoGH3V1g/edit?utm_content=DAGoK2ScWc4&utm_campaign=designshare&utm_medium=link2&utm_source=sharebutton)


---