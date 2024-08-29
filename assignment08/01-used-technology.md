# Used technology

software
- [Docker](https://www.docker.com/)
- [Docker Compose](https://docs.docker.com/compose/)
- [Ubuntu Server](https://ubuntu.com/)
- [Docker Hub](https://hub.docker.com/)
- [Git](https://git-scm.com/)
- [Github](https://github.com/)

iot
- cucumber (คือ esp32 ที่คนไทย เอามา mod)
- sensor เเสง อุณหภูมิ ความชื้น เเละ มี internet ในตัว สามารถ sync time กับ google ได้
- bread board สำหรับต่อ sensor รับเเสง (sensor เเสงไม่มีในตัวมันเอง)

software ตัวหลักๆ
- mqtt คือ protocol หลักที่ใช้
- prometius ไว้ monitoring data เเบบ realtime ไว้ป้องกัน error ที่จะเกิดขึ้น หรืออาจจะไว้ดู data ที่ใหล flow เล่นๆ ก็ทำได้เช่นกัน
- grafana เป็น auto dashboard ที่ใช้ angular base ข้อดีคือไม่ต้องเขียน code frontend เอง ข้อเสียคือติดตั้งยากมากๆ
