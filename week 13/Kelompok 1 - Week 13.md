**Dokumen Desain Jaringan: Perancangan & Implementasi Jaringan Enterprise PT. Nusantara Network**

**Deliverable Pekan 13 - Mata Kuliah Desain & Manajemen Jaringan Komputer**

**Disusun oleh Kelompok [Kelompok 1]:**

1.  **Nadhilah Nur Amalina** - Network Architect
2.  **Raditya Yudianto** - Network Engineer
3.  **Irwan Maulana** - Network Services Specialist
4.  **Cantika Ade Qutnindra Maharani** - Security & Documentation Specialist

**Tanggal Pengumpulan:** Jumat, 9 Mei 2025

---

### Tugas Kelompok:
1. Konfigurasi DHCP Server untuk setiap departemen.
2. Implementasi DNS Server untuk resolusi nama internal.
3. Konfigurasi NAT untuk akses internet.
4. Lampiran

---

**1. Konfigurasi DHCP Server Untuk Setiap Departemen.**

**1.1. Konfigurasi DHCP Server Pada Gedung A**

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

**1.1. Konfigurasi DHCP Server Pada Gedung B**

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

**2. Implementasi DNS Server untuk resolusi nama internal.**
Konfigurasi DNS internal dapat dilakukan pada router atau server DNS. Pada contoh ini, konfigurasi DNS biasanya mengaktifkan resolusi nama dan mengarahkan ke server DNS internal. Berdasarkan hasil konfirgurasi DHCP di atas, dapat disimpulkan bahwa
- Alamat IP Dinamis sesuai dengan rentang yang didefinisikan pada masing-masing DHCP pool.
- Default Gateway, yang mengarahkan trafik keluar dari subnet.
- DNS Server, yaitu Google Public DNS (8.8.8.8 dan 8.8.4.4). Jika diperlukan resolusi internal, sebaiknya gunakan alamat DNS internal.
- Domain Name, yaitu gedung-a.local bagi client di Gedung A dan gedung-b.local bagi client di Gedung B.

Dengan konfigurasi ini, client tidak hanya mendapatkan alamat IP, tetapi juga informasi domain yang memudahkan mereka dalam mengakses sumber daya internal menggunakan nama daripada alamat IP. Ini sangat berguna dalam lingkungan jaringan dengan pengelolaan nama domain internal yang terstruktur.



---

**3. Konfigurasi NAT untuk akses internet.**

**3.1. Konfigurasi Interface NAT**
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

**3.2. Pembuatan Access List Untuk NAT**

Hal ini dilakukan agar router hanya menerjemahkan alamat IP dari jaringan internal yang diijinkan, pada kasus kali ini, `Access List` yang menyatakan jaringan mana yang akan menggunakan NAT.

```bash
Router(config)# access-list 1 permit 192.168.10.0 0.0.0.255
Router(config)# access-list 1 permit 192.168.11.0 0.0.0.255
Router(config)# access-list 1 permit 192.168.12.0 0.0.0.255
Router(config)# access-list 1 permit 192.168.13.0 0.0.0.255
Router(config)# access-list 1 permit 192.168.20.0 0.0.0.255
Router(config)# access-list 1 permit 192.168.21.0 0.0.0.255
```

**3.3. Konfigurasi NAT Dinamis & Statis**
Untuk memungkinkan semua host di jaringan internal berbagi satu alamat IP publik saat melakukan koneksi ke Internet, digunakan NAT dinamis dengan fitur overload (sering disebut Port Address Translation/PAT).Selain NAT dinamis, kadang diperlukan NAT statis untuk memungkinkan akses langsung ke server internal dari luar.

```bash
Router(config)# ip nat inside source list 1 interface g0/0/1 overload
Router(config)# ip nat inside source static 192.168.13.10 203.0.113.10
Router(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.254
Router(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.1
```
Dengan konfigurasi dinamis, setiap host internal yang melakukan koneksi ke internet akan diterjemahkan ke alamat IP 203.0.113.1, dengan penambahan nomor port sebagai identifikasi sesi masing-masing. Sedangkan konfigurasi statis memungkinkan klien dari internet mengakses server internal melalui 203.0.113.10, sementara trafik masuk secara otomatis ditranslasikan ke alamat internal 192.168.13.10. Lalu terdapat Routing Default agar trafik yang tidak memiliki rute spesifik (misalnya, trafik ke internet) dapat diarahkan ke router yang menghadap ke internet.

**3.3. Pengujian Konfigurasi NAT**

![Image](https://github.com/user-attachments/assets/1e1f5568-ebb7-438e-b521-08de3e5ca1c6)
- Akses ke Server Internal dari Luar: PC di jaringan luar melakukan ping ke 203.0.113.10, di mana trafik ini secara resmi ditranslasikan ke alamat internal 192.168.13.10.

![Image](https://github.com/user-attachments/assets/c5746d42-d633-446e-adf3-01a2698102d7)

- Ping dari Jaringan Internal ke IP NAT (203.0.113.1): Client di Gedung B melakukan ping ke alamat 203.0.113.1 dan menerima balasan yang cepat.

![Image](https://github.com/user-attachments/assets/1100b22e-a8e1-4e23-be7d-2a41b4bb9c42)

- Verifikasi dengan show ip nat translations: Perintah ini digunakan untuk memastikan bahwa alamat internal telah diterjemahkan sesuai aturan NAT yang ditetapkan


---

**4. Lampiran**

*  [ (Link ke file pkt).](https://github.com/cantikaade/TUBES-DMJK/blob/main/week%2013/KELOMPOK%201%20WEEK%2013.pkt)
*  [ (Link Github Week 13).](https://github.com/cantikaade/TUBES-DMJK/tree/main/week%2013)

---