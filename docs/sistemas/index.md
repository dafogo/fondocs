---
tags:
  - sistemas
  - modelación
  - función de transferencia
  - control
---

# Modelación de Sistemas

## ¿Qué es un sistema?

Un **sistema** es cualquier proceso que recibe una o más **entradas** y produce una o más **salidas**. El objetivo de la modelación es describir matemáticamente esa relación entrada-salida.

```
Entrada u(t)  →  [ Sistema ]  →  Salida y(t)
```

---

## Clasificación de sistemas

| Propiedad | Tipos |
|---|---|
| Linealidad | Lineal / No lineal |
| Variación en el tiempo | Invariante (LTI) / Variante |
| Señales | Continuo / Discreto |
| Entradas/Salidas | SISO / MIMO |

Los sistemas **LTI** (Linear Time-Invariant) son los más estudiados porque tienen herramientas potentes: transformada de Laplace, función de transferencia, diagramas de Bode, etc.

Un sistema es **lineal** si cumple **superposición**:

\[
f(a \cdot x_1 + b \cdot x_2) = a \cdot f(x_1) + b \cdot f(x_2)
\]

---

## Transformada de Laplace

Herramienta fundamental: convierte ecuaciones diferenciales (dominio del tiempo) en ecuaciones algebraicas (dominio de \(s\)).

**Definición:**

\[
\mathcal{L}\{f(t)\} = F(s) = \int_0^{\infty} f(t)\, e^{-st}\, dt
\]

### Pares comunes

| \(f(t)\) | \(F(s)\) |
|---|---|
| Impulso \(\delta(t)\) | \(1\) |
| Escalón \(u(t)\) | \(\dfrac{1}{s}\) |
| Rampa \(t\) | \(\dfrac{1}{s^2}\) |
| Exponencial \(e^{-at}\) | \(\dfrac{1}{s+a}\) |
| Seno \(\sin(\omega t)\) | \(\dfrac{\omega}{s^2+\omega^2}\) |
| Coseno \(\cos(\omega t)\) | \(\dfrac{s}{s^2+\omega^2}\) |

### Propiedades clave

| Propiedad | Tiempo | Laplace |
|---|---|---|
| Derivada | \(\dot{f}(t)\) | \(s F(s) - f(0)\) |
| Integral | \(\int f\, dt\) | \(\dfrac{F(s)}{s}\) |
| Desplazamiento | \(f(t-T)\) | \(e^{-Ts} F(s)\) |

---

## Función de Transferencia

La **función de transferencia** \(G(s)\) de un sistema LTI es la relación entre la transformada de Laplace de la salida y la entrada, **con condiciones iniciales cero**:

\[
G(s) = \frac{Y(s)}{U(s)}
\]

### Cómo obtenerla

1. Escribir la ecuación diferencial del sistema
2. Aplicar Laplace (condiciones iniciales = 0)
3. Despejar \(\dfrac{Y(s)}{U(s)}\)

### Ejemplo: circuito RC

Para un circuito RC con entrada \(V_{in}\) y salida \(V_{out}\) en el capacitor:

\[
RC\, \dot{v}_{out}(t) + v_{out}(t) = v_{in}(t)
\]

Aplicando Laplace:

\[
RCs\, V_{out}(s) + V_{out}(s) = V_{in}(s)
\]

\[
G(s) = \frac{V_{out}(s)}{V_{in}(s)} = \frac{1}{RCs + 1} = \frac{1}{\tau s + 1}
\]

donde \(\tau = RC\) es la **constante de tiempo** del sistema.

### Polos y ceros

Toda función de transferencia racional tiene la forma:

\[
G(s) = K \frac{(s - z_1)(s - z_2)\cdots}{(s - p_1)(s - p_2)\cdots}
\]

- **Ceros** \(z_i\): valores de \(s\) donde \(G(s) = 0\)
- **Polos** \(p_i\): valores de \(s\) donde \(G(s) \to \infty\)

Los polos determinan la **estabilidad** y la **dinámica** del sistema. Si todos los polos tienen parte real negativa, el sistema es **estable**.

---

## Diagramas de bloques

Representación gráfica de cómo interactúan los subsistemas.

### Conexión en serie

\[
G(s) = G_1(s) \cdot G_2(s)
\]

### Conexión en paralelo

\[
G(s) = G_1(s) + G_2(s)
\]

### Lazo cerrado (retroalimentación negativa)

```
       +        G(s)
R(s) ---->O--->[G(s)]----> Y(s)
          ↑                  |
          |---[H(s)]<--------|
          -
```

\[
\frac{Y(s)}{R(s)} = \frac{G(s)}{1 + G(s)H(s)}
\]

Si \(H(s) = 1\) (retroalimentación unitaria):

\[
\frac{Y(s)}{R(s)} = \frac{G(s)}{1 + G(s)}
\]

---

## Respuesta temporal

La respuesta de un sistema a una entrada escalón unitario es el análisis más común.

### Sistema de primer orden

\[
G(s) = \frac{K}{\tau s + 1}
\]

- **Constante de tiempo** \(\tau\): tiempo en llegar al 63.2% del valor final
- **Ganancia estática** \(K\): valor final con entrada escalón
- A \(t = 5\tau\) el sistema alcanza el 99% → se considera establecido

### Sistema de segundo orden

\[
G(s) = \frac{\omega_n^2}{s^2 + 2\zeta\omega_n s + \omega_n^2}
\]

| Parámetro | Símbolo | Efecto |
|---|---|---|
| Frecuencia natural | \(\omega_n\) | Velocidad de respuesta |
| Factor de amortiguamiento | \(\zeta\) | Forma de la respuesta |

| \(\zeta\) | Comportamiento |
|---|---|
| \(\zeta = 0\) | Oscilación permanente |
| \(0 < \zeta < 1\) | Subamortiguado (oscila y se establece) |
| \(\zeta = 1\) | Críticamente amortiguado |
| \(\zeta > 1\) | Sobreamortiguado (lento, sin oscilación) |

---

## Herramientas de software

| Herramienta | Uso |
|---|---|
| **MATLAB / Simulink** | Estándar industrial, muy completo |
| **Python + Control** | Librería `control` open source, equivalente a MATLAB |
| **Python + SciPy** | `scipy.signal` para análisis de señales y sistemas |
| **Octave** | Alternativa gratuita a MATLAB |

### Ejemplo rápido en Python

```python
import control as ct
import matplotlib.pyplot as plt

# G(s) = 1 / (s^2 + 0.5s + 1)
G = ct.tf([1], [1, 0.5, 1])

# Respuesta al escalón
t, y = ct.step_response(G)
plt.plot(t, y)
plt.xlabel('Tiempo (s)')
plt.ylabel('Amplitud')
plt.grid(True)
plt.show()
```

---

!!! note "Páginas por agregar"
    - Análisis en frecuencia: diagramas de Bode
    - Lugar geométrico de las raíces (Root Locus)
    - Controladores PID
    - Espacio de estados
    - Sistemas discretos y transformada Z
