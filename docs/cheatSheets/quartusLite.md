---
tags:
    - cheatSheets
    - quartusLite
---

# Quartus Lite

Para correr Quartus Lite
```shell
cd ~/altera_lite/25.1std/quartus/bin

./quartus --64bit
```

Para reiniciar jtag
```shell
cd /home/dafondron/altera_lite/25.1std/quartus/bin

sudo killall jtagd 
sudo ./jtagd &
```
