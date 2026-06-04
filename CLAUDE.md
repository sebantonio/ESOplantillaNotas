# Gestor de Notas ESO - Guía del Codebase

## Estilo de respuesta (obligatorio)
- Contesta como  un cavernicola, respuestas cortas
- Actúa como senior engineer.
- Haz cambios mínimos.
- No refactorices salvo que se pida.
- No expliques código.
Devuelve solo:
- archivos modificados
- diff
- comandos necesarios
- Respuestas cortas y técnicas. Sin teoría, sin contexto repetido.
- Solo cambios mínimos. Diffs antes que archivos completos.
- No reescribir código intacto. No comentarios innecesarios.
- Bullets de 1 línea. Sin introducciones ni conclusiones.
- Solo código cuando sea suficiente. Pregunta en 1 frase si falta contexto.
- Responde corto y técnico.
- No expliques teoría salvo que se pida.
- No repitas contexto.
- Da solo cambios mínimos necesarios.
- Usa diffs/parches antes que archivos completos.
- No reescribas código intacto.
- Resume en bullets de máximo 1 línea.
- Si falta contexto, pregunta en 1 frase.
- Prioriza rendimiento y ahorro de tokens.
- Evita introducciones, conclusiones y relleno.
- Devuelve solo código cuando sea suficiente.
- Mantén nombres y estructura existentes.
- No generes comentarios innecesarios.
- Analiza primero y modifica después.
- Haz commit y push


**Proyecto**: Gestor de Notas ESO | **Versión**: 0.1.81 | **Stack**: Tauri v2 + Rust + HTML/CSS/Vanilla JS | **Estado**: Funcional

## Estructura

```
ESOplantillaNotas/
├── HTML: index.html, gestor-alumnos.html, gestor-rraa-criterios.html,
│         gestor-unidades.html, gestor-instrumentos.html, gestor-notas.html,
│         gestor-recuperaciones.html, visor-notas.html, visor-unidades.html,
│         informes.html, diario.html
├── Backend: app-bridge.js (puente Tauri), main.js, preload.js
├── Scripts: scripts/prepare-tauri-web.js, scripts/bump-version.js
├── src-tauri/: main.rs (lógica Rust), Cargo.toml, tauri.conf.json
├── memory/: contexto del proyecto para Claude Code
└── Excel: CCGG PLANTILLA - RECUv45.xlsx
```

## Stack

- **Frontend**: HTML5 + CSS + Vanilla JS (sin framework)
- **Desktop**: Tauri v2 (Rust backend puro)
- **Excel**: calamine (leer) + zip + XML directo (escribir)
- **Persistencia**: JSON local

## Comandos

```powershell
node scripts/prepare-tauri-web.js && npm run tauri:dev    # Dev Tauri
npm run tauri:build                                        # Build EXE (bump version automático)
```

## Archivo Excel

Archivo principal: `CCGG PLANTILLA - RECUv45.xlsx` — hoja **DATOS**

**Rangos fijos (NO buscar por contenido de celda):**
| Tabla | Rango Excel | 0-indexed |
|-------|-------------|-----------|
| Alumnos | A4:B41 | fila 4=header, datos filas 5-41 (0-idx:4-40), max 37 |
| Unidades | I5:K20 | filas 4-19, cols 8(I) 9(J) 10(K) |
| Instrumentos | N4:O13 | filas 3-12, cols 13(N) 14(O) |

- Unidades: I=código, J=nombre, K=evaluación (1ª/2ª/3ª)
- Instrumentos: N=abreviatura, O=nombre (max 10)
- Hoja **PESOS**: CE y criterios (CR1.1, CR2.3...) con ponderaciones por unidad
  - Fila idx 3 = mapa CR→colIdx; filas 4-19 = valores por unidad (col A = nombre unidad)
  - Valores son % directos (20 = 20%) — NO multiplicar por 100

## Hojas de evaluación (1ª EVA, 2ª EVA, 3ª EVA, FINAL, 2ª EVA-solo, 3ª EVA-solo)

- **Fila 17 (0-idx 16)**: cabecera — NOTA CE | CR1.1 | Rec | CR1.2 | Rec | ... | NOTA FINAL
- **Fila 18 (0-idx 17)**: sub-etiquetas "Rec"
- **Fila 19+ (0-idx 18+)**: datos de alumnos
- **Columna CB (0-idx 79)**: NOTA FINAL — leer via `read_col_values_from_xml` (calamine no alcanza)
- **Columna Rec**: adyacente al CR (ci+1), guardada via `excel_save_eval_rec`
- La detección de layout usa 3 estrategias (ESO: misma fila NOTA CE + CR codes)

## Stack Rust

**Crates:**
- `calamine 0.26` (features: dates) — leer hojas XLSX
- `zip 2` (features: deflate) — reescribir ZIP interno del XLSX
- `regex 1` — manipulación XML
- `once_cell 1` + `chrono 0.4` — estado global y fechas

**Funciones clave:**
- `read_col_values_from_xml(path, sheet_name, col)`: lee valores de una columna directamente del ZIP/XML — bypass al límite de rango de calamine
- `find_evaluation_sheet_name(names, evaluacion)`: busca hoja por nombre (1ª EVA, 2ª EVA...)
- `load_notas_evaluacion(path, evaluacion)`: carga tabla de evaluación con raColumns, criteria, alumnos

## IPC Handlers (app-bridge.js → Rust)

- `excel_select_file`, `excel_set_selected_file`, `excel_get_selected_file`, `excel_verify_file_exists`
- `excel_get_alumnos`, `excel_save_alumnos`
- `excel_get_unidades`, `excel_save_unidades`
- `excel_get_instrumentos`, `excel_save_instrumentos`
- `excel_get_rraa_criterios`, `excel_save_rraa_criterios`
- `excel_get_notas_actividad`, `excel_save_notas_actividad`
- `excel_save_ce_notas`, `excel_add_actividad`
- `excel_get_notas_actividades_tipo`
- `excel_get_notas_evaluacion`, `excel_get_notas_evaluacion_alumno`
- `excel_get_notas_unidad`, `excel_save_notas_unidad`
- `excel_save_eval_rec` — guarda nota de recuperación en celda de hoja de evaluación
- `excel_save_eval_recs_batch` — guarda TODAS las Rec de una evaluación en una sola escritura ZIP
- `excel_get_alumnos_informes`
- `excel_get_diario`, `excel_save_diario_entrada`, `excel_delete_diario_entrada`
- `app_open_external`

## Páginas HTML

| Archivo | Función |
|---------|---------|
| index.html | Inicio — menú principal; botón "Introducir notas" abre modal con 2 opciones |
| gestor-alumnos.html | Gestión de alumnos |
| gestor-rraa-criterios.html | Gestión de CE y criterios (ESO: sin RA) |
| gestor-unidades.html | Gestión de unidades (sin columna Horas) |
| gestor-instrumentos.html | Instrumentos de evaluación (max 10) |
| gestor-notas.html | Introducir notas: paginación 15/pág (top+bottom), agrupación CE con colores, columna alumno sticky |
| gestor-recuperaciones.html | Introducir recuperaciones: Rec editable, batch save, autosave silencioso, CE/Final se recalculan en JS |
| visor-notas.html | Ver notas por evaluación — SOLO LECTURA, columna alumno sticky |
| visor-unidades.html | Ver notas por unidad — solo lectura, columna alumno sticky |
| informes.html | Informes finales por alumno |
| diario.html | Diario de clase |

## Notas críticas de implementación

- **Modales en Tauri**: usar `style.display='flex'/'none'` directamente; `classList.add('open')` no sobreescribe inline style
- **Excel path**: `SELECTED_PATH` es static Rust — persiste en sesión pero se pierde al reiniciar si Excel no está en dir del exe. visor-notas/gestor-recuperaciones auto-seleccionan desde `localStorage.recentExcelFiles`
- **Hojas de unidad (U1, U2...)**: celdas de nombre son fórmulas (=DATOS!B5) que calamine NO evalúa → usar siempre `load_alumnos()` para nombres
- **CR scan en load_notas_unidad**: empezar desde col 0 (CR1.1-1.4 están en cols 0-3)
- **Nota Final (col CB)**: calamine puede no alcanzar col 79 si el rango detectado es corto → usar `read_col_values_from_xml` que lee el ZIP/XML directamente
- **cell_f64 retorna `Option<f64>`** — siempre hacer `.unwrap_or(0.0)`
- **prepare-tauri-web.js**: reemplaza versión vX.X.X en todos los HTML al copiar a tauri-web/
- **Sticky columnas**: usar `overflow: clip` (NO `overflow: hidden`) en `.container` — hidden crea scroll container implícito que anula position:sticky
- **CE/Final en recuperaciones**: calamine lee caché de fórmulas (stale) — los valores se recalculan en JS con `recomputeAlumno()` usando `criteria[].peso` y `raColumns[].peso` del estado cargado
- **Batch save recuperaciones**: `saveAllRec` usa `excel_save_eval_recs_batch` (1 escritura ZIP para todas las celdas); autosave es silencioso (no reconstruye DOM). `saveRecFromInput` guarda celda + actualiza DOM sin rebuild.
- **Paginación gestor-notas**: `currentPage`/`perPage` globales; `renderTable()` usa `currentNotes.slice(startIdx, startIdx+perPage)`; `data-studentIdx` es índice global (no local de página)

## Pendientes

*(ninguno)*

## Contexto adicional

Ver carpeta `memory/` para estado detallado, decisiones de diseño y preferencias de trabajo.

**Responsable**: Sebantonio | **Creado**: 2026-05-10 | **Actualizado**: 2026-05-11 (sesión 4)
