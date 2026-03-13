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

The LCD screen is connected using the I2C interface.

| Raspberry Pi Pin | LCD Connection |
|------------------|---------------|
| 5V               | VCC           |
| GND              | GND           |
| GPIO2 (SDA)      | SDA           |
| GPIO3 (SCL)      | SCL           |

<img width="1021" height="827" alt="image" src="https://github.com/user-attachments/assets/f0798a86-b660-4f22-a84a-793a0c50c532" />



---

## Create the script file

nano lcd_status.py


## Python Script

The following Python script displays the Raspberry Pi CPU temperature and local IP address on the LCD screen.

```
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
```

<img width="1920" height="1080" alt="pic11" src="https://github.com/user-attachments/assets/b48593dd-7a7c-4468-8a3a-d74508bad24f" />

CTRL + O
Enter
CTRL + X

## Create a Python Virtual Environment

python3 -m venv lcd-env
source lcd-env/bin/activate

## Install the required libraries:

pip install RPLCD
pip install smbus2

## Create a Systemd Service

sudo nano /etc/systemd/system/lcd-display.service

Add the following configuration:

```
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
```

<img width="1920" height="1080" alt="pic13" src="https://github.com/user-attachments/assets/f4987e39-a8fc-459a-a045-20087a07acd9" />


## Reload systemd and enable the service:

sudo systemctl daemon-reload
sudo systemctl enable lcd-display.service
sudo systemctl start lcd-display.service

## Check the service status:

sudo systemctl status lcd-display.service

<img width="780" height="262" alt="image" src="https://github.com/user-attachments/assets/7a1e5352-785c-430a-ac80-971af84a03c7" />


## Output Example

The LCD screen will display something like:

CPU:45.1C
IP:192.168.0.135

![LCD-Screen](https://github.com/user-attachments/assets/672cfc21-c2ca-4446-b581-95fd16d03042)




