---
tags:
    - cheatSheets
    - droneFlashing
---

# Drone Flashing

Usar systemd para bloquear la suspensión.
```shell
dafondron@fondiu-PC:~/vanttec/vanttec_swarm/drone_configurator$
python3 main.py --mavlink_connection /dev/ttyACM0
```
Después de flashear con python script, asegurarse de que estos parámetros sean correctos:

+ [x] SHOW_LED0_CHAN -> 2
+ [x] SHOW_LED0_TYPE -> 1
+ [x] S_THR_ENABLE -> Disabled
+ [x] SYSID_THISMAV -> (id del dron)
