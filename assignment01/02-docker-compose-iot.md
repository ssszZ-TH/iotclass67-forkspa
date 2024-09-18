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




## เวลาสตาร์ทบริการต้องไปรันไฟล์ `start.sh` ทั้ง 4 ตัว เรียงกัน โดยต้องเปิด terminal ทิ้งไว้ด้วย

<img src='./start4.jpg'>
<img src="./4screen.jpg">


# docker compose file

```yaml

volumes:
    prometheus_data: {}
    grafana_data: {}
    zookeeper-data:
      driver: local
    zookeeper-log:
      driver: local
    kafka-data:
      driver: local

services:

  # ZooKeeper is a centralized service for maintaining configuration information,
  # naming, providing distributed synchronization, and providing group services.
  # It provides distributed coordination for our Kafka cluster.
  # http://zookeeper.apache.org/
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    container_name: zookeeper
    # ZooKeeper is designed to "fail-fast", so it is important to allow it to
    # restart automatically.
    restart: unless-stopped
    volumes:
      - zookeeper-data:/var/lib/zookeeper/data
      - zookeeper-log:/var/lib/zookeeper/log
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_LOG4J_ROOT_LOGLEVEL: INFO
      ZOOKEEPER_LOG4J_PROP: INFO,ROLLINGFILE
      ZOOKEEPER_LOG_MAXFILESIZE: 10MB
      ZOOKEEPER_LOG_MAXBACKUPINDEX: 10
      ZOOKEEPER_SNAP_COUNT: 10
      ZOOKEEPER_AUTOPURGE_SNAP_RETAIN_COUNT: 10
      ZOOKEEPER_AUTOPURGE_PURGE_INTERVAL: 3


  # Kafka is a distributed streaming platform. It is used to build real-time streaming
  # data pipelines that reliably move data between systems and platforms, and to build
  # real-time streaming applications that transform or react to the streams of data.
  # http://kafka.apache.org/
  kafka:
    # image: confluentinc/cp-kafka
    image: confluentinc/cp-kafka:latest
    container_name: kafka
    ports:
      - '9092:9092'
    volumes:
      - kafka-data:/var/lib/kafka
    restart: unless-stopped
    environment:
      # Required. Instructs Kafka how to get in touch with ZooKeeper.
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_NUM_PARTITIONS: 1
      KAFKA_COMPRESSION_TYPE: gzip
      # Required when running in a single-node cluster, as we are. We would be able to take the default if we had
      # three or more nodes in the cluster.
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
      # Required. Kafka will publish this address to ZooKeeper so clients know
      # how to get in touch with Kafka. "PLAINTEXT" indicates that no authentication
      # mechanism will be used.
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092
      KAFKA_AUTO_CREATE_TOPICS_ENABLE: 'true'
    links:
      - zookeeper


  # The Kafka REST Proxy provides a RESTful interface to a Kafka cluster.
  # It makes it easy to produce and consume messages, view the state
  # of the cluster, and perform administrative actions without using
  # the native Kafka protocol or clients.
  # https://github.com/confluentinc/kafka-rest
  kafka-rest-proxy:
    image: confluentinc/cp-kafka-rest:latest
    container_name: kafka-rest-proxy
    environment:
      # Specifies the ZooKeeper connection string. This service connects
      # to ZooKeeper so that it can broadcast its endpoints as well as
      # react to the dynamic topology of the Kafka cluster.
      KAFKA_REST_ZOOKEEPER_CONNECT: zookeeper:2181
      # The address on which Kafka REST will listen for API requests.
      KAFKA_REST_LISTENERS: http://0.0.0.0:8082/
      # Required. This is the hostname used to generate absolute URLs in responses.
      # It defaults to the Java canonical hostname for the container, which might
      # not be resolvable in a Docker environment.
      KAFKA_REST_HOST_NAME: kafka-rest-proxy
      # The list of Kafka brokers to connect to. This is only used for bootstrapping,
      # the addresses provided here are used to initially connect to the cluster,
      # after which the cluster will dynamically change. Thanks, ZooKeeper!
      KAFKA_REST_BOOTSTRAP_SERVERS: kafka:9092
    # Kafka REST relies upon Kafka, ZooKeeper
    # This will instruct docker to wait until those services are up
    # before attempting to start Kafka REST.
    restart: unless-stopped
    ports:
      - "9999:8082"
    depends_on:
      - zookeeper
      - kafka

  # Kafka Connect, an open source component of Apache Kafka,
  # is a framework for connecting Kafka with external systems
  # such as databases, key-value stores, search indexes, and file systems.
  # https://docs.confluent.io/current/connect/index.html
  kafka-connect:
    image: confluentinc/cp-kafka-connect:latest
    hostname: kafka-connect
    container_name: kafka-connect
    environment:
      # Required.
      # The list of Kafka brokers to connect to. This is only used for bootstrapping,
      # the addresses provided here are used to initially connect to the cluster,
      # after which the cluster can dynamically change. Thanks, ZooKeeper!
      CONNECT_BOOTSTRAP_SERVERS: "kafka:9092"
      # Required. A unique string that identifies the Connect cluster group this worker belongs to.
      CONNECT_GROUP_ID: kafka-connect-group
      # Connect will actually use Kafka topics as a datastore for configuration and other data. #meta
      # Required. The name of the topic where connector and task configuration data are stored.
      CONNECT_CONFIG_STORAGE_TOPIC: kafka-connect-meta-configs
      # Required. The name of the topic where connector and task configuration offsets are stored.
      CONNECT_OFFSET_STORAGE_TOPIC: kafka-connect-meta-offsets
      # Required. The name of the topic where connector and task configuration status updates are stored.
      CONNECT_STATUS_STORAGE_TOPIC: kafka-connect-meta-status
      # Required. Converter class for key Connect data. This controls the format of the
      # data that will be written to Kafka for source connectors or read from Kafka for sink connectors.
      CONNECT_KEY_CONVERTER: org.apache.kafka.connect.json.JsonConverter
      # Required. Converter class for value Connect data. This controls the format of the
      # data that will be written to Kafka for source connectors or read from Kafka for sink connectors.
      CONNECT_VALUE_CONVERTER: org.apache.kafka.connect.json.JsonConverter
      # Required. The hostname that will be given out to other workers to connect to.
      CONNECT_REST_ADVERTISED_HOST_NAME: "kafka-connect"
      CONNECT_REST_PORT: 8083
      # The next three are required when running in a single-node cluster, as we are.
      # We would be able to take the default (of 3) if we had three or more nodes in the cluster.
      CONNECT_CONFIG_STORAGE_REPLICATION_FACTOR: "1"
      CONNECT_OFFSET_STORAGE_REPLICATION_FACTOR: "1"
      CONNECT_STATUS_STORAGE_REPLICATION_FACTOR: "1"
      #Connectos path
      CONNECT_PLUGIN_PATH: "/usr/share/java,/data/connectors/"
      CONNECT_LOG4J_ROOT_LOGLEVEL: "INFO"
    restart: unless-stopped
    volumes:
      - ./kafka_connect/data:/data
    command: 
      - bash 
      - -c 
      - |
        echo "Launching Kafka Connect worker"
        /etc/confluent/docker/run & 
        #
        echo "Waiting for Kafka Connect to start listening on http://$$CONNECT_REST_ADVERTISED_HOST_NAME:$$CONNECT_REST_PORT/connectors ⏳"
        while [ $$(curl -s -o /dev/null -w %{http_code} http://$$CONNECT_REST_ADVERTISED_HOST_NAME:$$CONNECT_REST_PORT/connectors) -ne 200 ] ; do 
          echo -e $$(date) " Kafka Connect listener HTTP state: " $$(curl -s -o /dev/null -w %{http_code} http://$$CONNECT_REST_ADVERTISED_HOST_NAME:$$CONNECT_REST_PORT/connectors) " (waiting for 200)"
          sleep 5 
        done
        nc -vz $$CONNECT_REST_ADVERTISED_HOST_NAME $$CONNECT_REST_PORT
        echo -e "\n--\n+> Creating Kafka Connect MongoDB sink Current PATH ($$PWD)"
        /data/scripts/create_mongo_sink.sh 
        echo -e "\n--\n+> Creating MQTT Source Connect Current PATH ($$PWD)"
        /data/scripts/create_mqtt_source.sh
        echo -e "\n--\n+> Creating Kafka Connect Prometheus sink Current PATH ($$PWD)"
        /data/scripts/create_prometheus_sink.sh
        sleep infinity
    # kafka-connect relies upon Kafka and ZooKeeper.
    # This will instruct docker to wait until those services are up
    # before attempting to start kafka-connect.
    depends_on:
      - zookeeper
      - kafka

  # Web client for ZooNavigator, web-based browser & editor for ZooKeeper.
  # https://github.com/elkozmon/zoonavigator
  # Navigate to zookeeper:2181 (see zookeeper service)
  zoonavigator:
    image: elkozmon/zoonavigator:latest
    container_name: zoonavigator
    restart: unless-stopped
    ports:
      - "9000:9000"
    environment:
      # The port on which the api service will listen for incoming connections.
      HTTP_PORT: 9000
    depends_on:
      - zookeeper

  # Eclipse Mosquitto is an open source (EPL/EDL licensed) message broker that implements the MQTT protocol versions 5.0, 3.1.1 and 3.1. Mosquitto is lightweight and is suitable for use on all devices from low power single board computers to full servers.
  # The MQTT protocol provides a lightweight method of carrying out messaging using a publish/subscribe model. This makes it suitable for Internet of Things messaging such as with low power sensors or mobile devices such as phones, embedded computers or microcontrollers.
  # The Mosquitto project also provides a C library for implementing MQTT clients, and the very popular mosquitto_pub and mosquitto_sub command line MQTT clients.
  mosquitto:
    image: eclipse-mosquitto:latest
    hostname: mosquitto
    container_name: mosquitto
    restart: unless-stopped
    ports:
      - "1883:1883"
      - "9001:9001"
    volumes:
      - ./mosquitto/config:/mosquitto/config
      - ./mosquitto/data:/mosquitto/data
      - ./mosquitto/log:/mosquitto/log

  mongo:
    image: mongo:4.4.6
    container_name: mongo
    env_file:
      - .env
    restart: unless-stopped
    environment:
      - MONGO_INITDB_ROOT_USERNAME=${MONGO_ROOT_USER}
      - MONGO_INITDB_ROOT_PASSWORD=${MONGO_ROOT_PASSWORD}
      - MONGO_INITDB_DATABASE=${MONGO_DB}

  # Web-based MongoDB admin interface, written with Node.js and express
  mongo-express:
    image: mongo-expr

```

```sh
docker compose up zookeeper kafka
```

```sh
docker compose up kafka-rest-proxy kafka-connect mosquitto mongo grafana prometheus
```

```sh
docker compose up iot-processor
```

```sh
docker compose up iot_sensor_1
```