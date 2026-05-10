# Gestor de Notas ESO - Guía del Codebase

## Estilo de respuesta (obligatorio)
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
- Haz commint y push


**Proyecto**: Gestor de Notas ESO | **Versión**: 0.1.0 | **Stack**: Tauri v2 + Rust + HTML/CSS/Vanilla JS | **Estado**: Funcional

## Estructura

```
ESOplantillaNotas/
├── HTML: index.html, gestor-alumnos.html, gestor-rraa-criterios.html,
│         gestor-unidades.html, gestor-instrumentos.html, gestor-notas.html,
│         visor-notas.html, visor-unidades.html, informes.html, diario.html
├── Backend: app-bridge.js (puente Tauri), main.js, preload.js
├── Scripts: scripts/prepare-tauri-web.js
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
npm run tauri:build                                        # Build EXE
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

## Stack Rust

**Crates:**
- `calamine 0.26` (features: dates) — leer hojas XLSX
- `zip 2` (features: deflate) — reescribir ZIP interno del XLSX
- `regex 1` — manipulación XML
- `once_cell 1` + `chrono 0.4` — estado global y fechas

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
- `excel_get_notas_unidad`
- `excel_get_alumnos_informes`
- `excel_get_diario`, `excel_save_diario_entrada`, `excel_delete_diario_entrada`
- `app_open_external`

## Páginas HTML

| Archivo | Función |
|---------|---------|
| index.html | Inicio — menú principal + modal importar CSV |
| gestor-alumnos.html | Gestión de alumnos |
| gestor-rraa-criterios.html | Gestión de CE y criterios (ESO: sin RA) |
| gestor-unidades.html | Gestión de unidades (sin columna Horas) |
| gestor-instrumentos.html | Instrumentos de evaluación (max 10) |
| gestor-notas.html | Introducir notas actividades + CE por alumno |
| visor-notas.html | CE y evaluaciones |
| visor-unidades.html | Notas por unidad |
| informes.html | Informes finales |
| diario.html | Diario de clase |

## Contexto adicional

Ver carpeta `memory/` para estado detallado, decisiones de diseño y preferencias de trabajo.

**Responsable**: Sebantonio | **Creado**: 2026-05-10 | **Actualizado**: 2026-05-10
