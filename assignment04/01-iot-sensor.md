# การเก็บและจัดเก็บข้อมูลเรียลไทม์จากเซ็นเซอร์ IoT

## การตั้งค่าและการเชื่อมต่อ

### การตั้งค่า WiFi และ MQTT
- ตั้งค่าที่อยู่ IP, พอร์ต, และข้อมูลการเข้าสู่ระบบ (username, password) สำหรับการเชื่อมต่อกับ WiFi และ MQTT broker
- ใช้ Adafruit_NeoPixel สำหรับแสดงสถานะการเชื่อมต่อ WiFi และ MQTT ผ่าน LED

### การเชื่อมต่อ WiFi
- ฟังก์ชัน `setup_wifi()` ใช้สำหรับเชื่อมต่อกับ WiFi และแสดงสถานะการเชื่อมต่อผ่านการกระพริบของ LED
  - หากการเชื่อมต่อสำเร็จ LED จะเป็นสีเขียว
  - หากการเชื่อมต่อล้มเหลว LED จะกระพริบสีแดง

### การเชื่อมต่อ MQTT
- ฟังก์ชัน `mqtt_handle()` ใช้จัดการการเชื่อมต่อกับ MQTT broker
  - หากเชื่อมต่อสำเร็จ LED จะเปลี่ยนเป็นสีเขียว

### การเริ่มต้นเซ็นเซอร์และ NTP
- เริ่มต้นเซ็นเซอร์ต่างๆ เช่น BMP280 และ SHT4x และตรวจสอบความผิดพลาด
- ตั้งค่า NTP client เพื่อซิงค์เวลา

## MQTT Topic

- ใช้ MQTT Topic ชื่อ "iot-frames"

## MQTT Payload

- Payload ที่ส่งผ่าน MQTT มีรูปแบบ JSON ที่รวมข้อมูลจากเซ็นเซอร์และข้อมูลเวลา:

## คำอธิบาย topic 
payload คือสิ่งที่ cucumber จะส่งไปให้กับ backend โดยจะมีรูปแบบ JSON ที่รวมข้อมูลจากเซ็นเซอร์ และข้อมูลเวลา การทำเเบบนี้เรียกว่า time series data ``สามารถนำไปตอบในข้อสอบได้เลย``

```json
{
  "id": "12434349",
  "name": "iot_sensor_3",
  "place_id": "32347983",
  "date": "2024-08-28T12:34:56",  // เวลาปัจจุบันในรูปแบบ ISO 8601
  "timestamp": 1693222496,         // เวลาในรูปแบบ epoch
  "payload": {
    "temperature": 25.3,           // อุณหภูมิจาก BMP280
    "humidity": 45.0,              // ความชื้นจาก SHT4x
    "pressure": 1012.5,            // ความดันจาก BMP280
    "luminosity": 123              // ค่าแสงจาก LDR
  }
}

```

ESP32 ทำหน้าที่เป็น:

- Client: สำหรับเชื่อมต่อกับ WiFi และ MQTT broker
- Collector: สำหรับเก็บข้อมูลจากเซ็นเซอร์ต่างๆ
- Publisher: สำหรับส่งข้อมูลที่เก็บได้ไปยัง MQTT broker

## flowchart

![flowchart](./iot_architecture-cucumber%20flow.drawio.png)

## code cucumber

```cpp

#include <Wire.h>
#include <Adafruit_BMP280.h>
#include <SensirionI2cSht4x.h>
#include <Adafruit_NeoPixel.h>
#include <PubSubClient.h>
#include <ArduinoJson.h>
#include <WiFiUdp.h>
#include <WiFi.h>
#include <time.h>

// WiFi & MQTT Configuration
const char* ssid = "TP-Link_CA30";
const char* password = "29451760";
const char* mqtt_server = "172.16.46.111";
const int mqtt_port = 1883;
const char* mqtt_topic = "give-me-grade-A";
const char* mqtt_user = "spa";
const char* mqtt_password = "grade_A";

IPAddress local_IP(172, 16, 46, 112);
IPAddress gateway(192, 168, 1, 1);
IPAddress subnet(255, 255, 255, 0);
IPAddress primaryDNS(8, 8, 8, 8);       
IPAddress secondaryDNS(8, 8, 4, 4);

WiFiClient espClient;
PubSubClient client(espClient);

// NeoPixel Configuration
#define LED_PIN 18
#define LED_COUNT 60
Adafruit_NeoPixel strip(LED_COUNT, LED_PIN, NEO_GRB + NEO_KHZ800);

// Sensor Configuration
Adafruit_BMP280 bmp280;
SensirionI2cSht4x sensor;
int Pin = 5; 
int Value = 0; 

// NTP Configuration
const char* ntp_server1 = "pool.ntp.org";
const char* ntp_server2 = "time.google.com";
const int gmtOffset_sec = 7 * 3600;

// Function Declarations
void connectWiFi();
void connectMQTT();
void initializeSensors();
void readAndSendSensorData();
void setup_wifi();
void reconnect();
void setAllLeds(uint32_t color, int delayTime = 0);

// Setup NeoPixel LEDs
void setAllLeds(uint32_t color, int delayTime) {
  strip.fill(color);
  strip.show();
  if (delayTime > 0) {
    delay(delayTime);
    strip.fill(0);
    strip.show();
  }
}

// WiFi Setup
void setup_wifi() {
  WiFi.config(local_IP, gateway, subnet, primaryDNS, secondaryDNS);
  WiFi.begin(ssid, password); 
  configTime(gmtOffset_sec, 0, ntp_server1, ntp_server2);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
    setAllLeds(strip.Color(255, 0, 0));  // Red for connecting
  }

  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
  setAllLeds(strip.Color(0, 0, 255));  // Blue when connected
}

// MQTT Reconnect
void reconnect() {
  while (!client.connected()) {
    if (WiFi.status() != WL_CONNECTED) {
      setup_wifi();
      if (WiFi.status() != WL_CONNECTED) return;
    }

    Serial.print("Attempting MQTT connection...");
    if (client.connect("ESP32Client5", mqtt_user, mqtt_password)) {
      Serial.println("connected");
      setAllLeds(strip.Color(0, 255, 0));  // Green for MQTT connected
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");
      setAllLeds(strip.Color(255, 165, 0));  // Orange for failed
      delay(5000);
    }
  }
}

// Sensor Initialization
void initializeSensors() {
  if (!bmp280.begin(0x76)) {
    Serial.println(F("BMP280 sensor not found!"));
    while (1) delay(10);
  }
  sensor.begin(Wire, SHT40_I2C_ADDR_44);
  sensor.softReset();
  delay(10);
}

// Sensor Data and MQTT Publish
void printSensorData() {
  float temperature = bmp280.readTemperature();
  float pressure = bmp280.readPressure();
  float aTemperature, aHumidity;
  sensor.measureLowestPrecision(aTemperature, aHumidity);

  time_t now = time(nullptr);
  char dateTimeBuffer[30];
  strftime(dateTimeBuffer, sizeof(dateTimeBuffer), "%Y-%m-%dT%H:%M:%S", localtime(&now));

  StaticJsonDocument<200> doc;
  doc["id"] = "67834567";
  doc["name"] = "iot_sensor_5";
  doc["place_id"] = "62345467";
  doc["date"] = dateTimeBuffer;
  doc["timestamp"] = now * 1000;
  JsonObject payload = doc.createNestedObject("payload");
  payload["temperature"] = temperature;
  payload["humidity"] = aHumidity;
  payload["pressure"] = pressure / 100;
  payload["luminosity"] = Value;

  char jsonBuffer[256];
  serializeJson(doc, jsonBuffer);
  client.publish(mqtt_topic, jsonBuffer);
  setAllLeds(strip.Color(0, 255, 255), 400);  // Pink for data sent
}

// Setup Function
void setup() {
  Wire.begin(41, 40);  
  Serial.begin(115200);
  strip.begin();
  strip.setBrightness(50);
  setAllLeds(strip.Color(255, 255, 255));  // White on start

  setup_wifi();
  client.setServer(mqtt_server, mqtt_port);
  initializeSensors();
}

// Loop Function
void loop() {
  if (WiFi.status() != WL_CONNECTED) {
    setup_wifi();
  }

  if (!client.connected()) {
    reconnect();
  }

  client.loop();
  delay(1000);
  printSensorData();
  Value = analogRead(Pin);
  Serial.println("Luminosity: " + String(Value));
  delay(2000);
}


```