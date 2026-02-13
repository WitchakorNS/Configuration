# Ubuntu Netplan: ตั้งค่า IP / Gateway / DNS (Lab)

เอกสารนี้สรุปขั้นตอนการตั้งค่า Network บน Ubuntu (ผ่าน Netplan) เพื่อให้ `ping 8.8.8.8` ผ่าน

---

## อ้างอิงเครื่องมือ/ลิงก์
- IP Subnet Calculator: https://www.calculator.net/ip-subnet-calculator.html
- วิดีโออ้างอิง: https://youtu.be/tHa24FxzrRQ

---

## ข้อมูลที่ต้องมี (ก่อนเริ่ม)
ให้เตรียมค่าต่อไปนี้จากรูป/โจทย์ของคุณ:
- **IP เครื่องเรา** (เช่น `192.168.0.9/24`)
- **Subnet mask / CIDR** (เช่น `/24`)
- **Gateway** (เช่น `192.168.0.1` = First usable host IP)
- **DNS** (เช่น `8.8.8.8`)

> ตัวอย่างจากโจทย์  
> - Network Address = `192.168.0.9/24`  
> - Broadcast Address = `192.168.0.255/24`

---

## ขั้นตอนการทำ

### 1) เปิด Web Console (แท็บใหม่)
1. คลิกที่เครื่อง (คอม)  
2. ไปที่ **Web console in new tab**

---

### 2) เข้า Linux Console
ใน console ดำ กด `Enter` เพื่อเข้า command line

- user: `ubuntu`  
- password: `ubuntu`

---

### 3) ทดสอบ Ping (ก่อนตั้งค่า)
ลองรัน:
```bash
ping 8.8.8.8
```

ถ้าขึ้น `Unreachable` → ไปตั้งค่า IP ตามขั้นตอนด้านล่าง

---

### 4) แก้ Netplan Config
แก้ไฟล์:
```bash
sudo nano /etc/netplan/50-cloud-init.yaml
```

> พอรัน `sudo` ระบบจะให้ใส่รหัสผ่าน: `ubuntu`

#### 4.1 (สำคัญ) ปิดการเขียนทับจาก cloud-init (ถ้าโดนรีเซ็ตค่าเอง)
สร้าง/แก้ไฟล์นี้:
```bash
sudo nano /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
```

ใส่เนื้อหา:
```yaml
network: {config: disabled}
```

#### 4.2 ตัวอย่าง Netplan YAML (ปรับให้ตรงกับ IP ของคุณ)
> **หมายเหตุ**: ห้ามใช้ `//` เป็นคอมเมนต์ใน YAML จริง ๆ  
> ถ้าจะคอมเมนต์ ให้ใช้ `#` แทน

ตัวอย่างโครง (ให้แก้ `addresses` และ `via`):
```yaml
network:
  ethernets:
    ens3:
      addresses:
        - 192.168.0.9/24   # IP เครื่องเรา/Prefix
      nameservers:
        addresses:
          - 8.8.8.8        # DNS
        search: []
      routes:
        - to: default
          via: 192.168.0.1 # Gateway (First usable host)
  version: 2
```

> ถ้าของคุณเป็นอีกวง เช่น `10.1.1.0/24` ก็เปลี่ยนให้ตรงกับรูปโจทย์  
> - `addresses: - 10.1.1.251/24`  
> - `via: 10.1.1.1`

---

### 5) เซฟและออกจาก nano
- กด `Ctrl + O` เพื่อเซฟ
- กด `Enter` ยืนยันชื่อไฟล์
- กด `Ctrl + X` เพื่อออก

---

### 6) Apply ค่า Netplan
```bash
sudo netplan apply
```

---

### 7) ทดสอบ Ping (หลังตั้งค่า)
```bash
ping 8.8.8.8
```

ถ้า ping ได้ = ผ่าน ✅

---

## Troubleshooting (ถ้ายัง ping ไม่ได้)
- เช็กว่า **Gateway** ถูกวง/ถูก IP (มักเป็น `.1` ของวงนั้น)
- เช็กว่า prefix ถูกต้อง เช่น `/24`
- ลองดูสถานะ:
  ```bash
  ip a
  ip r
  systemd-resolve --status || resolvectl status
  ```
- ถ้ารีบแก้แบบปลอดภัยก่อน apply จริง:
  ```bash
  sudo netplan try
  ```

---

## Classroom Codes
> เก็บไว้ตามที่ให้มา


