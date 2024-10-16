# การวิเคราะห์และการทำ Aggregation

## ความสัมพันธ์ระหว่างการวิเคราะห์ (Analyze) และการรวมข้อมูล (Aggregation)

- **Aggregation**: เป็นขั้นตอนที่ช่วยลดความซับซ้อนของข้อมูล โดยทำการรวมข้อมูลเพื่อให้สามารถวิเคราะห์ได้ง่ายขึ้น เช่น การสรุปยอดขายรายเดือน
- **Analyze**: เป็นกระบวนการที่นำข้อมูลที่ได้จากการรวมข้อมูลมาใช้ในการวิเคราะห์เชิงลึก เช่น เพื่อหาความสัมพันธ์หรือแนวโน้มสำคัญสำหรับตัดสินใจ

---

## การตั้งค่า Kafka Streams สำหรับ IoT Processor

Kafka Streams คือบริการที่ช่วยในการประมวลผลข้อมูลจากเซ็นเซอร์ที่รับเข้ามาผ่าน Kafka topic โดยใช้โปรเซสเซอร์ (Processor) เพื่อทำการวิเคราะห์และประมวลผลข้อมูลที่ไหลเข้ามาในลักษณะสตรีม (streaming data)

### โปรเซสเซอร์หลักในระบบมีทั้งหมด 3 ตัว ได้แก่:

1. **Aggregate Metrics By Sensor Processor**: ประมวลผลข้อมูลจากเซ็นเซอร์แต่ละตัว
2. **Aggregate Metrics By Place Processor**: ประมวลผลข้อมูลตามสถานที่ต่างๆ
3. **Metrics Time Series Processor**: ประมวลผลข้อมูลในรูปแบบ time series

```java
@Configuration
@EnableKafka
@EnableKafkaStreams
public class KafkaStreamsConfig {
    // กำหนดค่าต่างๆ ที่จำเป็น เช่น Kafka bootstrap server, Serde (Serializer/Deserializer)
    // รวมถึงการตั้งค่าของ topic ที่ใช้ในระบบ
}
```

---

## การทำงานของแต่ละโปรเซสเซอร์

### Aggregate Metrics By Sensor Processor

โปรเซสเซอร์ตัวนี้จะทำการรวมข้อมูล (aggregate) จากเซ็นเซอร์แต่ละตัวโดยใช้ "window" ขนาด 5 นาที เพื่อคำนวณค่าเฉลี่ยของตัวแปรต่างๆ เช่น อุณหภูมิ ความชื้น ความดัน และความสว่างสำหรับแต่ละเซ็นเซอร์

```java
@Component
public class AggregateMetricsBySensorProcessor {
    // การประมวลผลข้อมูลจากเซ็นเซอร์ในช่วงเวลาหน้าต่าง (window) 5 นาที
    // ข้อมูลถูกประมวลผลแล้วจะถูกส่งออกไปยัง Kafka topic อื่นเพื่อใช้งานต่อ
}
```

---

### Aggregate Metrics By Place Processor

ตัวนี้ทำการประมวลผลข้อมูลโดยแบ่งตาม "สถานที่" (Place) ซึ่งคล้ายกับการทำงานของ Aggregate Metrics By Sensor Processor แต่จะคำนวณค่าเฉลี่ยสำหรับเซ็นเซอร์ทั้งหมดในแต่ละสถานที่แทนที่จะคำนวณแยกเป็นเซ็นเซอร์

```java
@Component
public class AggregateMetricsByPlaceProcessor {
    // การคำนวณค่าเฉลี่ยของตัวแปรต่างๆ โดยอิงตามสถานที่
}
```

---

### Metrics Time Series Processor

ตัวนี้ประมวลผลข้อมูลเซ็นเซอร์ในรูปแบบ time series เพื่อทำการวิเคราะห์เชิงเวลาสำหรับตัวแปรต่างๆ โดยจะนำข้อมูลที่ได้จากเซ็นเซอร์ไปสร้างเป็นกราฟหรือรายงานแบบต่อเนื่อง

```java
@Component
public class MetricsTimeSeriesProcessor {
    // นำข้อมูลจากเซ็นเซอร์มาประมวลผลในรูปแบบ time series
}
```

---

## SerDe (Serializer/Deserializer)

ใน Kafka Streams การส่งและรับข้อมูลจะต้องใช้การแปลงข้อมูลให้อยู่ในรูปแบบที่เหมาะสม ซึ่งเรียกว่า SerDe

- **Serializer**: ใช้ในการแปลงวัตถุเป็นข้อมูลไบต์ (byte) ก่อนที่จะส่งผ่าน Kafka
- **Deserializer**: แปลงข้อมูลไบต์ที่รับมาจาก Kafka ให้กลับเป็นวัตถุที่ใช้งานได้ในโค้ด

ในโค้ดนี้มีการใช้ `SensorTimeSerieMetricSerde()` ซึ่งเป็น Custom SerDe ที่สร้างขึ้นมาเฉพาะเพื่อจัดการการแปลงข้อมูลใน Kafka ให้สามารถทำงานกับข้อมูลเซ็นเซอร์ได้อย่างถูกต้อง