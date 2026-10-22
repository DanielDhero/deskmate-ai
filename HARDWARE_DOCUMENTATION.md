# 🤖 Laporan Progres Hardware (Minggu 1-2) - M. Ilham Ramadhana

Dokumen ini merupakan pemenuhan Initial Project Backlog **T-01 (Pengadaan komponen & perakitan hardware; setup repo)**, **T-04 (Casing robot)**, serta pencapaian **Milestone M2 (System Design Architecture)** dari sisi teknis hardware.

---

## 1. Bill of Materials (BOM) & Spesifikasi Komponen Resmi

Berdasarkan hasil riset komparasi harga dan rating toko (4.9 - 5.0), berikut daftar lengkap komponen elektronik, speaker, rencana casing fisik, dan perkakas bantu untuk pengadaan hardware Xiaozhi AI Desk Robot:

> 📁 **Dokumen Tangkapan Layar Komponen:**  
> [Daftar Komponen & SS Belanja Shopee](https://docs.google.com/document/d/1I73vNSf51nehUgsLfrKjjqcRRP_cEJMayi3WSkrIXMk/edit?usp=sharing)

### A. Komponen Utama (Eceran Terverifikasi)

1. **Main Processing Unit (MCU):** ESP32-S3 DevKitC-1 N16R8 Type-C (Pre-soldered Header Pins)
   * *Harga:* Rp 99.000
   * *Link Belanja:* https://shopee.co.id/ESP32-S3-N16R8-DevKitC-1-USB-type-C-ESP32-S3-Development-Board-i.1346118344.44382181573

2. **Audio Input:** INMP441 Omnidirectional Microphone Module I2S
   * *Harga:* Rp 31.900 - Rp 32.900
   * *Link Belanja:* https://shopee.co.id/Modul-Mic-INMP441-INMP-441-Omnidirectional-I2S-MEMS-Microphone-Sensor-Module-ESP32-i.1555409167.48415516164

3. **Audio Output (Amplifier):** MAX98357A I2S 3W Class-D Mono Amplifier DAC Module
   * *Harga:* Rp 50.300
   * *Link Belanja:* https://shopee.co.id/MAX98357-I2S-3W-Class-D-Amplifier-DAC-Decoder-Module-MAX98357A-i.906610.27553511943

4. **Audio Output (Transducer):** Small Mini Speaker 4 Ohm 3 Watt (30x20x5mm)
   * *Harga:* Rp 35.900
   * *Link Belanja:* https://shopee.co.id/Small-Mini-Speaker-4-8-Ohm-1-2-3-Watt-1W-2W-3W-i.28984350.20494047784

5. **Display Wajah:** Layar LCD TFT Bulat 1.28 Inch IPS Display (Driver IC GC9A01)
   * *Harga:* Rp 64.500
   * *Link Belanja:* https://shopee.co.id/TFT-LCD-1.28-1.28-Inch-RGB-Round-Display-Module-SPI-Interface-GC9A01-i.1242367.56905781139

### B. Prototyping & Casing Struktural (T-04)

1. **Solderless Mini Breadboard SYB-120 (400 Tie-Points):**
   * *Harga:* Rp 9.000
   * *Link Belanja:* https://shopee.co.id/MINI-SOLDERLESS-BREADBOARD-400P-TIE-POINTS-MINI-SOLDERLESS-400-TITIK-i.7898671.23659424085

2. **Kabel Jumper DuPont Kombinasi (40p):**
   * *Harga:* Rp 9.400 - Rp 16.400
   * *Link Belanja:* https://shopee.co.id/Kabel-Jumper-Female-to-Female-Male-to-Male-Female-to-Male-40p-Dupont-Wire-untuk-Breadboard-10-20-30-40-cm-i.421203311.24691269735

3. **Chassis / Enclosure Robot Low-Fidelity:**
   * *Estimasi Biaya:* Rp 0 (Kardus ketebalan ganda / daur ulang).
   * *Alasan Teknis:* Digunakan pada fase awal (Prototype v1) untuk memangkas biaya produksi hingga 100% pada sektor casing fisik, serta mempercepat proses pengujian tata letak komponen (*mock-up layout*).

**Total Estimasi Anggaran Belanja Komponen Fisik:** **± Rp 308.000**

---

## 🗺️ 2. M2 – System Design Architecture & Desain Teknis

### A. High-Level Hardware Architecture

Perancangan catu daya dilakukan tanpa baterai (Desk Robot Type-C direct pass) untuk menjamin keamanan dari risiko baterai kembung/meledak dan efisiensi biaya.

```text
               +----------------------------------+
               |  Host Power Source (USB Type-C)  |
               |       5V DC / Direct Pass        |
               +----------------+-----------------+
                                |
                                v
               +----------------------------------+
               |    Main Processing Unit (MCU)    |
               |  ESP32-S3 DevKitC-1 (N16R8 Flash) |
               +--------+----------------+--------+
                        |                |
        +---------------+                +---------------+
        | (I2S Protocol)                 | (SPI Protocol)| 3.3V Logic
        v                                v               v
+---------------+                +---------------+---------------+
| Audio Input   |                | Audio Output  | Display Unit  |
| INMP441 Mic   |                | MAX98357A DAC | GC9A01 LCD    |
| Module (I2S)  |                | + Speaker 3W  | 1.28" Round   |
+---------------+                +---------------+---------------+
```
---

### B. Pinout & Wiring Interconnect Matrix

Sistem pengkabelan internal berbasis *Solderless Mini Breadboard* SYB-120 dan jumper DuPont untuk mempermudah perakitan tanpa solder:

| Peripheral Module | Physical Pin | ESP32-S3 GPIO | Bus / Protocol | Functional Description |
| :--- | :--- | :--- | :--- | :--- |
| **INMP441** *(Mic)* | VCC | `3V3` | Power | Supply tegangan logika 3.3V |
| | GND | `GND` | Ground | Common Ground |
| | WS | `GPIO 42` | I2S | Word Select / LRCLK |
| | SCK | `GPIO 41` | I2S | Bit Clock (BCLK) |
| | SD | `GPIO 2` | I2S | Serial Data Output |
| **MAX98357A** *(Amp)* | VIN | `5V` | Power | Supply daya utama amplifier 5V |
| | GND | `GND` | Ground | Common Ground |
| | LRC | `GPIO 45` | I2S | Left/Right Clock Select |
| | BCLK | `GPIO 5` | I2S | Bit Clock |
| | DIN | `GPIO 6` | I2S | Serial Data Input |
| **GC9A01** *(Display)* | VCC | `3V3` | Power | Supply tegangan display 3.3V |
| | GND | `GND` | Ground | Common Ground |
| | SCL / SCK | `GPIO 12` | SPI | Serial Clock Line |
| | SDA / MOSI | `GPIO 11` | SPI | Master Output Slave Input |
| | DC / RES | `GPIO 10 / 9` | Control | Display Command / Reset |

---

## 3. Target Aksi Minggu Selanjutnya (Fase Implementasi)

- [ ] Pembelian dan verifikasi fisik seluruh komponen yang ada di daftar BOM Shopee.
- [ ] Pengujian awal alur suara bawaan (*flash firmware* `xiaozhi-esp32`) bersama Muliadi.
- [ ] Pemotongan kompartemen sasis kardus low-fidelity (*mock-up layout*) setelah komponen tiba untuk pengaturan manajemen kabel yang rapi.