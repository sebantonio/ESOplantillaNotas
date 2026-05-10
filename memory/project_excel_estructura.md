---
name: Estructura del Excel ESO
description: Rangos exactos de cada tabla en CCGG PLANTILLA - RECUv45.xlsx
type: project
---
Archivo: `CCGG PLANTILLA - RECUv45.xlsx`

**Hoja DATOS — rangos fijos:**
| Tabla | Rango Excel | 0-indexed (filas, cols) |
|-------|-------------|--------------------------|
| Alumnos | A4:B30 | filas 3-29, cols 0(A) 1(B) |
| Unidades | I5:K20 | filas 4-19, cols 8(I) 9(J) 10(K) |
| Instrumentos evaluación | N4:O13 | filas 3-12, cols 13(N) 14(O) |

- Col I = código unidad (U1, U2...)
- Col J = nombre unidad
- Col K = evaluación (1ª / 2ª / 3ª)
- Col N = abreviatura instrumento (PE, TD...)
- Col O = nombre instrumento

**Hoja PESOS:** Criterios de evaluación (CE) y ponderaciones. Códigos tipo CR1.1, CR2.3. Los CE van en fila 4 (0-indexed: 3), los CR en columnas variables.

**Why:** El Excel ESO tiene una estructura diferente al Excel FP (plantilla313_dual). Los rangos son fijos y no hay que buscarlos por contenido.

**How to apply:** Siempre usar rangos fijos. No buscar headers ni "UNIDADES" por texto. Si el usuario añade columnas nuevas en el Excel, revisar estos rangos.
