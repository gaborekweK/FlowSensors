# ESP8266 Flow Sensor Monitor

A real-time flow sensor monitoring system using ESP8266 with web-based dashboard for measuring and recording liquid flow rates from two SLF3X sensors.

![Dashboard](FlowSensor_UI_ESP8266/UI/dashboard.png)

## Features

- **Dual Sensor Support**: Monitor two SLF3X flow sensors simultaneously
- **Real-time Monitoring**: 20Hz sampling rate with live web dashboard
- **Statistical Analysis**: Real-time calculation of mean, RMS, and coefficient of variation (CV)
- **Data Recording**: Record measurements to CSV files with 0.5-second intervals
- **Web Interface**: Modern, responsive dashboard accessible from any device
- **WiFi Connectivity**: Remote monitoring via WiFi connection
- **I2C Multiplexer Support**: Uses TCA9548A for multiple sensor management

## Hardware Requirements

### Components
- ESP8266 development board (NodeMCU, Wemos D1 Mini, etc.)
- 2x SLF3X flow sensors (Sensirion)
- 1x TCA9548A I2C multiplexer 
- Breadboard and jumper wires

### Pin Connections

#### ESP8266 to TCA9548A (I2C Multiplexer)
| ESP8266 Pin | TCA9548A Pin | Description |
|-------------|--------------|-------------|
| D1 (GPIO5)  | SCL          | I2C Clock   |
| D2 (GPIO4)  | SDA          | I2C Data    |
| 3.3V        | VCC          | Power       |
| GND         | GND          | Ground      |

#### TCA9548A to Flow Sensors
| TCA9548A Channel | Sensor | SLF3X Pin | Description |
|------------------|--------|-----------|-------------|
| Channel 0        | Sensor 1| SCL/SDA  | I2C Bus     |
| Channel 1        | Sensor 2| SCL/SDA  | I2C Bus     |

#### Power Connections
- Connect 3.3V and GND to both flow sensors
- Each sensor requires 3.3V power supply

### Alternative Setup (Without Multiplexer)
If not using TCA9548A, connect sensors directly with different I2C addresses:
- Sensor 1: Address 0x08
- Sensor 2: Address 0x08 (modify `sensors.h` for different addresses)

## Software Setup

### 1. Arduino IDE Configuration
1. Install ESP8266 board package in Arduino IDE
2. Install required libraries:
   - ESP8266WiFi
   - ESP8266mDNS
   - LittleFS

### 2. Configure WiFi Settings
Edit the following lines in `FlowSensor_UI_ESP8266.ino`:
```cpp
const char* WIFI_SSID = "YOUR_WIFI_NAME";     // Replace with your WiFi network name
const char* WIFI_PASS = "YOUR_WIFI_PASSWORD"; // Replace with your WiFi password
const char* MDNS_HOST = "flowsensor";          // Optional: Change hostname
```

### 3. Upload Code
1. Connect ESP8266 to computer via USB
2. Select correct board and port in Arduino IDE
3. Upload `FlowSensor_UI_ESP8266.ino`

### 4. Hardware Configuration
Verify pin assignments in `sensors.h`:
```cpp
#define SDA_PIN        4          // ESP8266 D2
#define SCL_PIN        5          // ESP8266 D1
#define TCA_ADDR       0x70       // TCA9548A I2C address
#define S1_MUX_CH      0          // Sensor 1 multiplexer channel
#define S2_MUX_CH      1          // Sensor 2 multiplexer channel
```

## Usage

### 1. Power On and Connect
1. Power the ESP8266 via USB or external supply
2. Monitor serial output (115200 baud) for connection status
3. Note the IP address displayed in serial monitor

### 2. Access Web Dashboard
- **Local IP**: Open browser to the IP address shown in serial monitor
- **mDNS** (if supported): `http://flowsensor.local` (or your custom hostname)

### 3. Web Interface Functions

#### Real-time Display
- **Sensor Status**: Shows "OK" or "ERR" for each sensor
- **Live Measurements**: 
  - 1-second averaged flow rate (mL/min)
  - Temperature (°C)
- **10-second Statistics**:
  - Mean flow rate over 10 seconds
  - RMS (Root Mean Square) value
  - CV (Coefficient of Variation) as percentage
- **Rolling History**: Last 10 measurements displayed in tables

#### Recording Controls
- **Start Button**: Begin recording measurements to CSV file
- **Stop Button**: End recording session
- **Download CSV**: Download recorded data (appears after stopping)

### 4. Data Recording and Download

#### Recording Process
1. Click "Start" to begin recording
2. System records averaged measurements every 0.5 seconds
3. Click "Stop" to end recording
4. "Download CSV" button appears when data is ready

#### CSV Format
Downloaded files contain:
```csv
time_s,s1_flow_ml_min,s1_temp_c,s2_flow_ml_min,s2_temp_c
0.000,12.345,23.45,15.678,24.12
0.500,12.289,23.47,15.723,24.15
...
```

Columns:
- `time_s`: Time in seconds from recording start
- `s1_flow_ml_min`: Sensor 1 flow rate (mL/min)
- `s1_temp_c`: Sensor 1 temperature (°C)
- `s2_flow_ml_min`: Sensor 2 flow rate (mL/min)
- `s2_temp_c`: Sensor 2 temperature (°C)

## Technical Specifications

- **Sampling Rate**: 20 Hz continuous monitoring
- **Recording Rate**: 0.5-second averages when recording
- **Flow Range**: Dependent on SLF3X sensor model
- **Temperature Range**: Dependent on SLF3X sensor specifications
- **Data Storage**: Local flash memory (LittleFS)
- **Network**: WiFi 802.11 b/g/n
- **Web Server**: HTTP on port 80

## Troubleshooting

### Common Issues

1. **Sensors show "ERR" status**
   - Check I2C wiring connections
   - Verify sensor power (3.3V)
   - Confirm TCA9548A configuration

2. **Cannot connect to WiFi**
   - Verify SSID and password in code
   - Check WiFi signal strength
   - Ensure 2.4GHz network (ESP8266 doesn't support 5GHz)

3. **Web interface not accessible**
   - Check serial monitor for IP address
   - Verify device is on same network
   - Try direct IP access instead of mDNS

4. **CSV download fails**
   - Ensure recording was started and stopped
   - Check available flash memory
   - Try refreshing browser

### Serial Monitor Output
Monitor the serial output for diagnostic information:
- WiFi connection status and IP address
- Sensor initialization status
- Recording start/stop events
- HTTP server status

## License

This project is open source and available for modification and distribution.

## Contributing

Feel free to submit issues, fork the repository, and create pull requests for any improvements.