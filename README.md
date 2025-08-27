# 🔍 nmap_guide_technical  

# 📖 คู่มือ Nmap   

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
- [🌐 Nmap Overview](#nmap-overview)


---

## 🎯 การใช้งานพื้นฐาน  

```bash
nmap [Scan Type(s)] [Options] {target}
```

---

## 🎯 Target Specification (ระบุเป้าหมายที่ต้องการสแกน)  

| 🛠️ คำสั่ง             | 📚 คำอธิบาย                                 | 💡 ตัวอย่าง                                  |
| ---------------------- | --------------------------------------------------------- | ------------------------------------------- |
| `nmap 192.168.1.1`     | สแกน IP address แบบระบุเครื่องเดียว                       | `nmap 192.168.1.1`                          |
| `nmap scanme.nmap.org` | สแกนโดเมนเนม โดย Nmap จะทำการ resolve DNS ก่อนเริ่มสแกน   | `nmap scanme.nmap.org`                      |
| `nmap 192.168.1.0/24`  | สแกนแบบ CIDR เพื่อครอบคลุม 256 IP (Class C subnet)        | `nmap 192.168.1.0/24`                       |
| `-iL list.txt`         | ใช้ไฟล์ที่มีรายชื่อ IP หรือโดเมนเป็น input                | `nmap -iL hosts.txt`                        |
| `--exclude`            | ยกเว้นเป้าหมายบางเครื่องจากการสแกน เพื่อไม่รบกวนระบบสำคัญ | `nmap 192.168.1.0/24 --exclude 192.168.1.5` |

---

## 🔎 Host Discovery (การค้นหาเครื่องที่ตอบสนอง)

| 🛠️ คำสั่ง | 📚 คำอธิบาย                                           | 💡 ตัวอย่าง                  |
| ---------- | ------------------------------------------------------------------------- | ----------------------------- |
| `-sn`      | Ping Scan: ตรวจสอบว่า host ปลายทาง online หรือไม่ โดยไม่สแกนพอร์ต        | `nmap -sn 192.168.1.0/24`     |
| `-Pn`      | Disable host discovery: ใช้เมื่อ host ปลายทางไม่ตอบสนองต่อ ICMP หรือ firewall block ping | `nmap -Pn 192.168.1.5`        |
| `-PS 80`   | ส่ง TCP SYN ไปยัง port 80 เพื่อตรวจสอบว่ามีการตอบรับ SYN/ACK หรือไม่          | `nmap -PS80 192.168.1.5`      |
| `-PE`      | ใช้ ICMP Echo Request (เช่นเดียวกับ `ping`)                               | `nmap -PE 192.168.1.5`        |

---

## 🚪 Scan Techniques (เทคนิคในการสแกนพอร์ต)

| 🛠️ คำสั่ง | 📚 คำอธิบาย                                                  | 💡 ตัวอย่าง               |
| ---------- | -------------------------------------------------------------------------- | -------------------------- |
| `-sS`      | TCP SYN Scan: ใช้เทคนิคส่ง SYN โดยไม่ทำการจับมือ 3 ขั้นตอน (half-open) จึง stealth   | `nmap -sS 192.168.1.5`     |
| `-sT`      | TCP Connect Scan: ใช้ system call `connect()` ทำ handshake ปกติ             | `nmap -sT 192.168.1.5`     |
| `-sU`      | UDP Scan: ส่ง UDP packet เพื่อตรวจสอบว่า port นั้นเปิดหรือปิด ซึ่งอาจตอบกลับ ICMP port unreachable  | `nmap -sU 192.168.1.5`     |
| `-sA`      | TCP ACK Scan: ใช้สำหรับระบุว่ามี firewall/filter อยู่หรือไม่ โดยไม่สามารถบอกได้ว่า port เปิดหรือปิด | `nmap -sA 192.168.1.5`     |
| `-sN/-sF/-sX` | TCP scans แบบไม่มี flag (Null), FIN, และ Xmas เพื่อหลบการตรวจจับจาก firewall ที่ไม่ได้กรอง packet แบบนี้ | `nmap -sX 192.168.1.5`     |

---

## 🎯 Port Specification (การกำหนดพอร์ตที่จะสแกน)

| 🛠️ คำสั่ง      | 📚 คำอธิบาย                       | 💡 ตัวอย่าง                           |
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

**📝 ใช้เมื่อไหร่**
-  ต้องการสำรวจเครื่องเป้าหมายเครื่องเดียวแบบตรงๆ (ด่วน/โฟกัส)
-  ตรวจสอบพอร์ตยอดนิยม 1,000 พอร์ตเริ่มต้นของ Nmap (ถ้าไม่ระบุ `-p`)

**⚙️ เทคนิค/กลไก**
-  ขั้นแรก Nmap ทำ **Host Discovery** (ถ้าไม่ใส่ `-Pn`) เพื่อตัดสินใจว่าจะสแกนต่อไหม
     -  Host Discovery คือ ก่อนที่ Nmap จะสแกนพอร์ต มันต้องรู้ก่อนว่า **โฮสต์เป้าหมายมีชีวิต (alive) อยู่หรือเปล่า** 
     -  ถ้า Host Discovery บอกว่า "เครื่องนี้ไม่ตอบสนอง" → Nmap จะไม่เสียเวลาสแกนพอร์ต (ยกเว้นจะสั่ง `-Pn` ให้เชื่อว่ามันออนไลน์แน่นอน)
    
-  ตามด้วย **Port Scan** (default = 1,000 TCP ports ที่พบบ่อย)
-  อาจทำ **Reverse DNS** เพื่อโชว์ชื่อโฮสต์ (เว้นแต่ใส่ `-n` คือ **No DNS resolution** (เร็วขึ้น, แสดงแต่ IP))
      -  Nmap จะ **พยายามถาม DNS** ว่า IP นี้มี PTR (Pointer Record) ไหม  
      -  ถ้ามี → จะโชว์ hostname ของ IP นั้น

**📊 ตัวอย่างผลลัพธ์**

```python
Nmap scan report for 192.168.1.5
Host is up (0.90s latency).
Not shown: 998 closed ports
PORT   STATE  SERVICE
22/tcp open   ssh
80/tcp open   http
```

**❓ ใช้เมื่อต้องการรู้อะไร**
-  เครื่องนี้ “ออนไลน์ไหม” (Host is up?)
-  พอร์ตยอดนิยมไหน “เปิด/ปิด/ถูกกรอง” (open/closed/filtered)
-  จะเจาะต่ออะไรได้บ้าง (เช่นพบ `ssh`, `http`)

**⚠️ ข้อควรระวัง**
-  ถ้าเครือข่ายบล็อก ICMP อาจขึ้น “Host seems down” → ลอง `-Pn`เพื่อไม่สนว่า host นั้นจะ Online หรือ Offline
-  ต้องการละเอียดขึ้นเรื่องบริการ/เวอร์ชัน → เพิ่ม `-sV` หรือ `-A`
-  ไม่มี root แล้วอยากใช้ SYN scan (`-sS`) จะไม่ได้ → จะ fallback เป็น `-sT`


## 2) สแกน Domain Name ด้วย nmap {domain}

**📝 ใช้เมื่อไหร่**
-  รู้โดเมน แต่ไม่รู้/ไม่อยากผูกกับ IP ตายตัว
-  ระบบปลายทางมี **หลาย IP (round-robin / anycast / load balancer)** อยากให้ Nmap จัดการให้

**⚙️ เทคนิค/กลไก**
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

**📊 ตัวอย่างผลลัพธ์** (ถ้าโดเมนชี้ไปหลาย IP จะได้หลายบล็อกรายงาน)

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

**❓ ใช้เมื่อต้องการรู้อะไร**
-  บริการที่ “ปลายโดเมนจริงๆ” เปิดอะไรบ้าง แม้หลัง load balancer
-  ความแตกต่างของพอร์ต/บริการระหว่าง IP หลายตัวที่อยู่หลังโดเมนเดียวกัน
-
**⚠️ ข้อควรระวัง**
-  โดเมนอาจแกว่ง IP ตามเวลา (DNS TTL/round-robin) → ผลแต่ละครั้งอาจต่าง
-  ถ้าอยากบังคับ IPv6 ใช้ `-6`
-  ถ้าการแก้ชื่อ (DNS) มีปัญหา สแกนอาจช้า/พัง → ใช้ `--dns-servers` ช่วย

### 🔎 “การแก้ชื่อ (DNS)” หมายถึงอะไร
-  เวลาเราพิมพ์ `nmap scanme.nmap.org` → Nmap ยังไม่รู้ว่า host นี้คือ **IP อะไร**
-  Nmap เลยต้องไป “ถาม DNS Server” ก่อน ว่า _“scanme.nmap.org มี IP อะไร?”_
-  ขั้นตอนนี้เองที่เรียกว่า **การแก้ชื่อ (Name Resolution / DNS Resolution)**
-
### ⚠️ ถ้า DNS ทำงานไม่ดี (ช้า หรือเจ๊ง) จะเกิดอะไรขึ้น

- ถ้า DNS ตอบช้า → Nmap ต้องรอนาน → การสแกน **ช้าลง**
- ถ้า DNS ไม่ตอบ → Nmap จะ error ว่า _“Failed to resolve hostname”_ → การสแกน **พังตั้งแต่ยังไม่เริ่ม**
- หรือบางที DNS ภายในองค์กรแก้ชื่อไปที่ **IP ไม่ตรงกับ public** → Pentester อาจเจอผลลัพธ์ไม่เหมือนคนอื่น

### 🛠️ วิธีแก้ปัญหาใน Nmap

- ปกติ Nmap จะใช้ DNS server ที่ระบบตั้งไว้ (เช่น DNS ของ Router/ISP)
- ถ้า DNS นั้นมีปัญหา → คุณสามารถบังคับ Nmap ให้ไปถาม DNS server ที่คุณมั่นใจได้ เช่น Cloudflare หรือ Google

```python
nmap --dns-servers 1.1.1.1,8.8.8.8 scanme.nmap.org
```

→ แบบนี้ Nmap จะไม่สนใจ DNS ของเครื่องคุณ แต่ไปถาม 1.1.1.1 และ 8.8.8.8 แทน
→ ผลคือ **สแกนไม่สะดุด** และคุณมั่นใจว่าได้ IP ที่ “ถูกต้อง/เป็น public จริง”


## 3) nmap {subnet} (สแกนยกซับเน็ตด้วย CIDR)

**📝 ใช้เมื่อไหร่**
- ทำ **Discovery/Inventory** ทั้งซอย/วงย่อย
- อยากรู้ว่า **มีเครื่องอะไรบ้าง + เปิดพอร์ตอะไร** ในเครือข่ายนั้น

**⚙️ เทคนิค/กลไก**
- Nmap ขยาย CIDR เป็นรายการไอพีทั้งหมดในช่วง (รวม .0 และ .255 ด้วย — Nmap ไม่ยึด classful แบบเก่า)
- ทำ Host Discovery แบบขนาน (Parallel) แล้วค่อยสแกนพอร์ตตามนโยบายของคุณ
- ปรับความเร็ว/ภาระเครือข่ายด้วย `-T<0..5>`, `--min-rate/--max-rate`, `--min-hostgroup/--max-hostgroup`

**📊 ตัวอย่างผลลัพธ์** (สรุปหลายโฮสต์)

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

**❓ ใช้เมื่อต้องการรู้อะไร**
- แผนที่บริการทั้งย่าน (ใครออนไลน์ / เปิดพอร์ตอะไร)
- ใช้คัดเป้าหมายสำหรับการเจาะเชิงลึกต่อไป

**⚠️ ข้อควรระวัง**
- **รบกวนระบบ**ได้ถ้าไม่กำกับความเร็ว/ขนาดกลุ่มโฮสต์ → ระวัง production
     -  เวลา Nmap สแกน **ทั้ง subnet** (เช่น `/24` = 256 IP) มันจะส่ง packet ออกไปเยอะมาก (ping, probe, SYN, ฯลฯ)
     -  ถ้าไม่กำหนดความเร็ว (`-T`, `--min-rate`, `--max-rate`) หรือขนาด batch (`--min-hostgroup`, `--max-hostgroup`) → Nmap จะเลือกวิธีที่มันคิดว่าเร็วพอสมควรเอง ซึ่งบางทีอาจส่ง request ทีละเยอะ ๆ
     -  ผลลัพธ์คือ **network หรือเครื่อง production อาจถูก flood จนช้าลง** (เหมือนมีคนมาเคาะประตูพร้อมกันหลายร้อยคน)
- ถ้าต้องการแค่หาว่าใครออนไลน์ก่อน ควรเริ่ม `-sn` แล้วค่อยสแกนพอร์ตเฉพาะที่ออนไลน์
- เครือข่ายภายในจะใช้ **ARP ping** โดยอัตโนมัติ ซึ่ง “แม่นและเร็ว” แต่ก็สร้างทราฟฟิก ARP มาก
     -  บน **LAN (Layer 2)** การเช็กว่า host นั้นมีตัวตนอยู่หรือไม่ ปกติ Nmap จะใช้ **ARP (Address Resolution Protocol) request** แทนที่จะส่ง ICMP ping
     -  เพราะบน LAN มันเชื่อถือได้และเร็วกว่า: ถ้าเครื่องเปิดอยู่ → มันจะตอบ ARP ทันที

### 💡 **ทำไม ARP บน LAN มันเร็วมากเพราะใช้แค่ Layer 2 broadcast**

📡 1. Network Layer มีกี่ชั้น (เอาเท่าที่เกี่ยว)
-  Layer 2 (Data Link Layer) = คุยกันด้วย MAC Address ผ่าน Ethernet Frame
-  Layer 3 (Network Layer) = คุยกันด้วย IP Address ผ่าน IP Packet
เวลาเรา “ping” หรือ “ส่งข้อมูล”
-  ที่ชั้น IP (Layer 3) → ต้องรู้ก่อนว่า IP นี้อยู่ที่ MAC ไหน ถึงจะส่งถึง
-  ดังนั้นถ้าไม่รู้ MAC → ต้องถามก่อน → นี่คือหน้าที่ของ ARP (Address Resolution Protocol)

⚡ 2. ARP Request/Reply ทำงานยังไง
สมมติคุณอยู่ใน LAN `192.168.1.0/24`
-  เครื่องคุณจะถามออกไปว่า:

“ใครคือ 192.168.1.10? ถ้าใช่ ตอบกลับมาพร้อม MAC ด้วย”
-  การถามนี้ส่งแบบ Broadcast → ทุกเครื่องใน LAN ได้ยินหมด (เพราะส่งที่ Layer 2 broadcast address `FF:FF:FF:FF:FF:FF`)
-  เครื่องที่เป็น `192.168.1.10` จะตอบกลับมาพร้อม MAC ของมัน

⚡ 3. ทำไมเร็วกว่า ICMP
-  ICMP Ping = ต้องสร้าง IP Packet → ส่งไปหา target → รออีกฝ่ายตอบกลับ ICMP Echo Reply
(ต้องขึ้นไปถึง Layer 3 แล้วลงมาที่ Layer 2)
-  ARP Ping = แค่ถาม MAC ตรง ๆ บน Layer 2 → ได้คำตอบทันทีถ้า host นั้นอยู่ใน subnet เดียวกัน
(ไม่ต้องผ่าน routing ไม่ต้องวิ่งออกนอก LAN)
ดังนั้น Nmap บน LAN จะเลือกใช้ ARP scan เพราะ
-  เร็ว → คำตอบมาแทบ real-time
-  แม่น → ถ้าเครื่องเปิดอยู่ มันต้องตอบ ARP (ไม่ block ได้เหมือน ICMP)

🔔 4. “แต่ก็สร้างทราฟฟิกเยอะ”
เพราะมัน broadcast เลยทุกครั้ง → ทุกเครื่องใน subnet จะเห็นการถาม ARP ของคุณทั้งหมด
ถ้าคุณสแกน `/24` (256 IP) = คุณ broadcast 256 ARP request
ถ้า `/16` (65,000 IP) = คุณ broadcast 65,000 ARP request
→ Blue Team จะเห็น ARP flood ชัดเจน

## 4) nmap -iL ip.txt (สแกนจากไฟล์รายการเป้าหมาย)

**📝 ใช้เมื่อไหร่**  
-  เวลาเรามี เป้าหมายหลายตัว (IP, subnet, หรือโดเมน) แล้วไม่อยากพิมพ์ยาว ๆ ในบรรทัดเดียว
-  ต้องการ สแกนแบบเดิมซ้ำได้ → เก็บเป้าหมายไว้ในไฟล์ แล้วใช้ไฟล์นั้นรันกี่ครั้งก็ได้
  
**⚙️ เทคนิค/กลไก**  
-  ไฟล์ `hosts.txt` รองรับทั้ง:
    -  IP เดี่ยว → `203.0.113.10`
    -  ช่วง IP → `10.0.0-255.1-254`
    -  CIDR → `10.10.0.0/24`
    -  โดเมน → `scanme.nmap.org`
-  ขึ้นบรรทัดใหม่หรือเว้นวรรคได้ → Nmap จะอ่านทั้งหมด
-  ใส่ `#` ขึ้นต้นบรรทัดเพื่อทำคอมเมนต์ได้
-  ถ้าบาง target ซ้ำกัน → Nmap จะไม่ยิงซ้ำ (deduplicate ให้เอง)

**📂 ตัวอย่างไฟล์ hosts.txt**

```python
# DMZ web servers
203.0.113.10
203.0.113.11
scanme.nmap.org
10.10.0.0/24
```

**📊 ตัวอย่างผลลัพธ์**  

```python
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-27 10:42 +07
Nmap scan report for 203.0.113.10
Host is up (0.032s latency).
Not shown: 997 closed tcp ports (reset)
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
80/tcp  open  http    Apache httpd 2.4.29 ((Ubuntu))
443/tcp open  ssl/http Apache httpd 2.4.29

Nmap scan report for 203.0.113.11
Host is up (0.028s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
3306/tcp open mysql   MySQL 5.7.38

Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.040s latency).
Not shown: 996 closed tcp ports (reset)
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13
80/tcp    open  http    Apache httpd 2.4.7 ((Ubuntu))
9929/tcp  open  nping-echo
31337/tcp open  Elite

Nmap scan report for 10.10.0.1
Host is up (0.00080s latency).
All 1000 scanned ports on 10.10.0.1 are closed

Nmap scan report for 10.10.0.2
Host is up (0.0010s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5 (protocol 2.0)

Nmap scan report for 10.10.0.3
Host is up (0.00090s latency).
All 1000 scanned ports on 10.10.0.3 are filtered

Nmap done: 6 IP addresses (6 hosts up) scanned in 21.43 seconds
```

**❓ ใช้เมื่อต้องการรู้อะไร**  
-  ผลสแกนของ ชุดเป้าหมายที่คัดมาแล้ว
-  ทำซ้ำได้เรื่อย ๆ โดยไม่ต้องพิมพ์ใหม่ (workflow คุมง่าย)
-  เหมาะสำหรับสแกนเครือข่ายใหญ่ หรือทำ Pentest ที่มี scope ชัดเจน (ลูกค้าให้มาหลาย IP / domain)
  
**⚠️ ข้อควรระวัง** 
-  ระวังไฟล์มี ช่องว่างเกิน, ตัวอักษรพิเศษ → ทำให้ Nmap อ่านเพี้ยน
-  ถ้าไฟล์มีเป้าหมายเยอะมาก (เช่น subnet ใหญ่) → อาจสร้าง traffic เยอะเกินไป
    -  คุมด้วย `-T<0-5>` (timing), `--min-rate`, `--max-rate` หรือ `--min-hostgroup/--max-hostgroup`

**🛠️ วิธีป้องกันไม่ให้สแกนจน network ช้า**
-  คุมความเร็วด้วย timing option:

```python
nmap -T2 target      # ช้าลง, ปลอดภัยขึ้น
nmap -T4 target      # เร็วขึ้น, เสี่ยงรบกวนระบบ
```

-  จำกัดจำนวน packet ต่อวินาที:
  
```python
nmap --max-rate 100 target   # ไม่ส่งเกิน 100 pkt/sec
```

-  จำกัด host group:
  
```python
nmap --max-hostgroup 5 -iL hosts.txt
```
> ทำ scope agreement กับ customer เสมอ → บอกก่อนว่าจะยิง subnet ใหญ่ และอาจมี traffic เยอะ

### ✅ สรุปเทียบกัน (Target Specification) 

| Option | วิธีใช้ | จุดเด่น | ข้อควรระวัง |
|--------|---------|---------|--------------|
| `nmap {ip}` | ใส่ IP เดี่ยว เช่น `192.168.1.10` | ง่าย เหมาะกับการเจาะจง host เดียว | ใช้ไม่ได้ถ้า scope เป็น subnet/หลาย host |
| `nmap {domain}` | ใส่ชื่อโดเมน เช่น `scanme.nmap.org` | เหมาะเมื่อรู้โดเมน ไม่ต้องจำ IP, ใช้ได้กับ target ที่เปลี่ยน IP (DNS round-robin) | ถ้า DNS ช้า/ผิด → scan fail หรือได้ผลไม่ตรง |
| `nmap {subnet/cidr}` | เช่น `192.168.1.0/24` | สะดวกเมื่อสแกน subnet ทั้งย่าน, ใช้กับ LAN/VPN ได้ดี | เป้าหมายเยอะ = traffic มาก, อาจโดน IDS แจ้งเตือน |
| `nmap -iL hosts.txt` | อ่านจากไฟล์ list ที่มี IP/โดเมน/subnet | ทำซ้ำได้, จัดการ scope ใหญ่ได้ง่าย, เวิร์กโฟลว์ชัดเจน | ไฟล์เพี้ยน (space, charset) → scan fail, ถ้า list ใหญ่มากต้องคุม rate/timing |
| `nmap {subnet} --exclude {ip}` | สแกน subnet แต่ตัด host บางตัวออก เช่น `192.168.1.0/24 --exclude 192.168.1.5` | ยืดหยุ่น คุม scope ได้ละเอียด | ต้องจัดการ exclude ให้ถูก ไม่งั้น host สำคัญอาจหลุดการสแกน |


## 🔎 Host Discovery

## 1) nmap -sn {ip}

**📝 ใช้เมื่อไหร่**
-  อยากรู้ว่า เครื่องออนไลน์หรือไม่ แต่ยังไม่สนใจว่า port อะไรเปิด
-  ใช้สำหรับทำ inventory network (ใคร online บ้างใน subnet)

**⚙️ เทคนิค/กลไก**
-  Default: ส่ง ICMP Echo request (ping) + TCP SYN (half-open-scan) ไปที่ port 443 และ 80
     -  80 (HTTP) และ 443 (HTTPS) = พอร์ตที่เกือบทุกองค์กรเปิด
     -  โอกาสสูงมากที่ firewall จะ allow traffic เข้าไปที่สองพอร์ตนี้
     -  ดังนั้นการยิง SYN ไป 80/443 ช่วยเพิ่มโอกาสเจอว่า host alive
-  ถ้าได้ตอบกลับ → Host is up
-  ไม่สแกนพอร์ต เลย

**📊 ตัวอย่างผลลัพธ์**  

```python
Nmap scan report for 192.168.1.10
Host is up (0.0010s latency).
Nmap done: 1 IP address (1 host up) scanned in 0.08 seconds
```

**❓ ใช้เมื่อต้องการรู้อะไร**
-  Host นี้ออนไลน์ไหม?
-  ใช้หาจำนวน host ที่ alive ใน subnet

**📊 ตัวอย่างผลลัพธ์** 
-  ถ้า firewall block ICMP → อาจเจอว่า “Host seems down” ทั้งที่จริงเครื่องยัง alive
-  ถ้าต้องการตรวจสอบหลาย subnet ต้องคุม rate ให้ดี (`--max-rate`) ไม่งั้น network จะหน่วง


## 2) nmap -Pn {ip} (Treat all hosts as online – ข้ามขั้น host discovery)

**📝 ใช้เมื่อไหร่**
-  เป้าหมาย ไม่ตอบ ping หรือถูก firewall block ICMP แต่คุณยังอยากสแกน
-  เหมาะกับการทดสอบ service แม้ไม่รู้ว่า host ออนไลน์หรือไม่

**⚙️ เทคนิค/กลไก**
-  ปกติ Nmap จะ “ping ก่อน” เพื่อเช็คว่า host alive
-  ถ้าใช้ `-Pn` → Nmap จะ “เชื่อว่า host online แน่ ๆ” แล้วกระโดดไปสแกนพอร์ตเลย

**📊 ตัวอย่างผลลัพธ์**

```python
Nmap scan report for 203.0.113.10
Host is up.
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

**❓ ใช้เมื่อต้องการรู้อะไร**
-  ถ้า host ไม่ตอบ ping → `-Pn` ช่วยให้ยังสแกน port/service ได้

**⚠️ ข้อควรระวัง**
-  ถ้า host ปิดจริง → จะเสียเวลาสแกนทุก port ทั้งที่เครื่อง offline
-  ถ้า scope มี subnet ใหญ่และหลาย host ปิดอยู่ → จะเปลืองเวลามาก


## 3) nmap -PS80 {ip} (TCP SYN Ping – ใช้ port ที่กำหนด)

**📝 ใช้เมื่อไหร่**
-  Firewall block ICMP (ping) แต่ TCP port บางอัน (เช่น 80, 443) ยังเปิดให้ใช้
-  ใช้ตรวจว่า host online โดยอาศัยการตอบ TCP SYN/ACK

**⚙️ เทคนิค/กลไก**
-  ส่ง TCP SYN ไปที่ port 80
-  ถ้า host ตอบ SYN/ACK → Host alive
-  ถ้า host reset (RST) → แสดงว่า port ปิด แต่ host ก็ยัง alive

**📊 ตัวอย่างผลลัพธ์**

```python
Nmap scan report for 203.0.113.11
Host is up (0.028s latency).
```

**❓ ใช้เมื่อต้องการรู้อะไร**
-  ใช้ทดสอบว่า host online โดยผ่านพอร์ต service จริง เช่น web (80/443)

**⚠️ ข้อควรระวัง**
-  ต้องเลือก port ที่ “มักจะเปิด” เช่น 80, 443 ไม่งั้นอาจสรุปผิดว่า host ไม่ online
-  ถ้า firewall drop packet SYN ทั้งหมด → จะไม่เจอ host


## 4) nmap -PE {ip} (ICMP Echo Ping – แบบคล้าย ping ธรรมดา)

**📝 ใช้เมื่อไหร่**
-  เหมือน `ping` ปกติ → ใช้ตรวจสอบ host online อย่างง่าย
-  เหมาะกับ network ภายในที่ไม่ block ICMP

**⚙️ เทคนิค/กลไก**
-  ส่ง ICMP Echo Request → ถ้า host ตอบ Echo Reply → alive

**📊 ตัวอย่างผลลัพธ์**

```python
Nmap scan report for 192.168.1.5
Host is up (0.00040s latency).
```

**❓ ใช้เมื่อต้องการรู้อะไร**
-  ใช้เช็คว่า host ตอบ ICMP ไหม → alive หรือไม่

**⚠️ ข้อควรระวัง**
-  หลายองค์กร block ICMP (ping) ที่ firewall → ทำให้ `-PE` ใช้ไม่ได้
-  ไม่บอกว่า port เปิดอะไร แค่ตอบว่ามี host

### ✅ สรุปเทียบกัน (Host Discovery Options)

| Option | วิธีใช้ | จุดเด่น | ข้อควรระวัง |
|--------|---------|---------|--------------|
| `-sn`  | Ping scan, ไม่สแกนพอร์ต | เร็ว, เหมาะกับหา host ที่ alive | Firewall block ICMP → false negative |
| `-Pn`  | ข้าม discovery → ถือว่า host online | ใช้เมื่อ ICMP ถูก block | สแกน host ปิดจริง → เสียเวลา |
| `-PS80`| ใช้ TCP SYN ping ผ่านพอร์ตที่กำหนด | ดีถ้า firewall block ICMP แต่เปิด TCP (TCP = โปรโตคอลที่ใช้โดย service พื้นฐาน) | ถ้าเลือก port ที่ไม่เปิด → ผลผิดพลาด |
| `-PE`  | ใช้ ICMP echo (เหมือน ping) | ง่าย, เร็วใน LAN | ใช้ไม่ได้ถ้า firewall block ICMP |


## 🚪 Scan Techniques

## 1) nmap -sS {ip} (TCP SYN Scan – Half-open scan)

**📝 ใช้เมื่อไหร่**
-  ใช้บ่อยที่สุดใน Pentest/Red Team เพราะเร็วและ stealth
-  เหมาะเมื่อเรามีสิทธิ์ root/admin บนเครื่องที่รัน Nmap
-  ต้องการสแกนพอร์ตเยอะ ๆ โดยไม่อยากสร้าง connection จริง

**⚙️ เทคนิค/กลไก**
-  ส่ง TCP SYN ไปที่พอร์ต → ถ้า:
   -  ได้ SYN/ACK → port **open**
   -  ได้ RST → port **closed**
   -  ไม่ตอบ/ICMP unreachable → port **filtered**
-  ไม่ส่ง ACK กลับ → การเชื่อมต่อไม่เสร็จสมบูรณ์ (half-open)

**📊 ตัวอย่างผลลัพธ์**

```python
Nmap scan report for 192.168.1.10
Host is up (0.0010s latency).
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

**❓ ใช้เมื่อต้องการรู้อะไร**
-  หาว่าพอร์ตไหนเปิด/ปิด/ถูกกรอง
-  ตรวจสอบ service ที่ TCP ฟังอยู่
  
**⚠️ ข้อควรระวัง**
-  ต้องรันด้วย root/admin
-  Firewall/IDS ยังตรวจจับได้ว่าเป็นการสแกน

## 2) nmap -sT {ip} (TCP Connect Scan – Full connect)

**📝 ใช้เมื่อไหร่**
-  ใช้ได้เมื่อเรา ไม่มีสิทธิ์ root (เช่น user ธรรมดา)
-  ต้องการสแกนง่าย ๆ ใช้ system call `connect()`

**⚙️ เทคนิค/กลไก**
-  ใช้ TCP 3-way handshake ปกติ:
     -  SYN → SYN/ACK → ACK
- ทำ connection เสร็จจริงแล้วค่อยปิด

**📊 ตัวอย่างผลลัพธ์**

```python
Nmap scan report for 203.0.113.11
Host is up (0.028s latency).
PORT   STATE SERVICE
3306/tcp open  mysql
```

**❓ ใช้เมื่อต้องการรู้อะไร**
-  ตรวจสอบ service TCP ที่เปิด โดยไม่ต้องใช้สิทธิ์สูง

**⚠️ ข้อควรระวัง**
-  ช้ากว่า `-sS`
-  noisy: server log จะเห็นการ connect ทุกครั้ง

## 3) nmap -sA {ip} (TCP ACK Scan)

**📝 ใช้เมื่อไหร่**
-  ต้องการตรวจสอบ firewall rules
-  เช็คว่า firewall block packet หรือไม่

⚙️ เทคนิค/กลไก
-  ส่ง TCP ACK → ถ้า:
    -  ได้ RST → unfiltered (firewall ไม่ block ACK)
    -  ไม่ตอบ/ICMP unreachable → filtered

📊 ตัวอย่างผลลัพธ์

```python
Nmap scan report for 192.168.1.20
Host is up (0.030s latency).
All 1000 scanned ports on 192.168.1.20 are unfiltered
```

❓ ใช้เมื่อต้องการรู้อะไร
-  Firewall filter traffic ยังไง
-  Port “reachable” ไหม (แต่ไม่ได้บอกว่า open/closed)

⚠️ ข้อควรระวัง
-  ไม่ได้บอกว่า port เปิดหรือปิด แค่ firewall block หรือไม่
-  เหมาะกับ recon firewall มากกว่าการหาบริการจริง

## 4) nmap -sU {ip} (UDP Scan)

**📝 ใช้เมื่อไหร่**
-  ใช้ตรวจสอบบริการ UDP เช่น DNS (53), SNMP (161), DHCP (67/68), TFTP (69)
-  เหมาะกับงาน Internal Pentest ที่มักเจอ UDP service

**⚙️ เทคนิค/กลไก**
-  ส่ง UDP datagram → ถ้า:
     -  ได้ ICMP port unreachable → closed
     -  ได้ response → open
     -  เงียบ → open|filtered

**📊 ตัวอย่างผลลัพธ์**

```python
Nmap scan report for 10.10.0.2
Host is up (0.001s latency).
PORT    STATE         SERVICE
53/udp  open          domain
161/udp open|filtered snmp
```

**❓ ใช้เมื่อต้องการรู้อะไร**
-  บริการ UDP ที่มักจะซ่อนจากการสแกน TCP

**⚠️ ข้อควรระวัง**
-  ช้ามาก เพราะ UDP ไม่มี handshake
-  Firewall มัก drop → เกิด false positive

## 5) nmap -sN / -sF / -sX {ip} (TCP Null, FIN, Xmas scans)

**📝 ใช้เมื่อไหร่**
-  ใช้เทคนิค stealth หรือ evasion
-  ทดสอบระบบปฏิบัติการที่ stack ตาม RFC 793

**⚙️ เทคนิค/กลไก**
-  ส่ง packet แปลก:
     -  Null: ไม่มี flag
     -  FIN: แค่ FIN
     - Xmas: FIN + URG + PSH
-  ถ้า host ตอบ RST → closed
-  ถ้าไม่ตอบ → อาจ open|filtered

**📊 ตัวอย่างผลลัพธ์**

```python
Nmap scan report for 10.10.0.3
Host is up (0.00090s latency).
PORT    STATE         SERVICE
25/tcp  open|filtered smtp
```

**❓ ใช้เมื่อต้องการรู้อะไร**
-  หาช่องโหว่ firewall / IDS เก่า ๆ
-  ใช้กับระบบที่ไม่ได้ harden TCP/IP stack

**⚠️ ข้อควรระวัง**
-  ไม่ reliable กับ Windows/OS ใหม่ ๆ
-  มักใช้เพื่อ evasion เฉพาะทาง

### ✅ สรุปเทียบกัน (Scan Techniques)

| Option          | วิธีใช้                               | จุดเด่น                                 | ข้อควรระวัง |
|-----------------|---------------------------------------|------------------------------------------|--------------|
| `-sS` (SYN)     | ส่ง SYN, ตัดสินจาก SYN/ACK หรือ RST  | เร็ว, stealth, ใช้บ่อยสุด               | ต้อง root, firewall ยัง detect ได้ |
| `-sT` (Connect) | ใช้ system call `connect()` จบ handshake | ใช้ได้แม้ไม่ใช่ root, เข้าใจง่าย        | ช้า, noisy (server log จะเห็น) |
| `-sA` (ACK)     | ส่ง ACK, ดูว่า firewall block หรือไม่ | ใช้ตรวจ firewall rules                  | ไม่บอก port เปิด/ปิด |
| `-sU` (UDP)     | ส่ง UDP, ตีความจาก response/ICMP     | เจอบริการ UDP ที่ TCP ไม่เห็น            | ช้า, false positive บ่อย |
| `-sN/-sF/-sX`   | ส่ง TCP packet แปลก (Null, FIN, Xmas) | เทคนิค stealth, หลบ firewall บางตัว      | ไม่ reliable บน Windows/OS ใหม่ |


## 🎯 Port Specification

## 1) nmap -p 22 {ip}

**📝 ใช้เมื่อไหร่**
-  อยากสแกนพอร์ตเฉพาะเจาะจง เช่น SSH (22)
-  เรารู้เป้าหมายว่าเปิด service ที่พอร์ตไหนแน่นอน

**⚙️ เทคนิค/กลไก**
-  `-p <port>` → ระบุพอร์ตเดี่ยว
-  Nmap จะสแกนเฉพาะพอร์ตที่กำหนด

**📊 ตัวอย่างผลลัพธ์**

```python
Nmap scan report for 192.168.1.10
Host is up (0.0010s latency).

PORT   STATE SERVICE
22/tcp open  ssh
```

**❓ ใช้เมื่อต้องการรู้อะไร**
-  ตรวจสอบ service เจาะจงว่ามีอยู่หรือไม่ (เช่น ssh)

**⚠️ ข้อควรระวัง**
-  ถ้า scope กว้างเกินไปแล้วเลือกแค่บางพอร์ต อาจพลาด service อื่นที่เปิดอยู่

## 2) nmap -p 1-65535 {ip}

**📝 ใช้เมื่อไหร่**
-  ต้องการสแกนทุกพอร์ต TCP (full port scan)
-  ใช้ในกรณีต้องการเก็บข้อมูลแบบละเอียดสุด

**⚙️ เทคนิค/กลไก**
-  `-p <range>` → ระบุช่วงพอร์ต
-  ตัวอย่าง: `-p 1-65535` → สแกน TCP ทุกพอร์ต

**📊 ตัวอย่างผลลัพธ์**

```python
Nmap scan report for 192.168.1.20
Host is up (0.0012s latency).
Not shown: 65533 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
443/tcp  open  https
3306/tcp open  mysql
```

**❓ ใช้เมื่อต้องการรู้อะไร**
-  มั่นใจว่าไม่ได้พลาดพอร์ตที่ซ่อนอยู่

**⚠️ ข้อควรระวัง**
-  ใช้เวลานานมาก
-  อาจสร้าง traffic เยอะ → ถูก IDS/Firewall แจ้งเตือน

## 3) nmap -F {ip} (Fast mode)

**📝 ใช้เมื่อไหร่**
-  ต้องการสแกนเร็ว ๆ เฉพาะ top ports ที่ใช้บ่อย
-  เหมาะกับ reconnaissance ช่วงแรก

**⚙️ เทคนิค/กลไก**
-  `-F` = fast scan
-  สแกนประมาณ 100 พอร์ตที่พบบ่อยที่สุด (อ้างอิงไฟล์ `nmap-services`)

**📊 ตัวอย่างผลลัพธ์**

```python
Nmap scan report for 10.10.0.5
Host is up (0.00090s latency).
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

**❓ ใช้เมื่อต้องการรู้อะไร**
-  เห็นภาพรวมเร็ว ๆ ของ service หลัก

**⚠️ ข้อควรระวัง**
-  อาจพลาด service ที่เปิดบน uncommon port (เช่น 8443, 3389)

## 4) nmap --top-ports <N> {ip}

**📝 ใช้เมื่อไหร่**
-  อยากโฟกัสแค่ N พอร์ตยอดนิยม
-  เหมาะกับ scanning เร็ว ๆ

**⚙️ เทคนิค/กลไก**
-  `--top-ports <N>` → ใช้สถิติ popularity ของ Nmap
-  ตัวอย่าง: `--top-ports 20`

**📊 ตัวอย่างผลลัพธ์**

```python
Nmap scan report for 192.168.1.30
Host is up (0.0020s latency).
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
443/tcp open  https
```

**❓ ใช้เมื่อต้องการรู้อะไร**
-  ใช้เพื่อหา service ที่น่าจะเปิดมากที่สุดก่อน

**⚠️ ข้อควรระวัง**
-  ไม่ครอบคลุม uncommon port
-  อาจไม่พอถ้าเป้าหมายซ่อน service ไว้

### ✅ สรุปเทียบกัน (Port Specification)

| Option            | วิธีใช้                        | จุดเด่น                  | ข้อควรระวัง |
|-------------------|--------------------------------|---------------------------|--------------|
| `-p 22`           | สแกนพอร์ตเดียว                | เร็ว, เจาะจง             | พลาด service อื่นที่ไม่ได้ระบุ |
| `-p 1-65535`      | สแกนทุกพอร์ต TCP              | ครอบคลุม, ไม่พลาด       | ช้า, traffic เยอะ |
| `-p U:...,T:...,S:...` | สแกนหลายโปรโตคอลพร้อมกัน   | เจาะลึก TCP+UDP+SCTP     | ช้า, noisy |
| `-F`              | สแกน top 100 ports             | เร็ว, เหมาะ recon        | พลาด uncommon port |
| `--top-ports N`   | เลือก top N ports              | ยืดหยุ่น, scan เร็ว       | ไม่ครอบคลุม uncommon port |


## ⚙️ Service & Version Detection

## 1) nmap -sV {ip}

**📝 ใช้เมื่อไหร่**
-  ใช้เพื่อระบุว่า service ที่รันบนพอร์ตคืออะไร เช่น SSH, HTTP, MySQL
-  ต้องการรู้เวอร์ชันของ service เพื่อเช็คช่องโหว่ (CVE) ต่อได้
-  Pentest ส่วนใหญ่ต้องมีขั้นตอนนี้หลังจากหา open ports แล้ว

**⚙️ เทคนิค/กลไก**
-  Nmap จะส่ง probe หลายรูปแบบไปยังพอร์ตที่เปิด
-  เปรียบเทียบการตอบกลับกับฐานข้อมูล `nmap-service-probes`
-  บอกชื่อ service + version (ถ้า match)
-  ใช้ร่วมกับ `-p` เพื่อเจาะพอร์ตที่สนใจได้

**📊 ตัวอย่างผลลัพธ์**

```python
Nmap scan report for 203.0.113.10
Host is up (0.031s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.7
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
3306/tcp open  mysql   MySQL 5.7.38
```

**❓ ใช้เมื่อต้องการรู้อะไร**
-  ชื่อและเวอร์ชันของ service ที่เปิดอยู่บนพอร์ต
-  ใช้เป็นจุดเริ่มหา exploit (เช่น `searchsploit apache 2.4.29`)

**⚠️ ข้อควรระวัง**
-  อาจ noisy เพราะ probe เยอะ → IDS/IPS detect ได้ง่าย
-  Service บางตัวอาจตอบกลับ misleading → version อาจไม่แม่น 100%

## 2) nmap --version-intensity <0-9> {ip}

**📝 ใช้เมื่อไหร่**
-  ใช้ควบคุม “ความเข้มข้น” ของการตรวจ version
-  ถ้าอยากเร็วและเบา → intensity ต่ำ
     -  เร็ว (Fast) → ใช้เวลาไม่นาน เพราะ Nmap ส่ง probe น้อย (เช่น แค่ HTTP GET ธรรมดาไปที่ port 80)
     -  เบา (Light) → ไม่สร้าง traffic แปลก ๆ เยอะ → โอกาสโดน IDS/Firewall ตรวจจับน้อยกว่า
     -  👉 ผลลัพธ์: ได้ข้อมูลพื้นฐาน เช่น “นี่คือ Apache httpd” แต่ไม่บอกว่า เวอร์ชันเท่าไร หรือรายละเอียดลึก
-  ถ้าอยากละเอียดสุด → intensity สูง

📈 Intensity ต่ำ (0–3)
-  ส่ง probe แค่ไม่กี่แบบ → เช่น probe “ทั่วไป”
-  ข้อดี:
    -  สแกนเร็ว
    -  Traffic ไม่เยอะ → stealth กว่า
-  ข้อเสีย:
    -  อาจรู้แค่ service กว้าง ๆ เช่น “http” แต่ไม่รู้ว่า Apache/NGINX/IIS
    -  อาจพลาด version number

📊 Intensity สูง (7–9)
-  ส่ง probe หลากหลาย (แม้แต่ probe แปลก ๆ)
-  ข้อดี:
     -  ระบุ service ได้ละเอียด เช่น Apache httpd 2.4.29 ((Ubuntu))
     -  เห็น banner หรือ response ที่ละเอียดมาก
-  ข้อเสีย:
     -  ช้า เพราะส่ง probe หลายสิบแบบ
     -  เสียง่าย (Noisy) = IDS/Firewall เห็น traffic แปลก ๆ เช่น HTTP request แปลก, malformed packet, unusual banner check → ทำให้ SOC จับได้ว่า “นี่คือการ fingerprinting”

**⚙️ เทคนิค/กลไก**
-  ค่า 0 → ส่ง probe เบาสุด (เร็ว แต่ไม่ครอบคลุม)
-  ค่า 9 → ส่ง probe ทุกแบบที่มี (ครอบคลุม แต่ช้า/เสียง่าย)
-  ค่า default = 7

> 🔊 “เสียง่าย” (Noisy) หมายถึงอะไร: เวลาเรา scan แบบ intensity สูง → Nmap ยิง probe จำนวนมากและ “ไม่เป็นธรรมชาติ”
> Firewall/IDS มักมี rule ว่า:

ถ้าเจอ request แปลก ๆ ที่ไม่ใช่ traffic ปกติ เช่น probe SMTP แต่อยู่บน port 80 → แจ้งเตือน
ถ้าเจอ packet scan pattern จำนวนมาก → แจ้งเตือนว่า port scan

-  นั่นหมายถึง defender จะเห็นคุณแน่ ๆ

**📊 ตัวอย่างผลลัพธ์ (Intensity ต่ำ (0–3))**

```python
nmap -sV --version-intensity 2 203.0.113.10
```

```python
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd
```
(รู้แค่ “http” แต่ไม่รู้ว่า Apache/NGINX/เวอร์ชันไหน)

**📊 ตัวอย่างผลลัพธ์ (Intensity สูง (7–9))**

```python
nmap -sV --version-intensity 9 203.0.113.10
```

```python
80/tcp open  http  Apache httpd 2.4.29 ((Ubuntu))
```
(รู้ละเอียดว่า Apache รุ่นไหน + OS hint)

**❓ ใช้เมื่อต้องการรู้อะไร**
-  ปรับสมดุลระหว่าง speed กับ accuracy

**⚠️ ข้อควรระวัง**
-  Intensity สูง = scan ช้า + noisy
-  Intensity ต่ำ = อาจไม่เจอ version ที่แท้จริง







































