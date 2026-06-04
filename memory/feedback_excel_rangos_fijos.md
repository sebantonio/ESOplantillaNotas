---
name: Usar rangos fijos para el Excel ESO
description: No buscar headers — los rangos de tablas son fijos y conocidos
type: feedback
---
Usar siempre rangos fijos para leer/escribir el Excel ESO donde la plantilla los define. No buscar "UNIDADES" por texto para la tabla principal de unidades.

**Why:** El usuario lo indicó explícitamente: "SI eres capaz de leer los instrumentos, los alumnos... Solo tienes que leer la tabla que hay en I5:K20". La búsqueda de headers causó que solo se mostrara la primera unidad durante varias iteraciones.

**How to apply:** En main.rs, unidades son `I5:K20` y instrumentos `N4:O13`. Alumnos usan el bloque `A4:B41` con header "Alumnado" y maximo 37. En gestor-unidades.html (modo browser con XLSX.js), leer `rows[4..19]` cols 8/9/10 directamente.
