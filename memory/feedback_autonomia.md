---
name: Autonomía y copias de seguridad
description: El usuario prefiere autonomía total pero siempre con copia de seguridad previa a cambios grandes
type: feedback
---
Actúa con autonomía total en cambios de código — no pedir permiso para cada modificación cuando la tarea ya está acordada.

**Why:** El usuario lo indicó explícitamente en plantillaNotas: "puedes hacer las modificaciones que creas necesarias, no pidas permiso". Mismo comportamiento esperado en este proyecto.

**How to apply:** Cuando hay una tarea acordada, ejecutarla directamente. Antes de reescrituras grandes del repo, crear backup externo o carpeta `copiaseguridad/` con los archivos afectados. Para guardados Excel de la app, mantener el patrón automático: temporal + validación + copia `*.autobak.xlsx`/`*.autobak.xlsm`. No preguntar "¿procedo?" antes de cada paso si la tarea ya está clara.
