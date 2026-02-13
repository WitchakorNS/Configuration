Config
https://www.calculator.net/ip-subnet-calculator.html
1. Click ที่คอม ไปที่ Web console in new tab
( ใส่ subnet mask ด้วย เช่น
Network Address = 192.168.0.9 /24
Broadcast Address = 192.168.0.255 /24 )

2. console ดำกด enter เข้า command Linux
user : ubuntu
password : ubuntu

3. ลอง ping 8.8.8.8 จะขึ้น Unreachable
config IP ใช้   sudo nano /etc/netplan/50-could-init.yaml
ใช้คำสั่งเสด จะขึ้นให้ใส่รหัส ubuntu (password: ubuntu) 

# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
  ethernets:
    ens3:
      addresses:
        - 10.1.1.251/24 // IP เครื่องเรา แก้อันนี้ตามรูป คอมมึงอะ เช่น 192.168.0.9 /24
      nameservers:
        addresses:
          - 8.8.8.8 // Address DNS
        search: []
      routes:
        - to: default
          via: 10.1.1.1 // Gate way แก้อันนี้ด้วย เช่น 192.168.0.1 First Usable Host IP
version: 2

กด ctrl + O เพื่อเซฟ
กด ctrl + X เพื่อออก
แล้วก็ใช้ sudo netplan apply

4. ping 8.8.8.8 
ping ได้ ก็ผ่านแล้ว

https://youtu.be/tHa24FxzrRQ
