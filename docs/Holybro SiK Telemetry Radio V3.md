# Guía de Uso: Holybro SiK Telemetry Radio V3

## Descripción General

El Holybro SiK Telemetry Radio V3 es un transceptor de radio de 915MHz/433MHz utilizado comúnmente para telemetría en drones y vehículos autónomos. Utiliza el firmware SiK y se configura mediante comandos AT.

---

## Conexión Serial con tio

### Instalación de tio (Ubuntu)

```bash
sudo apt install tio
```

Si no está disponible en los repositorios:

```bash
sudo apt install meson ninja-build pkg-config libinih-dev libglib2.0-dev liblua5.4-dev
git clone https://github.com/tio/tio.git
cd tio
meson setup build
meson compile -C build
sudo meson install -C build
```

### Conexión básica

```bash
tio -b 57600 /dev/ttyUSB0
```

> **Nota:** El baudrate por defecto es 57600. Si no responde, prueba con 115200 o 9600.

### Atajos de teclado en tio

|Atajo|Función|
|---|---|
|`Ctrl+t q`|Salir de tio|
|`Ctrl+t ?`|Mostrar ayuda|
|`Ctrl+t l`|Activar/desactivar log|
|`Ctrl+t h`|Mostrar hexadecimal|

---

## Modo de Comandos AT (de la radio)

### Entrar al modo comando

1. Conectarse al radio con tio
2. **Esperar 1 segundo** sin escribir nada
3. Escribir rápidamente: `+++`
4. **Esperar 1 segundo** sin escribir nada
5. El radio responderá con `OK`

> **Importante:** Los tiempos de espera son críticos. Si escribes antes o después del tiempo, no entrará en modo comando.

---

## Comandos AT de Referencia

### Comandos de información

|Comando|Descripción|
|---|---|
|`ATI`|Versión del firmware|
|`ATI2`|Parámetros del radio|
|`ATI3`|Versión de la placa|
|`ATI4`|Frecuencias soportadas|
|`ATI5`|Todos los parámetros (formato S)|
|`ATI6`|Estadísticas de transmisión|
|`ATI7`|Estadísticas del RSSI|

### Comandos de control

|Comando|Descripción|
|---|---|
|`ATO`|Salir del modo comando|
|`ATZ`|Reiniciar el radio|
|`AT&F`|Restaurar valores de fábrica|
|`AT&W`|Guardar cambios en EEPROM|
|`AT&T=RSSI`|Mostrar RSSI en tiempo real|
|`AT&T`|Desactivar modo debug|

### Comandos de parámetros (Sn)

|Comando|Descripción|
|---|---|
|`ATSn?`|Leer valor del parámetro n|
|`ATSn=valor`|Establecer valor del parámetro n|

---

## Parámetros Configurables (Registros S)

|Registro|Nombre|Descripción|Valores típicos|
|---|---|---|---|
|S0|FORMAT|Formato de respuesta|0-1|
|S1|SERIAL_SPEED|Velocidad serial (kbps)|1-230|
|S2|AIR_SPEED|Velocidad de aire (kbps)|2-250|
|S3|NETID|ID de red (debe coincidir en ambos radios)|0-499|
|S4|TXPOWER|Potencia de transmisión (dBm)|0-30|
|S5|ECC|Corrección de errores|0=off, 1=on|
|S6|MAVLINK|Modo MAVLink|0=raw, 1=MAVLink, 2=Low Latency|
|S7|OPPRESEND|Reenvío oportunista|0=off, 1=on|
|S8|MIN_FREQ|Frecuencia mínima (kHz)|Ej: 915000|
|S9|MAX_FREQ|Frecuencia máxima (kHz)|Ej: 928000|
|S10|NUM_CHANNELS|Número de canales de salto|1-50|
|S11|DUTY_CYCLE|Ciclo de trabajo (%)|10-100|
|S12|LBT_RSSI|Listen Before Talk RSSI|0=off, >0=threshold|
|S13|MANCHESTER|Codificación Manchester|0=off, 1=on|
|S14|RTSCTS|Control de flujo RTS/CTS|0=off, 1=on|
|S15|MAX_WINDOW|Ventana máxima (ms)|33-400|

---

## Ejemplos Prácticos

### Ver configuración actual

```
+++          # Entrar modo comando (esperar OK)
ATI5         # Ver todos los parámetros
ATO          # Salir del modo comando
```

### Cambiar NETID (ambos radios deben tener el mismo)

```
+++
ATS3=25      # Cambiar NETID a 25
AT&W         # Guardar en EEPROM
ATZ          # Reiniciar
```

### Aumentar potencia de transmisión

```
+++
ATS4=20      # Establecer potencia a 20 dBm
AT&W
ATZ
```

### Monitorear señal RSSI

```
+++
AT&T=RSSI    # Activar monitor RSSI
             # (muestra nivel de señal en tiempo real)
AT&T         # Desactivar monitor
ATO
```

### Configuración típica para drones

```
+++
ATS1=57      # Serial speed: 57600 bps
ATS2=64      # Air speed: 64 kbps
ATS3=25      # NETID: 25
ATS4=20      # TX Power: 20 dBm
ATS5=1       # ECC: habilitado
ATS6=1       # MAVLink: habilitado
AT&W         # Guardar
ATZ          # Reiniciar
```

---

## Troubleshooting

### El radio no responde a +++

- Verifica el baudrate (prueba 57600, 115200, 9600)
- Asegúrate de esperar 1 segundo antes y después de +++
- Verifica que el puerto serial sea correcto (`ls /dev/ttyUSB*`)

### No hay comunicación entre radios

- Verifica que ambos tengan el mismo NETID (S3)
- Verifica que las frecuencias coincidan (S8, S9)
- Usa `AT&T=RSSI` para verificar si hay señal

### Permisos denegados en el puerto serial

```bash
sudo usermod -a -G dialout $USER
# Cerrar sesión y volver a iniciar
```

---

## Script de Configuración Rápida

```bash
#!/bin/bash
# sik_config.sh - Configuración rápida de radio SiK

PORT=${1:-/dev/ttyUSB0}
BAUD=${2:-57600}

echo "Conectando a $PORT @ $BAUD..."
echo "Esperando 2 segundos..."
sleep 2

{
    sleep 1
    echo -n "+++"
    sleep 1
    echo "ATI"
    sleep 0.5
    echo "ATI5"
    sleep 0.5
    echo "ATO"
} | tio -b $BAUD $PORT
```

---

## Referencias

- [SiK Radio Firmware (GitHub)](https://github.com/ArduPilot/SiK)
- [Holybro SiK Telemetry Radio](https://holybro.com/collections/telemetry-radios)
- [tio - Terminal I/O](https://github.com/tio/tio)

---

_Última actualización: Diciembre 2024_