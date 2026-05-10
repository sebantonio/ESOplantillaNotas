---
name: Estado del proyecto ESOplantillaNotas
description: Estado actualizado 2026-05-10 — Excel ESO analizado, main.rs adaptado, compilando
type: project
---
Proyecto creado el 2026-05-10 replicando la estructura completa de plantillaNotas para el Excel ESO `CCGG PLANTILLA - RECUv45.xlsx`. La app compila y funciona.

**Why:** El usuario quiere una app Tauri para gestionar notas ESO sobre su Excel propio.

**Estado actual (2026-05-10):**
- App compila y genera EXE en `C:\cargo-target\plantillaNotas\release\bundle\nsis\ESO Notas Local_0.1.0_x64-setup.exe`
- Excel analizado: hoja DATOS, tabla de alumnos en A4:B30 (A=nº, B=nombre), unidades en I5:K20 (I=código, J=nombre, K=evaluación), instrumentos en N4:O13 (N=abrev, O=nombre)
- Todos los gestores funcionan: alumnos, unidades, instrumentos, CE+criterios
- index.html adaptado a ESO: branding, botones, modal de importación CSV

**Cambios clave aplicados a main.rs:**
- `load_alumnos`: lee A4:B30 (cols 0/1, filas 3-29)
- `load_unidades`: lee I5:K20 FIJO (cols 8/9/10, filas 4-19) — sin buscar headers
- `save_unidades_to_file`: escribe I5:K20 fijo (cols 8/9/10)
- `load_instrumentos`: lee N4:O13 (cols 13/14, filas 3-12, max 10)
- `save_instrumentos_to_file`: escribe N4:O13 fijo
- Eliminado: toda lógica de "buscar UNIDADES" por header — la tabla es fija

**Cambios en HTML:**
- index.html: branding ESO, modal importar (CSV → Excel activo), botón Instrumentos de Evaluación
- gestor-unidades.html: sin columna Horas, lee I5:K20 directo
- gestor-instrumentos.html: max 10, reescrito en ASCII puro (sin emojis ni tildes)
- gestor-rraa-criterios.html: RA→CE en toda la UI, eliminadas columnas Instituto/Empresa
- app-bridge.js: añadidos getInstrumentos/saveInstrumentos
- scripts/prepare-tauri-web.js: añadido gestor-instrumentos.html a la lista de copia

**How to apply:** La estructura del Excel ESO es diferente al FP. Siempre usar rangos fijos (I5:K20, N4:O13, A4:B30) — no buscar por contenido de celdas.
