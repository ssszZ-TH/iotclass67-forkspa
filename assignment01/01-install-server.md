# Install Server and Docker

## How to Install Server

ขั้นตอนการติดตั้งเซิร์ฟเวอร์ในอุปกรณ์ Dell Server:

1. **เตรียมอุปกรณ์:** เตรียม Dell Server, จอภาพ, เมาส์, คีย์บอร์ด, สาย LAN และแฟลชไดร์ฟที่มีไฟล์ติดตั้ง Ubuntu Server
2. **ดาวน์โหลดไฟล์ติดตั้ง:** ดาวน์โหลดไฟล์ติดตั้ง Ubuntu Server เวอร์ชัน 22.04 จากเว็บไซต์ [Ubuntu](https://ubuntu.com/download/server) และบันทึกลงในแฟลชไดร์ฟ
3. **บูตจากแฟลชไดร์ฟ:** เปิดเครื่อง Dell Server และบูตจากแฟลชไดร์ฟ (กด F2 เพื่อเข้าสู่เมนูบูต) จากนั้นทำตามขั้นตอนการติดตั้ง Ubuntu Server ซึ่งรวมถึงการตั้งค่าพื้นที่เก็บไฟล์, ผู้ใช้, รหัสผ่าน และการติดตั้งไดรเวอร์ที่จำเป็น
4. **ตรวจสอบการติดตั้ง:** หลังจากการตั้งค่าเสร็จสิ้น ระบบจะทำการติดตั้ง Ubuntu Server เมื่อเสร็จแล้วให้รีสตาร์ทเครื่องและตรวจสอบว่า Ubuntu Server ทำงานได้ตามปกติ

## How to Install Docker

1. **เข้าสู่ระบบ:** เข้าสู่ระบบ Dell Server ด้วยชื่อผู้ใช้และรหัสผ่านที่ตั้งไว้
2. **ตรวจสอบการเชื่อมต่ออินเทอร์เน็ต:** ใช้คำสั่ง `sudo apt update` เพื่อตรวจสอบการเชื่อมต่ออินเทอร์เน็ต หากยังไม่ได้เชื่อมต่อ ให้ตั้งค่าอินเทอร์เน็ตแบบบริดจ์ โดยตั้งค่า IPV4 และเมตริกของ WiFi และ Ethernet ให้เป็น 15
3. **ติดตั้ง Docker:** ใช้คำสั่งต่อไปนี้เพื่อติดตั้ง Docker

    ```bash
    sudo apt update
    sudo apt install apt-transport-https ca-certificates curl software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt update
    apt-cache policy docker-ce
    sudo apt install docker-ce
    sudo systemctl status docker
    ```

4. **ติดตั้งเครื่องมือเพิ่มเติม:** ติดตั้งเครื่องมือที่จำเป็นด้วยคำสั่ง

    ```bash
    sudo apt update
    sudo apt upgrade
    sudo apt-get install net-tools
    sudo apt-get install vim
    ```

## การติดตั้ง Ubuntu แบบ Custom Installation

สำหรับการติดตั้ง Ubuntu แบบ Custom:

1. **เลือกพื้นที่ Swap:** ตั้งค่า Swap Area เป็น 1 GB สำหรับ RAM
2. **ตั้งค่า Root (/):** ใช้พื้นที่ที่เหลือ 63 GB สำหรับ Root (/)
3. **ดำเนินการตามขั้นตอนเดิม:** ทำตามขั้นตอนการติดตั้งเดิมเพื่อเสร็จสิ้นการติดตั้ง
