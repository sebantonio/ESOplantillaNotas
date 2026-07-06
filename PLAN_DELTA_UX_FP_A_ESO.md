# Plan de modificaciones pendientes (delta FP -> ESO)

Fecha: 2026-07-06

Objetivo:
- Aplicar en el proyecto ESO los refinamientos UX/flujo que ya funcionan en FP.
- Ejecutarlo por tandas cortas, con validacion y build en cada cierre.

---

## 1) Resumen de lo que falta en ESO (respecto a FP)

1. Gestor de notas: pulido de flujo de correccion
- Deshacer ultimo vaciado de notas.
- Atajos extra para navegar y abrir CE del alumno activo.
- Microcopy de ayuda docente actualizado con atajos.

2. Visores analiticos: filtros y ordenaciones
- Ordenacion configurable en visores con listados largos.
- Mejor comportamiento de teclado en busquedas/filtros (Escape/Enter).

3. Consistencia UX
- Estados de guardado y confirmaciones coherentes.
- Cierre de detalles pequenos de foco/navegacion.

Nota:
- En ESO no aplica el bloque de empresa dual de FP.
- Las mejoras deben adaptarse a su dominio (instrumentos, recuperaciones, evaluaciones).

---

## 2) Tandas recomendadas (orden de ejecucion)

## Tanda A - Gestor de notas (alta prioridad)

Archivo principal:
- gestor-notas.html

Cambios:
1. Boton "Deshacer vaciado"
- Guardar snapshot en memoria antes de vaciar.
- Restaurar snapshot al pulsar deshacer.
- Deshabilitar boton cuando no haya snapshot.

2. Atajo para abrir CE del alumno activo
- Registrar indice del ultimo input de nota enfocado.
- Atajo sugerido: Alt+C para abrir/cerrar CE de ese alumno.

3. Mensajeria de ayuda
- Actualizar texto del panel docente con atajos disponibles.

4. Validacion de no regresion
- Mantener pegado masivo, filtros rapidos y guardado actual.

Criterios de aceptacion:
- Vaciar y deshacer restaura notas correctamente.
- Alt+C funciona sin romper navegacion existente.
- No aparecen errores JS en la pagina.

Commit sugerido:
- feat(ux): gestor-notas undo clear and CE quick toggle

---

## Tanda B - Visor de notas (media prioridad)

Archivo principal:
- visor-notas.html

Cambios:
1. Si no existe, anadir selector de orden para listado de alumnos
- Nombre A-Z / Z-A
- Nota final asc/desc (o indicador equivalente de la vista)

2. Busqueda y teclado
- Escape limpia busqueda.
- Enter aplica refresco de filtros/orden.

3. Mantener lectura pura
- No introducir escrituras ni side effects.

Criterios de aceptacion:
- Orden y filtro se aplican sobre el mismo conjunto visible.
- Escape limpia y rerenderiza.
- Sin errores JS.

Commit sugerido:
- feat(ux): visor-notas sorting and keyboard polish

---

## Tanda C - Visor por unidad y/o actividades (media prioridad)

Archivos candidatos:
- visor-unidades.html
- visor-actividades.html

Cambios:
1. Revisar necesidad de ordenaciones en tablas largas
- Orden por alumno.
- Orden por nota media o estado si aplica.

2. Teclado y busqueda
- Escape para limpiar.
- Enter para aplicar filtros.

Criterios de aceptacion:
- Mejora perceptible en exploracion de listados.
- Sin romper despliegues RA/CE existentes.

Commit sugerido:
- feat(ux): viewer keyboard and sorting improvements

---

## Tanda D - Pulido transversal (baja/media prioridad)

Archivos candidatos:
- ux-common.js
- ux-common.css
- index.html
- paginas de gestion donde falte consistencia

Cambios:
1. Unificar microcopy y estados
- Mensajes de guardado/pendiente/error consistentes.

2. Revisar confirmaciones sensibles
- Borrados, vaciados y acciones destructivas.

3. Reforzar foco visible y navegacion
- Especialmente en modales y tablas de datos.

Criterios de aceptacion:
- Experiencia homogena entre modulos.
- Sin cambios de dominio ni de estructura de Excel.

Commit sugerido:
- feat(ux): cross-page consistency polish

---

## 3) Protocolo de trabajo (recomendado)

Por cada tanda:
1. Snapshot previo
- git commit --allow-empty -m "chore: snapshot before <tanda>"
- git push

2. Implementacion
- Cambios minimos por archivo
- Validacion de errores de editor

3. Cierre de tanda
- git add <archivos>
- git commit -m "feat(ux): ..."
- git push

4. Build
- cerrar exe en uso si hace falta
- npm run tauri:build

---

## 4) Checklist rapido de validacion por tanda

Checklist tecnico:
- Sin errores JS/HTML en archivos tocados.
- Sin rotura de carga de datos desde Excel.
- Sin regresion en guardado/autoguardado.

Checklist funcional:
- Flujo docente principal tarda menos o igual.
- Atajos no pisan atajos existentes.
- Confirmaciones aparecen donde toca.

Checklist de build:
- Build Tauri completa.
- Instalador generado correctamente.

---

## 5) Riesgos a evitar

1. No tocar mapeos de celdas/rangos Excel en esta fase UX.
2. No mezclar mejoras UX con refactor profundo.
3. No introducir dependencias nuevas innecesarias.
4. Mantener commits pequenos por pantalla/tanda.

---

## 6) Siguiente accion sugerida

Empezar por Tanda A (gestor-notas.html), porque da el mayor impacto docente con riesgo bajo y se puede validar rapido.
