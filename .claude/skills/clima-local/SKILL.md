---
name: clima-local
description: Consulta el tiempo meteorológico actual de una ciudad (por defecto Oviedo, Asturias, España) y lo resume en pocas líneas. Úsala cuando pidan el clima, el tiempo o la temperatura de un lugar.
argument-hint: "[ciudad, región, país]"
---

# Clima local

Obtiene el tiempo actual de una localidad y lo resume en español.

## Ubicación

- Si hay argumento (`$ARGUMENTS`), úsalo como ubicación, p. ej. `Gijón,Asturias,Spain`.
- Si no hay argumento, usa `Oviedo,Asturias,Spain`.
- Escribe siempre la ubicación con ciudad, región y país: wttr.in resuelve nombres ambiguos o pequeños a otros lugares.

## Pasos

1. Consulta los datos (sustituye `UBICACION`, sin espacios; usa `%20` si hace falta):

   ```bash
   curl -s "https://wttr.in/UBICACION?format=j1"
   ```

   Si `curl` no está disponible, usa WebFetch con la misma URL.

2. Del JSON, lee `current_condition[0]` y `nearest_area[0]`:
   - `temp_C`, `FeelsLikeC`, `humidity`, `windspeedKmph`, `winddir16Point`, `precipMM`
   - `lang_es[0].value` o `weatherDesc[0].value` para la descripción
   - `observation_time` y `nearest_area[0].areaName[0].value` (estación real usada)

3. **Comprueba la ubicación**: si `areaName` no corresponde a la ciudad pedida, díselo al usuario en lugar de presentar el dato como si fuera de su ciudad.

## Formato de respuesta

Breve, en español, una o dos líneas:

> Oviedo, 12:21: **25 °C** (sensación 25 °C), cielo cubierto, humedad 44 %, viento SSO 6 km/h, 0,0 mm.

Indica la estación (`areaName`) si difiere de la ciudad pedida, y avisa si la observación es la misma que en una consulta anterior (la fuente no ha actualizado).

## Notas

- La hora de `observation_time` es UTC; conviértela a hora local de España si la muestras.
- Si la petición falla o devuelve vacío, dilo y no inventes datos.
