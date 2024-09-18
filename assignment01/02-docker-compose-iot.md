# IoT Docker compose

## How to start docker compose
1. ติดตั้ง rufas
2. ติดตั้ง ubantu iso ของ version 22.04.xx
3. ใช้ rufas burn flashrdrive ให้ กลายเป็น boot device
4. เข้า bios ในเครื่อง dell 5000
5. เปลี่ยน boot privority ให้ boot ผ่าน flashdrive sandisk ก่อนเสมอ
6. กด save config ใน bios
7. เครื่องจะ restart เอง
8. Chose try or install buntu Server
9. Chose longuage..
10. Chose ubuntu server(minimize)
11. Confie my ip address ต้องไม่ซ้ำกัน
12. Chose custom storage layout
13. Delete old doto (parsition)
14. เอา free space ที่มีอยู่มาตั้งเป็น swap ขนาด 1GB ชนิดข้อมูลเเบบ ext4
15. เอาที่ว่างที่เหลือทั้งหมดมาทำเป็น /
16. กด continue
17. ตั้ง username password ตั้งชื่อเครื่อง ตั้งชื่อเจ้าของเครื่อง
18. หลัง download เสร็จ เลือก Reboot now
19. login user-password ใน terminal
20. ดูพื้นที่เก็บในไดร์ฟ ด้วยคำสั่ง df -h
21. ติดตั้ง text editor ที่ชื่อว่า nano ด้วยคำสั่ง sudo apt-get install nano -y
22. cd/etc/netplan
25. sudo nano 00-network-config.yaml  # เพื่อเเก้ configuration ของ network ให้เป็นเเบบ static
เเละเลือก domain name server เป็นของ รร เเล้วก็ตั้ง domain สำรองเป็นของ 8.8.8.8 (google DNS)
24. ctrl + s เพื่อ save
25. ctrl + x เพื่อ exit
26. sudo netplan apply
27. sudo reboot now 
28. ติดตั้ง github
29. ติดตั้ง docker
30. clone git hub ของอาจารย์มาลง
31. docker compose up

## Error we founud
1. kafka ไม่ start
2. kafka error 404

## How to solve the problems.
1. docker compose restart kafka
2. docker compose log -f |egrep 'WARN|ERR' (ใช้สำหรับดู logs จาก Docker Compose ที่มีข้อความที่มีคำว่า "WARN" หรือ "ERR" โดยใช้คำสั่ง egrep เพื่อกรองเฉพาะบรรทัดที่มีข้อความเหล่านี้อยู่เท่านั้น เช่น คำสั่งที่ให้มาจะทำการเรียกดู logs แบบ real-time (-f) และกรองเฉพาะข้อความที่มี "WARN" หรือ "ERR" ด้วย 'egrep'.)

# ภาพรวมของบริการใน Docker Compose

## ZooKeeper
- **วัตถุประสงค์:** ให้บริการการจัดเก็บการกำหนดค่าที่กระจาย, การตั้งชื่อ, การซิงโครไนซ์, และบริการกลุ่มสำหรับคลัสเตอร์ Kafka
- **พอร์ต:** 2181
- **ข้อมูลที่เก็บ:** `/var/lib/zookeeper/data` และ `/var/lib/zookeeper/log`
- **การตั้งค่า:** ออกแบบให้ "fail-fast" และจะรีสตาร์ทอัตโนมัติเมื่อหยุดทำงาน

## Kafka
- **วัตถุประสงค์:** แพลตฟอร์มสตรีมมิ่งแบบกระจายสำหรับสร้างท่อข้อมูลและแอปพลิเคชันสตรีมมิ่งแบบเรียลไทม์
- **การเชื่อมต่อกับ ZooKeeper:** ใช้พอร์ต 9092
- **คุณสมบัติ:** รองรับการบีบอัดข้อมูลและการสร้างหัวข้ออัตโนมัติ

## Kafka REST Proxy
- **วัตถุประสงค์:** ให้ส่วนต่อประสาน RESTful กับคลัสเตอร์ Kafka เพื่อให้ง่ายต่อการผลิตและบริโภคข้อความ
- **พอร์ต:** 8082
- **การเชื่อมต่อ:** ใช้ ZooKeeper และ Kafka สำหรับการเชื่อมต่อเบื้องต้น

## Kafka Connect
- **วัตถุประสงค์:** เฟรมเวิร์กสำหรับเชื่อมต่อ Kafka กับระบบภายนอก เช่น ฐานข้อมูลและระบบไฟล์
- **พอร์ต:** 8083
- **การตั้งค่า:** ใช้หัวข้อใน Kafka สำหรับการจัดเก็บข้อมูลการกำหนดค่าและสถานะ

## Mosquitto (MQTT Broker)
- **วัตถุประสงค์:** โบรกเกอร์ MQTT แบบโอเพ่นซอร์สที่รองรับการสื่อสารแบบ publish/subscribe เหมาะสำหรับอุปกรณ์ IoT
- **พอร์ต:** 1883 และ 9001

## MongoDB
- **วัตถุประสงค์:** ฐานข้อมูล NoSQL ที่จัดเก็บข้อมูลในรูปแบบเอกสาร
- **การตั้งค่า:** กำหนดค่าในไฟล์ `.env` สำหรับการยืนยันตัวตน

## Grafana
- **วัตถุประสงค์:** แพลตฟอร์มการแสดงข้อมูลสำหรับสร้างแดชบอร์ดเพื่อตรวจสอบและแสดงข้อมูลแบบเรียลไทม์
- **พอร์ต:** 8085
- **ปลั๊กอิน:** รองรับปลั๊กอินเพิ่มเติม เช่น Grafana Clock, Worldmap, และ Piechart

## Prometheus
- **วัตถุประสงค์:** ระบบสำหรับรวบรวมเมตริกในฐานข้อมูลแบบ time-series และแจ้งเตือนตามการเปลี่ยนแปลงของเมตริก
- **พอร์ต:** 8086
- **การตั้งค่า:** ใช้ไฟล์การกำหนดค่า Prometheus สำหรับการตั้งค่าเมตริก

## IoT Processor
- **วัตถุประสงค์:** ประมวลผลข้อมูลจากเซนเซอร์ IoT
- **พอร์ต:** 8080
- **การเชื่อมต่อ:** ขึ้นอยู่กับ Kafka Connect

บริการเหล่านี้ทำงานร่วมกันเพื่อสร้างสภาพแวดล้อมสำหรับการสตรีมและตรวจสอบข้อมูลแบบเรียลไทม์ โดยมีการเชื่อมต่อระหว่างบริการผ่านพอร์ตต่างๆ ซึ่งสามารถปรับแต่งได้ตามต้องการ


เวลาสตาร์ทบริการต้องไปรันไฟล์ `start.sh` ทั้ง 4 ตัว เรียงกัน โดยต้องเปิด terminal ทิ้งไว้ด้วย
<img src="./4screen.jpg">
