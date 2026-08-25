# 💧 WaterGuard v7
Sistema de monitoreo inteligente de tanques de agua basado en Arduino. Combina lecturas de nivel, temperatura, humedad, luz ambiente y luminosidad para calcular un **Índice de Riesgo Hídrico (IRH)** y una **probabilidad de evaporación** en tiempo real, detectar anomalías con una ventana móvil estadística y avisar al usuario mediante LEDs, buzzer y pantalla OLED.

> 📖 **¿Quieres profundizar?** Toda la documentación extendida —incluyendo el análisis offline con K-Means, el detalle de las fórmulas del IRH y de la probabilidad de evaporación, y el diseño del cableado— está en la **[Wiki del repositorio](../../wiki)**.

---

## 🚀 ¿Qué hace?
- Mide el **nivel de agua** del tanque con un sensor ultrasónico HC-SR04.
- Mide la **temperatura del agua** (DS18B20) y la **temperatura/humedad ambiente** (DHT11).
- Mide la **luz ambiente**, tanto de forma digital (KY-018) como analógica en porcentaje (fotoresistencia).
- Calcula un **IRH (Índice de Riesgo Hídrico)** que resume el estado general del tanque en un solo número de 0 a 100.
- Calcula una **probabilidad de evaporación** (0-100%), combinando temperatura del agua, humedad, luminosidad y temperatura ambiente, cada una con su propio peso.
- Detecta **anomalías** comparando cada lectura contra el comportamiento reciente (media + desviación estándar de una ventana móvil), no solo contra umbrales fijos.
- Clasifica el sistema en 5 estados: `NORMAL`, `ALERTA`, `CRITICO`, `DEGRADADO` y `ERROR`.
- Reporta todo en una pantalla **OLED** legible de un vistazo (estados en palabras completas) y por el **Monitor Serial**, incluyendo una línea `LOG,...` en formato CSV lista para exportar y analizar en Python.

---

## 🧠 Novedades de la v6 y v7

| Versión | Cambio | Descripción |
|---|---|---|
| v6 | 💦 Probabilidad de evaporación | Nuevo índice compuesto (0-100%) calculado a partir de temperatura del agua, humedad, luminosidad y temperatura ambiente, normalizado solo sobre los sensores disponibles — igual patrón que el IRH. Se agrega también como columna al log CSV por Serial. |
| v7 | 📟 Evaporación como lectura propia | La probabilidad de evaporación deja de mezclarse entre los datos internos del IRH y pasa a mostrarse junto a nivel, temperatura y humedad, con número y categoría en palabras: BAJA / MEDIA / ALTA. |
| v7 | 🔤 OLED en palabras completas | El estado del sistema y si hay anomalía activa ahora se imprimen como "Estado: NORMAL" / "Anomalia: SI/NO" en vez de códigos de una letra, para leerse de un vistazo sin interpretar abreviaturas. |

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

## 💦 Probabilidad de evaporación
Índice heurístico (no un modelo físico exacto, ya que faltarían variables como viento y presión) que combina varias señales para estimar qué tan probable es la evaporación en el tanque:

| Variable | Peso |
|---|---|
| Temperatura del agua | 35 |
| Humedad | 30 |
| Luminosidad | 20 |
| Temperatura ambiente | 15 |

Se normaliza solo sobre los sensores disponibles en ese momento (mismo patrón que el IRH), de modo que ninguna variable sola puede mover el número por sí misma. Se muestra en OLED y Serial con número y categoría en palabras: **BAJA** (<30%), **MEDIA** (30-60%), **ALTA** (≥60%).

---

## 🛠️ Librerías necesarias
- `Wire.h`
- `U8x8lib`
- `DHT sensor library`
- `OneWire`
- `DallasTemperature`

## ▶️ Uso
1. Conecta los sensores según la tabla de pines.
2. Sube [WaterGuard_v7.ino](WaterGuard_v7.ino) a tu Arduino desde el IDE.
3. Abre el Monitor Serial a **9600 baudios** para ver el diagnóstico en vivo y el log en CSV.

---

## 📚 Más información
Este README es solo un resumen. El diseño completo del IRH, la fórmula de la probabilidad de evaporación, las justificaciones de cada umbral, el análisis de anomalías y el procesamiento offline con K-Means están documentados con más detalle en la **[Wiki de este repositorio](../../wiki)**.
