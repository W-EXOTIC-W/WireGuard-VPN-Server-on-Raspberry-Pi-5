# LCD Status Display (Optional)

This section explains how I connected a 16x2 I2C LCD screen to my Raspberry Pi to display system information such as the device IP address and CPU temperature.

---

## Hardware Used

- Raspberry Pi 5
- 16x2 I2C LCD Display
- Breadboard
- Jumper wires

---

## Wiring Diagram

![LCD Wiring Diagram](images/lcd-diagram.png)

The LCD screen is connected using the I2C interface.

| Raspberry Pi Pin | LCD Connection |
|------------------|---------------|
| 5V               | VCC           |
| GND              | GND           |
| GPIO2 (SDA)      | SDA           |
| GPIO3 (SCL)      | SCL           |

---

## Create the script file

nano lcd_status.py


Python Script

The following Python script displays the Raspberry Pi CPU temperature and local IP address on the LCD screen.

#!/usr/bin/env python3


from RPLCD.i2c import CharLCD
from time import sleep, strftime
import subprocess

lcd = CharLCD(i2c_expander='PCF8574', address=0x27, port=1,
              cols=16, rows=2, charmap='A00', auto_linebreaks=True)

def get_cpu_temp():
    with open("/sys/class/thermal/thermal_zone0/temp", "r") as f:
        temp = int(f.read()) / 1000.0
    return round(temp, 1)

def get_ip():
    ip = subprocess.check_output("hostname -I", shell=True).decode().strip().split()[0]
    return ip

try:
    while True:
        temp = get_cpu_temp()
        ip = get_ip()

        lcd.clear()
        lcd.write_string(f"CPU:{temp}C")
        lcd.cursor_pos = (1, 0)
        lcd.write_string(f"IP:{ip}")
        sleep(1)

except KeyboardInterrupt:
    lcd.clear()
    lcd.write_string("Goodbye!")
    sleep(2)
    lcd.clear()


CTRL + O
Enter
CTRL + X

Create a Python Virtual Environment

python3 -m venv lcd-env
source lcd-env/bin/activate

Install the required libraries:

pip install RPLCD
pip install smbus2

Output Example

The LCD screen will display something like:

Create a Systemd Service

sudo nano /etc/systemd/system/lcd-display.service

Add the following configuration:

[Unit]
Description=Raspberry Pi LCD Status Display
After=network-online.target

[Service]
ExecStart=/home/matheo/lcd-env/bin/python /home/matheo/lcd_status.py
WorkingDirectory=/home/matheo
Restart=always
User=matheo

[Install]
WantedBy=multi-user.target

Enable and Start the Service
Reload systemd and enable the service:

sudo systemctl daemon-reload
sudo systemctl enable lcd-display.service
sudo systemctl start lcd-display.service

Check the service status:
sudo systemctl status lcd-display.service


CPU:45.1C
IP:192.168.0.135

