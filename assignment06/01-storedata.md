ในส่วนนี้จะอธิบายถึงขั้นตอนการเชื่อมต่อและการเก็บข้อมูลจาก Kafka ไปยัง MongoDB และ Prometheus โดยใช้ Kafka Connectors ซึ่งเป็นกระบวนการสำคัญในการจัดการข้อมูลจากระบบ IoT โดยอธิบายดังนี้:

## การตั้งค่าการเชื่อมต่อระหว่าง Kafka และ MongoDB

MongoDB ถูกเลือกใช้ในระบบนี้เพราะเป็นฐานข้อมูลแบบ NoSQL ที่สามารถจัดเก็บข้อมูลแบบไม่เป็นโครงสร้าง และรองรับข้อมูลจำนวนมากได้ดี ซึ่งเป็นสิ่งสำคัญสำหรับข้อมูลจากเซ็นเซอร์ในระบบ IoT ที่มีการเก็บข้อมูลจำนวนมากและต่อเนื่อง

การเชื่อมต่อระหว่าง Kafka และ MongoDB นั้นทำได้โดยใช้ **MongoDB Kafka Sink Connector** ซึ่งทำหน้าที่ส่งข้อมูลจาก Kafka topic ไปเก็บใน MongoDB collection ที่กำหนดไว้ โดยขั้นตอนนี้ทำให้ข้อมูลจากระบบ Kafka ถูกจัดเก็บในฐานข้อมูลเพื่อการใช้งานในอนาคต

### ตัวอย่างขั้นตอนที่ 1: การตั้งค่าเชื่อมต่อระหว่าง Kafka และ MongoDB
ในขั้นตอนนี้ได้ตั้งค่า Kafka Connector เพื่อส่งข้อมูลจาก Kafka topic ที่ชื่อ **iot-frames** ไปเก็บใน MongoDB ที่คอลเล็กชันชื่อ **iot_frames**

```json
{
  "name": "iot-frames-mongodb-sink",
  "config": {
    "connector.class": "com.mongodb.kafka.connect.MongoSinkConnector",  // ใช้คลาสนี้สำหรับเชื่อมต่อ MongoDB
    "tasks.max": 1,  // ตั้งค่าจำนวน tasks ให้เป็น 1
    "topics": "iot-frames",  // ระบุ topic ที่จะรับข้อมูลมาจาก Kafka
    "connection.uri": "mongodb://devroot:devroot@mongo:27017",  // กำหนด URI ของ MongoDB เพื่อเชื่อมต่อ
    "database": "iot",  // ชื่อฐานข้อมูลใน MongoDB
    "collection": "iot_frames",  // ชื่อ collection ที่จะเก็บข้อมูล
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",  // ตั้งค่า Converter ให้ใช้ JSON
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter.schemas.enable": false,
    "key.converter.schemas.enable": false
  }
}
```

### ตัวอย่างขั้นตอนที่ 2: ส่งข้อมูลจาก Kafka ไปยัง MongoDB (กรณี topic iot-aggregate-metrics-sensor)
ในขั้นตอนนี้เราจะทำการตั้งค่าเพื่อเก็บข้อมูลที่ได้รับจาก Kafka topic **iot-aggregate-metrics-sensor** ไปยังคอลเล็กชันใน MongoDB ที่ชื่อว่า **iot_aggregate_metrics_sensor**

```json
{
  "name": "iot-aggregate-metrics-sensor-mongodb-sink",
  "config": {
    "connector.class": "com.mongodb.kafka.connect.MongoSinkConnector",
    "tasks.max": 1,
    "topics": "iot-aggregate-metrics-sensor",
    "connection.uri": "mongodb://devroot:devroot@mongo:27017",
    "database": "iot",
    "collection": "iot_aggregate_metrics_sensor",  // ระบุคอลเล็กชันสำหรับข้อมูลนี้
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "key.converter": "org.apache.kafka.connect.storage.StringConverter",  // กำหนด Converter สำหรับ key
    "value.converter.schemas.enable": false,
    "key.converter.schemas.enable": false
  }
}
```

### ตัวอย่างขั้นตอนที่ 3: ส่งข้อมูลตามสถานที่ (place) จาก Kafka ไปยัง MongoDB
ในกรณีนี้ Kafka topic ที่ใช้คือ **iot-aggregate-metrics-place** ซึ่งจะทำการเก็บข้อมูลไปยังคอลเล็กชัน **iot_aggregate_metrics_place**

```json
{
  "name": "iot-aggregate-metrics-place-mongodb-sink",
  "config": {
    "connector.class": "com.mongodb.kafka.connect.MongoSinkConnector",
    "tasks.max": 1,
    "topics": "iot-aggregate-metrics-place",
    "connection.uri": "mongodb://devroot:devroot@mongo:27017",
    "database": "iot",
    "collection": "iot_aggregate_metrics_place",  // คอลเล็กชันสำหรับข้อมูลตามสถานที่
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "key.converter": "org.apache.kafka.connect.storage.StringConverter",
    "value.converter.schemas.enable": false,
    "key.converter.schemas.enable": false
  }
}
```

---

## การเชื่อมต่อ Kafka กับ Prometheus

Prometheus เป็นระบบที่ใช้ในการเก็บข้อมูลแบบ time-series ซึ่งเหมาะสำหรับการติดตามค่าต่างๆ ที่เปลี่ยนแปลงตามเวลา เช่น ข้อมูลจาก IoT metrics ดังนั้นการเชื่อมต่อกับ Prometheus นั้นจะทำให้เราสามารถเก็บข้อมูลจาก Kafka ในรูปแบบที่สามารถใช้ในการสังเกตการณ์ (monitoring) ได้อย่างมีประสิทธิภาพ

ในที่นี้เราใช้ **PrometheusMetricsSinkConnector** เพื่อส่งข้อมูลจาก Kafka topic ที่ชื่อ **iot-metrics-time-series** ไปเก็บไว้ใน Prometheus

### ตัวอย่างการตั้งค่าเชื่อมต่อ Kafka กับ Prometheus
```json
{
  "name": "prometheus-connector-sink",
  "config": {
    "topics": "iot-metrics-time-series",  // ระบุ topic ที่ต้องการเก็บข้อมูล
    "connector.class": "io.confluent.connect.prometheus.PrometheusMetricsSinkConnector",  // ใช้ Prometheus Sink Connector
    "tasks.max": "1",
    "confluent.topic.bootstrap.servers": "kafka:9092",
    "prometheus.scrape.url": "http://0.0.0.0:8084/iot-metrics-time-series",  // URL สำหรับ Prometheus ในการเก็บข้อมูล
    "prometheus.listener.url": "http://0.0.0.0:8084/iot-metrics-time-series",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter.schemas.enable": false,
    "key.converter.schemas.enable": false,
    "reporter.bootstrap.servers": "kafka:9092",
    "reporter.result.topic.replication.factor": "1",
    "reporter.error.topic.replication.factor": "1",
    "behavior.on.error": "log"
  }
}
```

การตั้งค่านี้ช่วยให้ข้อมูลแบบ time-series จาก Kafka สามารถถูกนำไปเก็บใน Prometheus เพื่อใช้ในการตรวจสอบค่าต่างๆ ในระบบแบบ real-time และวิเคราะห์ข้อมูลในช่วงเวลาต่างๆ ได้