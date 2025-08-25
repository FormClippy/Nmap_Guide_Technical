# 🔍 nmap_guide_technical  

# 📖 คู่มือ Nmap ภาษาไทย  

---

## 📑 สารบัญ (Table of Contents)
- [🎯 การใช้งานพื้นฐาน](#-การใช้งานพื้นฐาน)
- [🎯 Target Specification](#-target-specification-ระบุเป้าหมายที่ต้องการสแกน)
- [🔎 Host Discovery](#-host-discovery-การค้นหาเครื่องที่ตอบสนอง)
- [🚪 Scan Techniques](#-scan-techniques-เทคนิคในการสแกนพอร์ต)
- [🎯 Port Specification](#-port-specification-การกำหนดพอร์ตที่จะสแกน)
- [⚙️ Service & Version Detection](#️-service--version-detection)
- [📜 Nmap Scripting Engine (NSE)](#-nse-nmap-scripting-engine)
- [💻 OS Detection](#-os-detection)
- [⏱ Timing & Performance](#-timing--performance)
- [🕵️ Firewall Evasion](#-firewall-evasion)
- [📝 Output](#-output-ผลลัพธ์การสแกน)
- [🌐 Nmap Overview](#-nmap-overview)

---

## 🎯 การใช้งานพื้นฐาน  

```bash
nmap [Scan Type(s)] [Options] {target}
```

---

## 🎯 Target Specification (ระบุเป้าหมายที่ต้องการสแกน)  

| 🛠️ คำสั่ง             | 📚 คำอธิบายแบบมีหลักการ                                 | 💡 ตัวอย่าง                                  |
| ---------------------- | --------------------------------------------------------- | ------------------------------------------- |
| `nmap 192.168.1.1`     | สแกน IP address แบบระบุเครื่องเดียว                       | `nmap 192.168.1.1`                          |
| `nmap scanme.nmap.org` | สแกนโดเมนเนม โดย Nmap จะทำการ resolve DNS ก่อนเริ่มสแกน   | `nmap scanme.nmap.org`                      |
| `nmap 192.168.1.0/24`  | สแกนแบบ CIDR เพื่อครอบคลุม 256 IP (Class C subnet)        | `nmap 192.168.1.0/24`                       |
| `-iL list.txt`         | ใช้ไฟล์ที่มีรายชื่อ IP หรือโดเมนเป็น input                | `nmap -iL hosts.txt`                        |
| `--exclude`            | ยกเว้นเป้าหมายบางเครื่องจากการสแกน เพื่อไม่รบกวนระบบสำคัญ | `nmap 192.168.1.0/24 --exclude 192.168.1.5` |

---

## 🔎 Host Discovery (การค้นหาเครื่องที่ตอบสนอง)

| 🛠️ คำสั่ง | 📚 คำอธิบายแบบมีหลักการ                                                 | 💡 ตัวอย่าง                  |
| ---------- | ------------------------------------------------------------------------- | ----------------------------- |
| `-sn`      | Ping Scan: ตรวจสอบว่า host ปลายทาง online หรือไม่ โดยไม่สแกนพอร์ต        | `nmap -sn 192.168.1.0/24`     |
| `-Pn`      | Disable host discovery: ใช้เมื่อ host ปลายทางไม่ตอบสนองต่อ ICMP หรือ firewall block ping | `nmap -Pn 192.168.1.5`        |
| `-PS 80`   | ส่ง TCP SYN ไปยัง port 80 เพื่อตรวจสอบการตอบรับ SYN/ACK                  | `nmap -PS80 192.168.1.5`      |
| `-PE`      | ใช้ ICMP Echo Request (เช่นเดียวกับ `ping`)                               | `nmap -PE 192.168.1.5`        |

---

## 🚪 Scan Techniques (เทคนิคในการสแกนพอร์ต)

| 🛠️ คำสั่ง | 📚 คำอธิบายแบบมีหลักการ                                                   | 💡 ตัวอย่าง               |
| ---------- | -------------------------------------------------------------------------- | -------------------------- |
| `-sS`      | TCP SYN Scan: half-open (ไม่จับมือครบ 3 ขั้นตอน) → stealth + เร็วกว่า       | `nmap -sS 192.168.1.5`     |
| `-sT`      | TCP Connect Scan: ใช้ system call `connect()` ทำ handshake ปกติ             | `nmap -sT 192.168.1.5`     |
| `-sU`      | UDP Scan: ส่ง UDP packet ตรวจสอบการตอบกลับ ICMP unreachable                | `nmap -sU 192.168.1.5`     |
| `-sA`      | TCP ACK Scan: ตรวจหา firewall/filter โดยไม่ได้บอกว่า port เปิด/ปิด           | `nmap -sA 192.168.1.5`     |
| `-sN/-sF/-sX` | TCP scan แบบ Null, FIN, Xmas → หลบ firewall ที่ไม่กรองแพ็กเกตพิเศษ       | `nmap -sX 192.168.1.5`     |

---

## 🎯 Port Specification (การกำหนดพอร์ตที่จะสแกน)

| 🛠️ คำสั่ง      | 📚 คำอธิบายแบบมีหลักการ                        | 💡 ตัวอย่าง                           |
| --------------- | ----------------------------------------------- | -------------------------------------- |
| `-p 22`         | สแกนเฉพาะ port ที่ระบุ                         | `nmap -p 22 192.168.1.5`               |
| `-p 1-65535`    | สแกนทุกพอร์ต TCP/UDP                           | `nmap -p 1-65535 192.168.1.5`          |
| `-F`            | Fast Scan (~100 พอร์ตยอดนิยม)                  | `nmap -F 192.168.1.5`                  |
| `--top-ports 100` | สแกนพอร์ตยอดนิยมตามฐานข้อมูลของ Nmap          | `nmap --top-ports 100 192.168.1.5`     |

---

## ⚙️ Service & Version Detection

| 🛠️ คำสั่ง           | 📚 คำอธิบาย                                  | 💡 ตัวอย่าง                             |
| -------------------- | --------------------------------------------- | ---------------------------------------- |
| `-sV`                | ตรวจสอบ service/เวอร์ชัน ด้วย probe + pattern | `nmap -sV 192.168.1.5`                   |
| `--version-intensity` | ปรับระดับการ probe (0 เร็ว, 9 ละเอียดมากสุด) | `nmap -sV --version-intensity 9 target` |

---

## 📜 NSE (Nmap Scripting Engine)

| 🛠️ คำสั่ง           | 📚 คำอธิบาย                                  | 💡 ตัวอย่าง                          |
| -------------------- | --------------------------------------------- | ------------------------------------- |
| `-sC`                | รันสคริปต์ default (service detect, vuln)    | `nmap -sC 192.168.1.5`                |
| `--script=vuln`      | รันสคริปต์ค้นหาช่องโหว่ที่รู้จัก             | `nmap --script=vuln 192.168.1.5`      |
| `--script=http-enum` | ตรวจ endpoint/directory ใน web service        | `nmap --script=http-enum 192.168.1.5` |

---

## 💻 OS Detection

| 🛠️ คำสั่ง        | 📚 คำอธิบาย                         | 💡 ตัวอย่าง                       |
| ----------------- | ----------------------------------- | ---------------------------------- |
| `-O`              | ใช้ TCP/IP fingerprint หา OS       | `nmap -O 192.168.1.5`              |
| `--osscan-guess`  | เดา OS ถ้า fingerprint ไม่ตรงชัดเจน | `nmap -O --osscan-guess target`    |
| `-A`              | รวม OS detect + service + script   | `nmap -A 192.168.1.5`              |

---

## ⏱ Timing & Performance

| 🛠️ คำสั่ง   | 📚 คำอธิบาย                           | 💡 ตัวอย่าง                  |
| ------------ | -------------------------------------- | ----------------------------- |
| `-T0..-T5`   | Profile scan speed (T0 = paranoid, T5 = aggressive) | `nmap -T4 target` |
| `--min-rate` | กำหนด packets/second ขั้นต่ำ           | `nmap --min-rate 1000 target` |
| `--max-rate` | จำกัด packets/second สูงสุด             | `nmap --max-rate 500 target`  |

---

## 🕵️ Firewall Evasion

| 🛠️ คำสั่ง     | 📚 คำอธิบาย                       | 💡 ตัวอย่าง                        |
| -------------- | ---------------------------------- | ----------------------------------- |
| `-f`           | Fragment packet หลบ firewall       | `nmap -f target`                    |
| `-D`           | ใช้ decoy IP สับขาหลอก IDS        | `nmap -D RND:10 target`             |
| `-S`           | ปลอม source IP                     | `nmap -S 1.2.3.4 target`            |
| `--spoof-mac`  | ปลอม MAC address                   | `nmap --spoof-mac 00:11:22:33:44:55 target` |

---

## 📝 Output (ผลลัพธ์การสแกน)

| 🛠️ คำสั่ง     | 📚 คำอธิบาย                    | 💡 ตัวอย่าง                      |
| -------------- | ------------------------------- | --------------------------------- |
| `-oN file.txt` | บันทึกผลแบบ text               | `nmap -oN result.txt target`      |
| `-oX file.xml` | บันทึกผลแบบ XML                | `nmap -oX result.xml target`      |
| `-oG file.grep`| Grepable output ใช้ต่อกับ script | `nmap -oG result.grep target`     |
| `-oA base`     | บันทึกทั้ง 3 format พร้อมกัน    | `nmap -oA scan target`            |

---

# 🌐 Nmap Overview


## 🎯 Target Specification

## 1) nmap {Your target} (สแกน IP เดี่ยว)

**ใช้เมื่อไหร่**
-  ต้องการสำรวจเครื่องเป้าหมายเครื่องเดียวแบบตรงๆ (ด่วน/โฟกัส)
-  ตรวจสอบพอร์ตยอดนิยม 1,000 พอร์ตเริ่มต้นของ Nmap (ถ้าไม่ระบุ `-p`)

**เทคนิค/กลไก**
-  ขั้นแรก Nmap ทำ **Host Discovery** (ถ้าไม่ใส่ `-Pn`) เพื่อตัดสินใจว่าจะสแกนต่อไหม
     -  Host Discovery คือ ก่อนที่ Nmap จะสแกนพอร์ต มันต้องรู้ก่อนว่า **โฮสต์เป้าหมายมีชีวิต (alive) อยู่หรือเปล่า** 
     -  ถ้า Host Discovery บอกว่า "เครื่องนี้ไม่ตอบสนอง" → Nmap จะไม่เสียเวลาสแกนพอร์ต (ยกเว้นจะสั่ง `-Pn` ให้เชื่อว่ามันออนไลน์แน่นอน)
    
-  ตามด้วย **Port Scan** (default = 1,000 TCP ports ที่พบบ่อย)
-  อาจทำ **Reverse DNS** เพื่อโชว์ชื่อโฮสต์ (เว้นแต่ใส่ `-n` คือ **No DNS resolution** (เร็วขึ้น, แสดงแต่ IP))
      -  Nmap จะ **พยายามถาม DNS** ว่า IP นี้มี PTR (Pointer Record) ไหม  
      -  ถ้ามี → จะโชว์ hostname ของ IP นั้น

**ผลลัพธ์หน้าตา**
```python
Nmap scan report for 192.168.1.5
Host is up (0.90s latency).
Not shown: 998 closed ports
PORT   STATE  SERVICE
22/tcp open   ssh
80/tcp open   http
```

**ใช้เมื่อต้องการรู้อะไร**
-  เครื่องนี้ “ออนไลน์ไหม” (Host is up?)
-  พอร์ตยอดนิยมไหน “เปิด/ปิด/ถูกกรอง” (open/closed/filtered)
-  จะเจาะต่ออะไรได้บ้าง (เช่นพบ `ssh`, `http`)

**ข้อควรระวัง**
-  ถ้าเครือข่ายบล็อก ICMP อาจขึ้น “Host seems down” → ลอง `-Pn`เพื่อไม่สนว่า host นั้นจะ Online หรือ Offline
-  ต้องการละเอียดขึ้นเรื่องบริการ/เวอร์ชัน → เพิ่ม `-sV` หรือ `-A`
-  ไม่มี root แล้วอยากใช้ SYN scan (`-sS`) จะไม่ได้ → จะ fallback เป็น `-sT`


## 2) nmap {domain name} (Scan Domain Name)

**ใช้เมื่อไหร่**
-  รู้โดเมน แต่ไม่รู้/ไม่อยากผูกกับ IP ตายตัว
-  ระบบปลายทางมี **หลาย IP (round-robin / anycast / load balancer)** อยากให้ Nmap จัดการให้

**เทคนิค/กลไก**
-  Nmap จะทำ **DNS Resolution** ก่อน (ถาม DNS ว่า `scanme.nmap.org` ชี้ไปที่ IP อะไร)
-  จากนั้นมันจะเอา IP ที่ได้มาเป็นเป้าหมายในการสแกน
เช่น ถ้า DNS ตอบกลับมาว่า

```python
scanme.nmap.org -> 45.33.32.156
```

Nmap ก็จะไปสแกนเฉพาะ `45.33.32.156` ไม่ได้ไปสแกน IP อื่น ๆ ทั้งหมดที่อยู่ใน "domain" นั้น 

 -  ถ้า Domain มีหลาย IP (เช่นกรณี Load Balancer / Round-robin DNS) บางโดเมน (โดยเฉพาะเว็บใหญ่ ๆ เช่น Google, Cloudflare) เวลา resolve DNS อาจตอบกลับมาหลาย IP เช่น 
 
```python
example.com -> 1.1.1.1
example.com -> 1.0.0.1
```

ขึ้นกับ DNS server ว่าให้ IP ไหนกลับมา
-  Nmap จะสแกน **เฉพาะ IP ที่ถูก resolve ได้ในตอนนั้น**
-  ถ้าอยากสแกนทุก IP ที่เกี่ยวข้องกับ domain ต้องใช้ `--resolve-all`

```python
nmap --resolve-all example.com
```

**ผลลัพธ์หน้าตา** (ถ้าโดเมนชี้ไปหลาย IP จะได้หลายบล็อกรายงาน)

```python
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up ...
PORT  STATE SERVICE
80/tcp open  http
...

Nmap scan report for scanme.nmap.org (2600:3c01::f03c:91ff:fe18:bb2f)
Host is up ...
...
```

**ใช้เมื่อต้องการรู้อะไร**
-  บริการที่ “ปลายโดเมนจริงๆ” เปิดอะไรบ้าง แม้หลัง load balancer
-  ความแตกต่างของพอร์ต/บริการระหว่าง IP หลายตัวที่อยู่หลังโดเมนเดียวกัน
-
**ข้อควรระวัง**
-  โดเมนอาจแกว่ง IP ตามเวลา (DNS TTL/round-robin) → ผลแต่ละครั้งอาจต่าง
-  ถ้าอยากบังคับ IPv6 ใช้ `-6`
-  ถ้าการแก้ชื่อ (DNS) มีปัญหา สแกนอาจช้า/พัง → ใช้ `--dns-servers` ช่วย

## 🔎 “การแก้ชื่อ (DNS)” หมายถึงอะไร
-  เวลาเราพิมพ์ `nmap scanme.nmap.org` → Nmap ยังไม่รู้ว่า host นี้คือ **IP อะไร**
-  Nmap เลยต้องไป “ถาม DNS Server” ก่อน ว่า _“scanme.nmap.org มี IP อะไร?”_
-  ขั้นตอนนี้เองที่เรียกว่า **การแก้ชื่อ (Name Resolution / DNS Resolution)**
-
## ⚠️ ถ้า DNS ทำงานไม่ดี (ช้า หรือเจ๊ง) จะเกิดอะไรขึ้น

- ถ้า DNS ตอบช้า → Nmap ต้องรอนาน → การสแกน **ช้าลง**
- ถ้า DNS ไม่ตอบ → Nmap จะ error ว่า _“Failed to resolve hostname”_ → การสแกน **พังตั้งแต่ยังไม่เริ่ม**
- หรือบางที DNS ภายในองค์กรแก้ชื่อไปที่ **IP ไม่ตรงกับ public** → Pentester อาจเจอผลลัพธ์ไม่เหมือนคนอื่น

## 🛠️ วิธีแก้ปัญหาใน Nmap

- ปกติ Nmap จะใช้ DNS server ที่ระบบตั้งไว้ (เช่น DNS ของ Router/ISP)
- ถ้า DNS นั้นมีปัญหา → คุณสามารถบังคับ Nmap ให้ไปถาม DNS server ที่คุณมั่นใจได้ เช่น Cloudflare หรือ Google

```python
nmap --dns-servers 1.1.1.1,8.8.8.8 scanme.nmap.org
```

→ แบบนี้ Nmap จะไม่สนใจ DNS ของเครื่องคุณ แต่ไปถาม 1.1.1.1 และ 8.8.8.8 แทน
→ ผลคือ **สแกนไม่สะดุด** และคุณมั่นใจว่าได้ IP ที่ “ถูกต้อง/เป็น public จริง”


## 3) `nmap {subnet} (สแกนยกซับเน็ตด้วย CIDR)

**ใช้เมื่อไหร่**
- ทำ **Discovery/Inventory** ทั้งซอย/วงย่อย
- อยากรู้ว่า **มีเครื่องอะไรบ้าง + เปิดพอร์ตอะไร** ในเครือข่ายนั้น

**เทคนิค/กลไก**
- Nmap ขยาย CIDR เป็นรายการไอพีทั้งหมดในช่วง (รวม .0 และ .255 ด้วย — Nmap ไม่ยึด classful แบบเก่า)
- ทำ Host Discovery แบบขนาน (Parallel) แล้วค่อยสแกนพอร์ตตามนโยบายของคุณ
- ปรับความเร็ว/ภาระเครือข่ายด้วย `-T<0..5>`, `--min-rate/--max-rate`, `--min-hostgroup/--max-hostgroup`

**ผลลัพธ์หน้าตา** (สรุปหลายโฮสต์)

```python
Nmap scan report for 192.168.1.3
Host is up.
PORT   STATE SERVICE
22/tcp open  ssh

Nmap scan report for 192.168.1.10
Host is up.
PORT   STATE SERVICE
80/tcp open  http
...
```

**ใช้เมื่อต้องการรู้อะไร**
- แผนที่บริการทั้งย่าน (ใครออนไลน์ / เปิดพอร์ตอะไร)
- ใช้คัดเป้าหมายสำหรับการเจาะเชิงลึกต่อไป

**ข้อควรระวัง**
- **รบกวนระบบ**ได้ถ้าไม่กำกับความเร็ว/ขนาดกลุ่มโฮสต์ → ระวัง production
     -  เวลา Nmap สแกน **ทั้ง subnet** (เช่น `/24` = 256 IP) มันจะส่ง packet ออกไปเยอะมาก (ping, probe, SYN, ฯลฯ)
     -  ถ้าไม่กำหนดความเร็ว (`-T`, `--min-rate`, `--max-rate`) หรือขนาด batch (`--min-hostgroup`, `--max-hostgroup`) → Nmap จะเลือกวิธีที่มันคิดว่าเร็วพอสมควรเอง ซึ่งบางทีอาจส่ง request ทีละเยอะ ๆ
     -  ผลลัพธ์คือ **network หรือเครื่อง production อาจถูก flood จนช้าลง** (เหมือนมีคนมาเคาะประตูพร้อมกันหลายร้อยคน)
- ถ้าต้องการแค่หาว่าใครออนไลน์ก่อน ควรเริ่ม `-sn` แล้วค่อยสแกนพอร์ตเฉพาะที่ออนไลน์
- เครือข่ายภายในจะใช้ **ARP ping** โดยอัตโนมัติ ซึ่ง “แม่นและเร็ว” แต่ก็สร้างทราฟฟิก ARP มาก
     -  บน **LAN (Layer 2)** การเช็กว่า host นั้นมีตัวตนอยู่หรือไม่ ปกติ Nmap จะใช้ **ARP (Address Resolution Protocol) request** แทนที่จะส่ง ICMP ping
     -  เพราะบน LAN มันเชื่อถือได้และเร็วกว่า: ถ้าเครื่องเปิดอยู่ → มันจะตอบ ARP ทันที

**ทำไม ARP บน LAN มันเร็วมากเพราะใช้แค่ Layer 2 broadcast**
📡 1. Network Layer มีกี่ชั้น (เอาเท่าที่เกี่ยว)

Layer 2 (Data Link Layer) = คุยกันด้วย MAC Address ผ่าน Ethernet Frame

Layer 3 (Network Layer) = คุยกันด้วย IP Address ผ่าน IP Packet

เวลาเรา “ping” หรือ “ส่งข้อมูล”

ที่ชั้น IP (Layer 3) → ต้องรู้ก่อนว่า IP นี้อยู่ที่ MAC ไหน ถึงจะส่งถึง

ดังนั้นถ้าไม่รู้ MAC → ต้องถามก่อน → นี่คือหน้าที่ของ ARP (Address Resolution Protocol)

⚡ 2. ARP Request/Reply ทำงานยังไง

สมมติคุณอยู่ใน LAN 192.168.1.0/24

เครื่องคุณจะถามออกไปว่า:
“ใครคือ 192.168.1.10? ถ้าใช่ ตอบกลับมาพร้อม MAC ด้วย”

การถามนี้ส่งแบบ Broadcast → ทุกเครื่องใน LAN ได้ยินหมด (เพราะส่งที่ Layer 2 broadcast address FF:FF:FF:FF:FF:FF)

เครื่องที่เป็น 192.168.1.10 จะตอบกลับมาพร้อม MAC ของมัน

⚡ 3. ทำไมเร็วกว่า ICMP

ICMP Ping = ต้องสร้าง IP Packet → ส่งไปหา target → รออีกฝ่ายตอบกลับ ICMP Echo Reply
(ต้องขึ้นไปถึง Layer 3 แล้วลงมาที่ Layer 2)

ARP Ping = แค่ถาม MAC ตรง ๆ บน Layer 2 → ได้คำตอบทันทีถ้า host นั้นอยู่ใน subnet เดียวกัน
(ไม่ต้องผ่าน routing ไม่ต้องวิ่งออกนอก LAN)

ดังนั้น Nmap บน LAN จะเลือกใช้ ARP scan เพราะ

เร็ว → คำตอบมาแทบ real-time

แม่น → ถ้าเครื่องเปิดอยู่ มันต้องตอบ ARP (ไม่ block ได้เหมือน ICMP)

🔔 4. “แต่ก็สร้างทราฟฟิกเยอะ”

เพราะมัน broadcast เลยทุกครั้ง → ทุกเครื่องใน subnet จะเห็นการถาม ARP ของคุณทั้งหมด
ถ้าคุณสแกน /24 (256 IP) = คุณ broadcast 256 ARP request
ถ้า /16 (65,000 IP) = คุณ broadcast 65,000 ARP request
→ Blue Team จะเห็น ARP flood ชัดเจน

## 4) `nmap -iL ip.txt` (สแกนจากไฟล์รายการเป้าหมาย)

