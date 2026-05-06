---
tags:
    - cheatSheets
    - serverUtils
---

# Server Utils

Usar systemd para bloquear la suspensión.
```shell
sudo systemctl mask sleep.target suspend.target hibernate.target
hybrid-sleep.target
```

Para deshacerlo:
```shell
sudo systemctl unmask sleep.target suspend.target
hibernate.target hybrid-sleep.target
```
