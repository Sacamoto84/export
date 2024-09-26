Создаем два файла
```bash
sudo nano /home/orangepi/reboot.py
```

/home/orangepi/reboot.py
```python
                                                                             
import OPi.GPIO as GPIO
import os
import time

# Отключаем предупреждения
GPIO.setwarnings(False)

# Настройка пина PC8 как вход
GPIO.setmode(GPIO.SUNXI)  # Используем нумерацию SUNXI для Orange Pi
GPIO.setup("PC8", GPIO.IN)  # Убираем настройку подтяжки

try:
    while True:
        if GPIO.input("PC8") == GPIO.LOW:  # Проверяем, если сигнал на пине PC8 стал низким
            print("PC8 is LOW. Rebooting...")
            os.system("sudo reboot")
        time.sleep(0.1)  # Пауза между проверками
except KeyboardInterrupt:
    GPIO.cleanup()  # Возвращаем пины в исходное состояние

```

```bash
sudo nano /home/orangepi/reboot.py
```

/home/orangepi/poweroff.py  
```python
import OPi.GPIO as GPIO
import os
import time

GPIO.setwarnings(False)

GPIO.setmode(GPIO.SUNXI)
GPIO.setup("PC7", GPIO.IN)

try:
    while True:
        if GPIO.input("PC7") == GPIO.LOW:
            print("PC7 is LOW. Shutting down...")
            os.system("sudo poweroff") 
        time.sleep(0.1)
except KeyboardInterrupt:
    GPIO.cleanup()

```

Выдать права на файлы
```shell
sudo chmod +x reboot.py
sudo chmod +x poweroff.py
```

Создаем регистрацию службы
```bash
sudo nano /etc/systemd/system/reboot-on-pc7.service
```

```bash
[Unit]
Description=PowerOff on PC7 signal

[Service]
ExecStart=/usr/bin/python3 /home/orangepi/poweroff.py
Restart=always

[Install]
WantedBy=multi-user.target
```

```bash
sudo nano /etc/systemd/system/reboot-on-pc8.service
```

```bash
[Unit]
Description=Reboot on PC8 signal

[Service]
ExecStart=/usr/bin/python3 /home/orangepi/reboot.py
Restart=always

[Install]
WantedBy=multi-user.target
```

Разрешаем службы
```bash
sudo systemctl enable reboot-on-pc7.service
sudo systemctl enable reboot-on-pc8.service
```

Профит...

Дополнительно:

Если нужно
```bash
sudo apt update 
sudo apt install python3-pip 
sudo pip3 install OPi.GPIO
```

Запуск скрипта руками для проверки
```bash
sudo python3 reboot.py
```

