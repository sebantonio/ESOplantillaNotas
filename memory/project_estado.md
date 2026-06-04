---
name: Estado del proyecto ESOplantillaNotas
description: Estado actualizado 2026-06-05 — seguridad Excel, CSP, vendor local y build Tauri
type: project
---
Proyecto creado el 2026-05-10 replicando la estructura completa de plantillaNotas para el Excel ESO `CCGG PLANTILLA - RECUv45.xlsx`. La app compila y funciona.

**Why:** El usuario quiere una app Tauri para gestionar notas ESO sobre su Excel propio.

**Estado actual (2026-06-05):**
- App compila con `cargo check` y tests Rust unitarios.
- Build recomendado: `npm run tauri:build`, que incrementa version, prepara `tauri-web/`, ejecuta `tauri build` y copia instalador a `exe/`.
- Excel editable: `.xlsx` y `.xlsm`; `.xls` queda bloqueado para evitar escrituras inseguras.
- Escritura Excel segura: temporal + validacion ZIP + copia `*.autobak.xlsx`/`*.autobak.xlsm` + reemplazo final.
- `xlsx.full.min.js` se sirve local desde `vendor/`; no quedan scripts CDN en HTML.
- CSP Tauri activa con origen local e IPC permitido.
- Enlaces externos restringidos: solo `http`/`https` seguros, bloqueando local, localhost y rangos privados.
- Modo oscuro de `ux-common.css` usa variables CSS, no `filter: invert`.
- Acciones destructivas evidentes usan confirmacion comun en `ux-common.js`.

**Cambios clave aplicados a main.rs:**
- `load_alumnos`: lee A4:B41, max 37 alumnos.
- `load_unidades`: lee I5:K20 FIJO (cols 8/9/10, filas 4-19)
- `save_unidades_to_file`: escribe I5:K20 fijo (cols 8/9/10)
- `load_instrumentos`: lee N4:O13 (cols 13/14, filas 3-12, max 10)
- `save_instrumentos_to_file`: escribe N4:O13 fijo
- `write_excel_safely`: centraliza guardado seguro en Tauri.
- `parse_grade`: valida notas 0-10 antes de escribir.

**Cambios en HTML:**
- index.html: branding ESO, modal importar (CSV → Excel activo), botón Instrumentos de Evaluación
- gestor-unidades.html: sin columna Horas, lee I5:K20 directo
- gestor-instrumentos.html: max 10, reescrito en ASCII puro (sin emojis ni tildes)
- gestor-rraa-criterios.html: RA→CE en toda la UI, eliminadas columnas Instituto/Empresa
- app-bridge.js: añadidos getInstrumentos/saveInstrumentos
- scripts/prepare-tauri-web.js: copia HTML/JS/CSS y `vendor/xlsx.full.min.js` a `tauri-web/`

**How to apply:** La estructura del Excel ESO es diferente al FP. Siempre usar rangos fijos (A4:B41, I5:K20, N4:O13). Antes de tocar guardados Excel, mantener escritura segura con temporal, validacion y `.autobak`.
