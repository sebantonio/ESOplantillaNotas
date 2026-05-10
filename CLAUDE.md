# Gestor de Notas ESO - Guía del Codebase

**Proyecto**: Gestor de Notas ESO | **Versión**: 0.1.0 | **Stack**: Tauri v2 + Rust + HTML/CSS/Vanilla JS | **Estado**: Desarrollo inicial

## Estructura

```
ESOplantillaNotas/
├── HTML: index.html, gestor-alumnos.html, gestor-rraa-criterios.html,
│         gestor-unidades.html, gestor-notas.html, visor-notas.html,
│         visor-actividades.html, visor-unidades.html, informes.html,
│         incluir-actividad.html, diario.html
├── Backend: app-bridge.js (puente Tauri), main.js, preload.js, tauri-node-backend.js
├── Scripts: scripts/prepare-tauri-web.js
├── src-tauri/: main.rs (lógica Rust), Cargo.toml, tauri.conf.json
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

Archivo principal: `CCGG PLANTILLA - RECUv45.xlsx`

> IMPORTANTE: La estructura de hojas de este Excel (ESO) puede diferir de plantillaNotas (FP).
> Hay que analizar el Excel antes de adaptar los comandos Rust en main.rs.

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
| index.html | Inicio — menú principal |
| gestor-notas.html | Introducir notas actividades + CE por alumno |
| gestor-alumnos.html | Gestión de alumnos |
| gestor-rraa-criterios.html | Gestión de RA y criterios |
| gestor-unidades.html | Gestión de unidades |
| visor-notas.html | RRAA y CCEE (evaluaciones) |
| visor-actividades.html | Ver notas por actividad + panel RA |
| visor-unidades.html | Ver notas por unidad + desplegable RA/CE |
| informes.html | Informes finales |
| diario.html | Diario de clase |

## Próximos Pasos

1. Analizar estructura del Excel `CCGG PLANTILLA - RECUv45.xlsx`
2. Adaptar `src-tauri/src/main.rs` a la estructura ESO
3. `npm install`
4. `npm run tauri:build`

**Responsable**: Sebantonio | **Creado**: 2026-05-10
