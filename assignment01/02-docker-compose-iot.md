# IoT Docker Compose

This document explains the structure of the `docker-compose.yml` file for various services involved in real-time data streaming using Kafka, Prometheus, and Grafana.

## Services Overview

### ZooKeeper
- **Purpose:** Provides distributed configuration storage, synchronization, and group services for Kafka clusters.
- **Port:** 2181
- **Data Storage:** `/var/lib/zookeeper`

### Kafka
- **Purpose:** A distributed streaming platform for building real-time data pipelines and streaming applications.
- **Connection to ZooKeeper:** Port 9092
- **Features:** Supports data compression and automatic topic creation.

### Kafka REST Proxy
- **Purpose:** Provides a RESTful interface to Kafka, simplifying message management and cluster status monitoring.
- **Port:** 8082
- **Connection to ZooKeeper:** Port 2181

### Kafka Topics UI
- **Purpose:** Interface for viewing and managing Kafka topics.
- **Port:** 8081
- **Connection:** Through Kafka REST Proxy

### Kafka Connect
- **Purpose:** Framework for connecting Kafka with external systems such as databases or file systems.
- **Port:** 8083
- **Cluster Management:** Uses ZooKeeper

### Kafka Connect UI
- **Purpose:** Interface for managing Kafka Connect.
- **Port:** 8082
- **Connection:** To Kafka Connect

### Mosquitto (MQTT Broker)
- **Purpose:** An open-source MQTT broker supporting publish/subscribe communication suitable for IoT devices.
- **Port:** 1883

### MongoDB
- **Purpose:** NoSQL database storing data in document format.
- **Port:** 27017
- **Authentication:** Configured via `.env` file

### Mongo Express
- **Purpose:** Web interface for managing MongoDB.
- **Port:** 8084
- **Connection:** To MongoDB

### Grafana
- **Purpose:** Data visualization platform for creating dashboards to monitor and display real-time data.
- **Port:** 8085
- **Plugins:** Supports additional plugins like Grafana Clock, Worldmap, and Piechart

### Prometheus
- **Purpose:** System for collecting metrics in a time-series database and alerting based on metric changes.
- **Port:** 8086
- **Data Storage:** `/etc/prometheus/`

### Node Exporter
- **Purpose:** Exports machine metrics to Prometheus.
- **Port:** 9100

Each service collaborates to create an environment for real-time data streaming and monitoring. The connections between services use various ports, and these configurations can be customized as needed.

## Start-Service #0
**Screen 1:**

- **zookeeper:** Manages distributed configuration, naming, synchronization, and group services in Kafka clusters.
- **kafka:** Acts as a distributed streaming platform for building real-time data pipelines and streaming applications.
- **kafka-rest-proxy:** Provides a RESTful interface for Kafka clusters to simplify message production and consumption.
- **kafka-topics-ui:** Tool for viewing and managing Kafka topics via a UI.
- **kafka-connect:** Framework for connecting Kafka with external systems like databases and file systems.
- **kafka-connect-ui:** UI for managing and configuring connections in Kafka Connect.

## Start-Service #1
**Screen 2:**

- **mongo:** MongoDB database for data storage.
- **mongo-express:** Web interface for MongoDB, making it easy to view and manage data.
- **mosquitto:** MQTT broker for publish/subscribe communication between IoT devices.
- **prometheus:** Monitoring and alerting system logging real-time metrics in a time-series database.
- **nodeexporter:** Used for exporting machine metrics to Prometheus.

## Start-Service #2
**Screen 3:**

- **grafana:** Tool for data visualization and analysis through dashboards, used to display data from Prometheus and others.
- **pushgateway:** Used to push data from long-running jobs to Prometheus.

## Start-Service #3
**Screen 4:**

- **iot_sensor_1, iot_sensor_2, iot_sensor_3:** IoT sensors used for sending data to the system.
- **iot-processor:** Processor for handling data from IoT sensors.
