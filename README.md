# 💧 WaterGuard v5

Sistema de monitoreo inteligente de tanques de agua basado en Arduino. Combina lecturas de nivel, temperatura, humedad y luz ambiente para calcular un **Índice de Riesgo Hídrico (IRH)** en tiempo real, detectar anomalías con una ventana móvil estadística y avisar al usuario mediante LEDs, buzzer y pantalla OLED.

> 📖 **¿Quieres profundizar?** Toda la documentación extendida —incluyendo el análisis offline con K-Means, el detalle de las fórmulas del IRH y el diseño del cableado— está en la **[Wiki del repositorio](../../wiki)**.

---

## 🚀 ¿Qué hace?

- Mide el **nivel de agua** del tanque con un sensor ultrasónico HC-SR04.
- Mide la **temperatura del agua** (DS18B20) y la **temperatura/humedad ambiente** (DHT11).
- Mide la **luz ambiente**, tanto de forma digital (KY-018) como analógica en porcentaje (fotoresistencia).
- Calcula un **IRH (Índice de Riesgo Hídrico)** que resume el estado general del tanque en un solo número de 0 a 100.
- Detecta **anomalías** comparando cada lectura contra el comportamiento reciente (media + desviación estándar de una ventana móvil), no solo contra umbrales fijos.
- Clasifica el sistema en 5 estados: `NORMAL`, `ALERTA`, `CRITICO`, `DEGRADADO` y `ERROR`.
- Reporta todo en una pantalla **OLED** y por el **Monitor Serial**, incluyendo una línea `LOG,...` en formato CSV lista para exportar y analizar en Python.

---

## 🧠 Novedades de la v5

| Cambio | Descripción |
|---|---|
| ☀️ Fotoresistencia analógica | Nuevo sensor en `A0` que da el nivel de luminosidad en 0-100%, en vez del simple "hay luz / no hay luz" del KY-018. |
| 🚨 Detección de anomalías real | La variable `anomalias` (que en v4 quedaba en 0 siempre) ahora se calcula con una ventana móvil sobre el IRH base. |
| 📊 Análisis offline (K-Means) | Documentado como proceso externo: no tiene sentido reentrenar clusters dentro de un Arduino Uno, así que el log se exporta por Serial y se procesa aparte. Detalles completos en la **[Wiki](../../wiki)**. |

---

## 🔌 Hardware y pines

| Componente | Pin | Función |
|---|---|---|
| HC-SR04 (Trig) | D2 | Nivel del tanque |
| HC-SR04 (Echo) | D3 | Nivel del tanque |
| DS18B20 | D4 | Temperatura del agua |
| LED Verde | D5 | Estado NORMAL |
| LED Amarillo | D6 | Estado ALERTA / DEGRADADO |
| LED Rojo | D7 | Estado CRITICO / ERROR |
| Buzzer | D8 | Alarma sonora |
| KY-018 | D9 | Luz (digital) |
| DHT11 | D10 | Temperatura y humedad ambiente |
| Fotoresistencia | A0 | Luminosidad (analógica, 0-100%) |
| OLED SSD1306 | I2C | Pantalla de estado |

---

## 🚦 Estados del sistema

- 🟢 **NORMAL** — todo en orden.
- 🟡 **ALERTA** — el IRH supera el umbral de alerta.
- 🔴 **CRITICO** — el IRH se mantiene alto por un tiempo de confirmación (evita falsas alarmas por un solo pico), con histéresis para salir del estado.
- 🟠 **DEGRADADO** — faltan sensores confiables, el sistema no puede confirmar nada.
- ⚠️ **ERROR** — fallo total: ningún sensor de datos responde.

---

## 🛠️ Librerías necesarias

- `Wire.h`
- `U8x8lib`
- `DHT sensor library`
- `OneWire`
- `DallasTemperature`

## ▶️ Uso

1. Conecta los sensores según la tabla de pines.
2. Sube [WaterGuard_v5.ino](WaterGuard_v5.ino) a tu Arduino desde el IDE.
3. Abre el Monitor Serial a **9600 baudios** para ver el diagnóstico en vivo y el log en CSV.

---

## 📚 Más información

Este README es solo un resumen. El diseño completo del IRH, las justificaciones de cada umbral, el análisis de anomalías y el procesamiento offline con K-Means están documentados con más detalle en la **[Wiki de este repositorio](../../wiki)**.
