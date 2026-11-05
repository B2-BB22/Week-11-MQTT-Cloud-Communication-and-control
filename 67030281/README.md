# แฟ้มผลงานและรายงานผลปฏิบัติการ สัปดาห์ที่ 11
## หัวข้อ: MQTT Cloud Communication and Control & ESP RainMaker Ecosystem

**รหัสนักศึกษา:** 67030281  
**ภาคการศึกษา:** ภาคเรียนที่ 1 ปีการศึกษา 2569  
**ที่อยู่โฟลเดอร์ปฏิบัติการ:** `67030281/`  

---

## 1. บทสรุปภาพรวม (Executive Summary)

ในสัปดาห์ที่ 11 นี้ นักศึกษาได้ศึกษาและลงมือปฏิบัติการพัฒนาระบบสื่อสารและควบคุมอุปกรณ์ IoT ระยะไกลผ่านระบบคลาวด์ (Cloud Communication & Control) ตามแนวทางของหนังสือ **ESP32-C3 Wireless Adventure (Chapter 9: Cloud Control)** โดย Espressif Systems ครอบคลุมเนื้อหาสำคัญ 4 มิติ:

1. **สถาปัตยกรรมโปรโตคอล MQTT พื้นฐาน และการติดตั้ง Broker (Lab 11.1):** การสร้างระบบตัวกลางรับส่งข้อมูลด้วย **Eclipse Mosquitto** บนพอร์ต 1883 การทดสอบระบบหัวข้อ (Topics & Wildcards `+`, `#`), การวิเคราะห์แพ็กเก็ตควบคุมตามระดับคุณภาพการบริการ (QoS 0, 1, 2), ข้อความคงค้าง (Retained Messages) และพินัยกรรมฉุกเฉิน (Last Will and Testament - LWT)
2. **การพัฒนา ESP-IDF MQTT Client สองทิศทาง (Lab 11.2):** การพัฒนาเฟิร์มแวร์ C บนบอร์ด ESP32/ESP32-C3 โดยใช้คอมโพเนนต์ **`esp-mqtt`** ร่วมกับ **`cJSON`** เพื่ออ่านค่าแรงดันแอนะล็อกของตัวต้านทานปรับค่าได้ (Potentiometer) ส่งขึ้นหัวข้อ Telemetry ทุก 4 วินาที และรับคำสั่งควบคุมเปิด-ปิดหลอดไฟ LED จากระยะไกล พร้อมส่งสถานะตอบรับ
3. **ความปลอดภัยระดับมาตรฐานสากลด้วย MQTTS และ Mutual Authentication (Lab 11.3):** การจัดตั้งระบบองค์กรรับรองความถูกต้องส่วนบุคคล (Local Root CA) ด้วย OpenSSL และการสร้างใบรับรองดิจิทัล X.509 ให้กับ Mosquitto Broker (พอร์ต 8883) และบอร์ด ESP32 เพื่อทำ **Mutual TLS (mTLS)** ผ่านคำสั่ง `EMBED_TXTFILES` ของ CMake พร้อมทดสอบการปฏิเสธการเชื่อมต่อจากผู้บุกรุก
4. **แพลตฟอร์มคลาวด์อัจฉริยะ ESP RainMaker (Lab 11.4):** การสร้างผลิตภัณฑ์ Smart Light สู่ระบบคลาวด์ AWS IoT Core แบบครบวงจร ผ่านระบบ BLE Wi-Fi Provisioning, การขอรับใบรับรองอัตโนมัติด้วย **Assisted Claiming**, การซิงก์สถานะกับแอปพลิเคชันบนสมาร์ตโฟนข้ามโครงข่าย 4G/5G และการควบคุมผ่าน ESP RainMaker CLI

---

## 2. แผนผังโครงสร้างโฟลเดอร์ส่งงาน (Folder Structure)

```
67030281/
├── README.md                                  <- สรุปภาพรวมและดัชนีนำทางของรหัสนักศึกษา 67030281
├── ALL_LABS_ANSWERS_SUMMARY.md                <- เอกสารรวมเฉลยคำถามท้ายการทดลองทั้งหมด (ข้อ 1-12)
│
├── Lab11-1_Mosquitto_Broker/                  <- [ใบงานที่ 11.1] Mosquitto Broker Setup & Fundamentals
│   ├── mosquitto.conf                         <- ไฟล์คอนฟิกูเรชัน Broker (พอร์ต 1883, allow_anonymous)
│   ├── run_broker.bat                         <- สคริปต์เปิดรัน Mosquitto ในโหมด Verbose (-v)
│   ├── test_commands.ps1                      <- สคริปต์รวมคำสั่งทดสอบ CLI Pub/Sub, Wildcards, QoS, LWT
│   ├── broker_execution_logs.txt              <- บันทึก Log การทำงานของ Broker อย่างละเอียด
│   └── Lab11-1_Report_and_Answers.md          <- รายงานผลการทดลองและการตอบคำถามท้ายการทดลอง 4 ข้อ
│
├── Lab11-2_MQTT_Client/                       <- [ใบงานที่ 11.2] ESP32 Bi-directional Telemetry & Control
│   ├── CMakeLists.txt                         <- โปรเจกต์ CMakeLists.txt
│   ├── sdkconfig.defaults                     <- ค่าคอนฟิกเริ่มต้นของ ESP-IDF
│   ├── main/
│   │   ├── CMakeLists.txt                     <- คอมโพเนนต์ CMakeLists.txt (ระบุ REQUIRES ครบถ้วน)
│   │   ├── idf_component.yml                  <- ประกาศ Dependency cJSON และ mqtt
│   │   └── Lab11-2_MQTT_Client.c              <- ซอร์สโค้ดภาษา C ฉบับสมบูรณ์ (FreeRTOS Task, ADC, cJSON, LED)
│   ├── test_client_cli.bat                    <- เมนูทดสอบส่งคำสั่งเปิด-ปิดไฟ และดู Telemetry ผ่าน CLI
│   ├── serial_monitor_log.txt                 <- บันทึก Serial Monitor การเชื่อมต่อ Wi-Fi, MQTT และการส่งข้อมูล
│   └── Lab11-2_Report_and_Answers.md          <- รายงานผลการทดลองและการตอบคำถามท้ายการทดลอง 3 ข้อ
│
├── Lab11-3_MQTTS_Secure/                      <- [ใบงานที่ 11.3] MQTTS Security with TLS & Mutual Auth (mTLS)
│   ├── certs/                                 <- โฟลเดอร์เก็บกุญแจและใบรับรองดิจิทัล X.509
│   │   ├── generate_certs.bat                 <- สคริปต์ OpenSSL สำหรับ Windows
│   │   ├── generate_certs.sh                  <- สคริปต์ OpenSSL สำหรับ Linux/macOS
│   │   ├── generate_certs.py                  <- สคริปต์ Python สำหรับสร้างกุญแจ 2048-bit RSA และ X.509 อัตโนมัติ
│   │   ├── ca.crt & ca.key                    <- ใบรับรองและกุญแจของ Root CA
│   │   ├── server.crt & server.key            <- ใบรับรองและกุญแจของ Mosquitto Broker (CN=192.168.1.111)
│   │   └── client.crt & client.key            <- ใบรับรองและกุญแจของ ESP32 Client (CN=esp32-secure-node)
│   ├── mosquitto_ssl.conf                     <- ไฟล์คอนฟิก Broker MQTTS พอร์ต 8883 (require_certificate true)
│   ├── CMakeLists.txt                         <- โปรเจกต์ CMakeLists.txt
│   ├── sdkconfig.defaults                     <- ค่าคอนฟิก mTLS และ TLS Client Session
│   ├── main/
│   │   ├── CMakeLists.txt                     <- คอมโพเนนต์ CMakeLists.txt พร้อมคำสั่ง EMBED_TXTFILES
│   │   ├── certs/ (ca.crt, client.crt, client.key) <- ใบรับรองที่ถูกคัดลอกมาฝังลงในไบนารี
│   │   └── main.c                             <- ซอร์สโค้ด C เชื่อมต่อ mqtts:// ผ่าน mbedTLS Assembler Symbols
│   ├── test_mtls_cli.bat                      <- เมนูทดสอบการต่อแบบถูกต้อง และการจำลอง Hacker โจมตี
│   ├── broker_mtls_log.txt                    <- บันทึก Log ฝั่ง Broker แสดงการจับมือ TLS และการปฏิเสธแฮกเกอร์
│   ├── client_serial_log.txt                  <- บันทึก Serial Monitor ฝั่ง ESP32 ยืนยัน TLS Handshake สำเร็จ
│   └── Lab11-3_Report_and_Answers.md          <- รายงานผลการทดลองและการตอบคำถามท้ายการทดลอง 3 ข้อ
│
└── Lab11-4_RainMaker_Light/                   <- [ใบงานที่ 11.4] ESP RainMaker Smart Device & Cloud Ecosystem
    ├── CMakeLists.txt                         <- โปรเจกต์ CMakeLists.txt
    ├── main/
    │   ├── CMakeLists.txt                     <- คอมโพเนนต์ CMakeLists.txt
    │   ├── idf_component.yml                  <- ประกาศ Dependency espressif/esp-rainmaker (^3.0.0)
    │   └── main.c                             <- ซอร์สโค้ด C Smart Light (Node, Device, Params, Write Callback)
    ├── test_rainmaker_cli.bat                 <- สคริปต์ทดสอบผ่านเครื่องมือ esp-rainmaker-cli (getnodes, setparams)
    ├── serial_monitor_log.txt                 <- บันทึก Serial Monitor แสดง QR Code ASCII, BLE Provisioning
    └── Lab11-4_Report_and_Answers.md          <- รายงานผลการทดลองและการตอบคำถามท้ายการทดลอง 2 ข้อ
```

---

## 3. ข้อมูลการกำหนดค่าและฮาร์ดแวร์ประจำชุดทดลอง (Hardware & Configuration Matrix)

| รายการพารามิเตอร์ | ค่าที่กำหนดในการทดลองจริง | คำอธิบาย |
| :--- | :--- | :--- |
| **หมายเลขประจำตัวนักศึกษา** | **67030281** | ใช้เป็นเอกลักษณ์ในการสร้าง Node และโครงสร้างไฟล์ |
| **ชื่อเครือข่าย Wi-Fi (SSID)** | `AIS 4G Hi-Speed Home WiFi_769475` | เครือข่าย Wi-Fi ท้องถิ่นย่านความถี่ 2.4 GHz |
| **รหัสผ่านเครือข่าย (Password)** | `50769475` | รหัสผ่าน WPA2-PSK |
| **หมายเลข IP ของ MQTT Broker** | `192.168.1.111` | เครื่องคอมพิวเตอร์ที่รัน Mosquitto Broker |
| **พอร์ต MQTT Plaintext** | `1883` | พอร์ตมาตรฐานสำหรับการทดลอง Lab 11.1 และ 11.2 |
| **พอร์ต MQTTS (Encrypted TLS)**| `8883` | พอร์ตความปลอดภัยสูงสำหรับการทดลอง Lab 11.3 |
| **ขาควบคุม Actuator (LED)** | **GPIO 2** (ESP32) / **GPIO 8** (ESP32-C3) | ต่ออนุกรมผ่านตัวต้านทาน 330Ω ลงกราวด์ |
| **ขาอ่านเซนเซอร์ (Potentiometer)** | **GPIO 34** (ADC1_CH6) / **GPIO 0** (ADC1_CH0) | อ่านค่าแอนะล็อก 12-bit (ช่วงค่า 0 - 4095 แปลงเป็น 0 - 3.3V) |

---

## 4. ลิงก์เอกสารรายงานผลและเฉลยคำถามท้ายการทดลองแยกตามใบงาน

* **[ใบงานที่ 11.1: Mosquitto Broker Setup & MQTT Fundamentals](Lab11-1_Mosquitto_Broker/Lab11-1_Report_and_Answers.md)**
  - การวิเคราะห์ตัวกรองไวลด์การ์ด `+` และ `#`
  - ตารางเปรียบเทียบลำดับแพ็กเก็ต QoS 0, QoS 1, QoS 2
  - ผลการทดลอง Retained Message และ LWT
  - เฉลยคำถามท้ายการทดลอง ข้อ 1 - 4
* **[ใบงานที่ 11.2: ESP32 MQTT Client Telemetry & Control](Lab11-2_MQTT_Client/Lab11-2_Report_and_Answers.md)**
  - การต่อวงจรเซนเซอร์และ Actuator
  - การวิเคราะห์ JSON Payload และการจัดการ Heap Memory ป้องกัน Guru Meditation Error
  - แนวทางแก้ปัญหา Split-Brain Broker บน Windows
  - เฉลยคำถามท้ายการทดลอง ข้อ 1 - 3
* **[ใบงานที่ 11.3: MQTTS TLS & Mutual Authentication Security](Lab11-3_MQTTS_Secure/Lab11-3_Report_and_Answers.md)**
  - ลำดับชั้น PKI และการสร้างใบรับรองดิจิทัล X.509
  - ลำดับขั้นตอนการจับมือ mTLS Handshake (Four-way Certificate Authentication)
  - ผลการทดสอบป้องกันการโจมตีจาก Unauthorized Client
  - เฉลยคำถามท้ายการทดลอง ข้อ 1 - 3
* **[ใบงานที่ 11.4: ESP RainMaker Cloud IoT & Smart Device](Lab11-4_RainMaker_Light/Lab11-4_Report_and_Answers.md)**
  - แบบจำลองข้อมูล Node, Device, Param, Services
  - กลไก Assisted Claiming และ BLE Wi-Fi Provisioning
  - ผลการทดสอบควบคุมข้ามเครือข่ายอินเทอร์เน็ตมือถือ 4G/5G และการสั่งการผ่าน CLI
  - เฉลยคำถามท้ายการทดลอง ข้อ 1 - 2
* **[เอกสารรวมเฉลยคำถามท้ายการทดลองฉบับเต็มทุกข้อ (Master Sheet)](ALL_LABS_ANSWERS_SUMMARY.md)**
  - รวบรวมคำถามและคำตอบทั้ง 12 ข้อของสัปดาห์ที่ 11 จัดรูปแบบสวยงาม พร้อมสำหรับการตรวจสอบและให้คะแนน

---

## 5. ตารางตรวจสอบความสมบูรณ์ของชิ้นงาน (Submission Checklist)

- [x] สร้างโฟลเดอร์ส่งงานชื่อ **`67030281`** ใน Root Workspace
- [x] ซอร์สโค้ดและไฟล์คอนฟิกูเรชันของ **Lab 11.1** ครบถ้วน (`mosquitto.conf`, สคริปต์รัน, สคริปต์ทดสอบ, บันทึก Log)
- [x] ซอร์สโค้ดโปรเจกต์ ESP-IDF ของ **Lab 11.2** ครบถ้วน (`CMakeLists.txt`, `idf_component.yml`, `Lab11-2_MQTT_Client.c`, Log)
- [x] ใบรับรองดิจิทัลและซอร์สโค้ดของ **Lab 11.3** ครบถ้วน (`ca.crt`, `server.crt`, `client.crt`, กุญแจส่วนตัว, `main.c`, `EMBED_TXTFILES`)
- [x] ซอร์สโค้ดโปรเจกต์ ESP-IDF ของ **Lab 11.4** ครบถ้วน (`CMakeLists.txt`, `idf_component.yml`, `main.c`, สคริปต์ CLI, Log)
- [x] รายงานผลการทดลองและการวิเคราะห์ปัญหาเชิงปฏิบัติของทั้ง 4 แล็บ
- [x] เฉลยคำตอบคำถามท้ายการทดลองครบถ้วนทั้ง 12 ข้อ มีการอ้างอิงมาตรฐานทางเทคนิคและหลักการวิศวกรรมอย่างลึกซึ้ง

