# MQTT

![mqtt plan](../img/partNetwork/Generic-operation-scheme-of-the-MQTT-protocol.png)

# Ingest and store real-time data from IoT sensors 
> อธิบาย 3 ส่วนนี้ สร้างมาได้อย่างไร

## iot-sensor-1
> คือ IoT sensor 1 ซึ่งเป็นเซนเซอร์ที่ถูกจำลองด้วยไมโครเซอร์วิสใน Spring Boot โดยใช้ไลบรารี Eclipse Paho MQTT เซนเซอร์นี้ส่งข้อมูล telemetry ไปยังโบรกเกอร์ Eclipse Mosquitto ข้อมูลใน payload จะถูกสร้างขึ้นทุกวินาทีจาก Callable และมีรูปแบบ payload ที่กำหนดไว้ล่วงหน้าเพื่อให้ตรงกัน 

## iot-sensor-2
> เซนเซอร์ที่ถูกจำลองด้วยไมโครเซอร์วิสใน Spring Boot โดยใช้ไลบรารี Eclipse Paho MQTT เช่นเดียวกับ IoT sensor 1 แต่เซนเซอร์นี้ติดตั้งอยู่ในเครื่องของสมาชิกในทีม

## iot-sensor-3-10
> sensor-3 ซึ่งเป็นเซนเซอร์ที่ใช้ค่าจริงจาก Cucumber RS ของกลุ่มตนเอง แตกต่างจาก iot-sensor-1 และ iot-sensor-2 ที่เป็นการจำลอง (mock-up) ส่งผ่าน MQTT โดย payload ถูกตั้งให้อยู่ในรูปแบบเดียวกัน สำหรับการใช้งานร่วมกันกับเซนเซอร์อื่นๆ เซนเซอร์ 4-10 ก็เป็นค่าจริงจากเซนเซอร์ของกลุ่มอื่น ที่อ่านค่าจาก Cucumber RS และส่งผ่าน MQTT ด้วย payload ที่มีรูปแบบเดียวกัน เพื่อแสดงผลพร้อมกัน 10 เซนเซอร์ใน Cucumber.

## หลักการทำงานคร่าวๆ ของ MQTT มีดังนี้
- MQTT Broker
- MQTT Clients
- MQTT Topics
- MQTT Payload
- MQTT Actors

### MQTT Broker 
ทำหน้าที่เหมือนกับนายหน้าขายของ Amway แต่ในที่นี้คือจะเป็นนายหน้าจะคอยจัดการระบบ `pipeline` ซึ่งระบบนี้มันไม่เหมือน RESTful API มันจะส่ง data เป็นสาย (streaming data) broker ทุกตัวจะบริหารจัดการท่อ pipeline ให้ส่งข้อมูล stream ข้อมูลมหาศาลได้ 

### MQTT Clients
คือตัวลูกข่ายที่จะต้อง `subscribe topic` และรับข้อมูลมาจาก topic นั้นๆ แบบรับเป็นสายข้อมูล streamdata เช่น รับอุณหภูมิ มาอย่างต่อเนื่อง เพื่อจะเอามาแสดงในหน้า dashboard

### MQTT Topics
เป็นหัวข้อเรื่อง ให้ actors มา `subscribe` และ public ข้อมูลลงแบบ realtime ให้ client มา subscribe แล้วดึงข้อมูลออกมาแบบ realtime เพื่อมาแสดงข้อมูลลงหน้า dashboard

### MQTT Payload
ข้อมูลที่ถูกส่งไปใน pipeline

### MQTT Actors
ตามจริงเรียกได้หลายแบบ แต่หลักๆ เลย มันจะเป็นตัวที่ `subscribe topic` และ `publish data` ลงไปใน topic นั้นๆ 

แหล่งข้อมูลเพิ่มเติม

[YouTube Video](https://www.youtube.com/watch?v=jTeJxQFD8Ak&list=PLRkdoPznE1EMXLW6XoYLGd4uUaB6wB0wd)
