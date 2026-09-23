# 🏢 PT. Nusantara Digitalindo — Enterprise Network Design

> Simulasi jaringan WAN perusahaan menggunakan Cisco Packet Tracer  
> Konsep: Static Routing, Subnetting, DHCP — Level: Entry Enterprise

---

## 📋 Studi Kasus

PT. Nusantara Digitalindo adalah perusahaan distribusi barang dengan 3 lokasi kantor:
- **HQ Jakarta** — Kantor pusat (50 host)
- **Branch Surabaya** — Kantor cabang (25 host)
- **Branch Makassar** — Kantor cabang (10 host)

Seluruh kantor terhubung melalui jaringan WAN dengan router Internet sebagai hub penghubung antar site.

---

## 🗺️ Topologi Jaringan

```
                        [ Internet Router ]
                         .2           .9
                        /               \
               .0/30   /                 \ .8/30
                      /                   \
                    .1                    .10
                 [ R1-HQ ]            [ R3-Makassar ]
                    .65                   .161
              192.168.10.64/26      192.168.10.160/28
                 (50 hosts)              (10 hosts)

                        .5
                   .4/30 |
                        .6
                  [ R2-Surabaya ]
                       .129
                 192.168.10.128/27
                      (25 hosts)
```

---

## 📊 Addressing Table

### WAN / Point-to-Point Links

| Link | Subnet | Router | Interface | IP Address |
|------|--------|--------|-----------|------------|
| R1 ↔ Internet | 192.168.10.0/30 | R1-HQ | Gi0/0/0 | 192.168.10.1 |
| R1 ↔ Internet | 192.168.10.0/30 | Internet | Gi0/0/0 | 192.168.10.2 |
| Internet ↔ R2 | 192.168.10.4/30 | Internet | Gi0/1/0 | 192.168.10.5 |
| Internet ↔ R2 | 192.168.10.4/30 | R2-Surabaya | Gi0/0/0 | 192.168.10.6 |
| Internet ↔ R3 | 192.168.10.8/30 | Internet | Gi0/2/0 | 192.168.10.9 |
| Internet ↔ R3 | 192.168.10.8/30 | R3-Makassar | Gi0/0/0 | 192.168.10.10 |

### LAN per Site

| Site | Subnet | Gateway | DHCP Range | Hosts |
|------|--------|---------|------------|-------|
| HQ Jakarta | 192.168.10.64/26 | 192.168.10.65 | .66 — .126 | 50 PC |
| Branch Surabaya | 192.168.10.128/27 | 192.168.10.129 | .130 — .158 | 25 PC |
| Branch Makassar | 192.168.10.160/28 | 192.168.10.161 | .162 — .174 | 10 PC |

---

## 🔢 Subnetting Summary

IP Block yang digunakan: `192.168.10.0/24`

| Subnet | Prefix | Kegunaan | Range | Hosts |
|--------|--------|----------|-------|-------|
| 192.168.10.0 | /30 | Serial R1 ↔ Internet | .1 - .2 | 2 |
| 192.168.10.4 | /30 | Serial Internet ↔ R2 | .5 - .6 | 2 |
| 192.168.10.8 | /30 | Serial Internet ↔ R3 | .9 - .10 | 2 |
| 192.168.10.64 | /26 | LAN HQ Jakarta | .65 - .126 | 62 |
| 192.168.10.128 | /27 | LAN Branch Surabaya | .129 - .158 | 30 |
| 192.168.10.160 | /28 | LAN Branch Makassar | .161 - .174 | 14 |

---

## ⚙️ Konfigurasi Router

### R1 — HQ Jakarta
```
hostname R1-HQ

interface GigabitEthernet0/0/0
 ip address 192.168.10.1 255.255.255.252
 no shutdown

interface GigabitEthernet0/0/1
 ip address 192.168.10.65 255.255.255.192
 no shutdown

ip dhcp excluded-address 192.168.10.65
ip dhcp pool LAN-HQ
 network 192.168.10.64 255.255.255.192
 default-router 192.168.10.65
 dns-server 8.8.8.8

ip route 192.168.10.128 255.255.255.224 192.168.10.2
ip route 192.168.10.160 255.255.255.240 192.168.10.2
```

### Internet Router
```
hostname Internet

interface GigabitEthernet0/0/0
 ip address 192.168.10.2 255.255.255.252
 no shutdown

interface GigabitEthernet0/1/0
 ip address 192.168.10.5 255.255.255.252
 no shutdown

interface GigabitEthernet0/2/0
 ip address 192.168.10.9 255.255.255.252
 no shutdown

ip route 192.168.10.64 255.255.255.192 192.168.10.1
ip route 192.168.10.128 255.255.255.224 192.168.10.6
ip route 192.168.10.160 255.255.255.240 192.168.10.10
```

### R2 — Branch Surabaya
```
hostname R2-Surabaya

interface GigabitEthernet0/0/0
 ip address 192.168.10.6 255.255.255.252
 no shutdown

interface GigabitEthernet0/0/1
 ip address 192.168.10.129 255.255.255.224
 no shutdown

ip dhcp excluded-address 192.168.10.129
ip dhcp pool LAN-Surabaya
 network 192.168.10.128 255.255.255.224
 default-router 192.168.10.129
 dns-server 8.8.8.8

ip route 192.168.10.64 255.255.255.192 192.168.10.5
ip route 192.168.10.160 255.255.255.240 192.168.10.5
```

### R3 — Branch Makassar
```
hostname R3-Makassar

interface GigabitEthernet0/0/0
 ip address 192.168.10.10 255.255.255.252
 no shutdown

interface GigabitEthernet0/0/1
 ip address 192.168.10.161 255.255.255.240
 no shutdown

ip dhcp excluded-address 192.168.10.161
ip dhcp pool LAN-Makassar
 network 192.168.10.160 255.255.255.240
 default-router 192.168.10.161
 dns-server 8.8.8.8

ip route 192.168.10.64 255.255.255.192 192.168.10.9
ip route 192.168.10.128 255.255.255.224 192.168.10.9
```

---

## ✅ Hasil Pengujian

### Ping Test (dari PC HQ)
```
C:\> ping 192.168.10.131   → Reply ✅ (HQ → Surabaya)
C:\> ping 192.168.10.162   → Reply ✅ (HQ → Makassar)
```

### Traceroute
```
HQ → Surabaya:
1. 192.168.10.65  (R1-HQ)
2. 192.168.10.2   (Internet)
3. 192.168.10.6   (R2-Surabaya)
4. 192.168.10.131 (PC Surabaya) ✅

HQ → Makassar:
1. 192.168.10.65  (R1-HQ)
2. 192.168.10.2   (Internet)
3. 192.168.10.10  (R3-Makassar)
4. 192.168.10.162 (PC Makassar) ✅
```

---

## 🛠️ Tools

- Cisco Packet Tracer 8.x
- Device: Cisco 2911 Router, Cisco 2960-24TT Switch

---

## 📁 Project Structure

```
pt-nusantara-digitalindo/
├── README.md
├── topology.png
├── test-result/
│   ├── ping-hq-to-surabaya.png
│   ├── ping-hq-to-makassar.png
│   ├── tracert-hq-to-surabaya.png
│   └── tracert-hq-to-makassar.png
└── configs/
    ├── R1-HQ.txt
    ├── Internet.txt
    ├── R2-Surabaya.txt
    └── R3-Makassar.txt
├── topology_pkt/
    ├── topology.pkt
```

---

## 👤 Author

**Sahrul Ashar**  
IT Infrastructure & Network Enthusiast  
S1 Teknik Informatika — Universitas Lamappapoleonro  
[GitHub](https://github.com/sahrulashar) | [LinkedIn](https://www.linkedin.com/in/sahrul-ashar-94909b2a7/)
