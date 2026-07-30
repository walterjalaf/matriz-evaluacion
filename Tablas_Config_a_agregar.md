# Tablas de conversión — lo que hay que agregar y corregir en la planilla

Relevado sobre la Matriz `1Vndtewzfb9DnXXyW7c2rKPNkU45kh3x_WxF0HiRy51E` y validado contra las
7 respuestas reales de la hoja de intake `1SOW-lAOopw7hddfG4R_Ii8SC42BLQvafqtBDeput5g4`.

---

## 1. La regla general que ya existe en la planilla

Los subcriterios 4.1, 4.2, 4.3, 4.4 y 5.2 **nunca se calcularon por proporción de checkboxes**.
La hoja `Matriz post verificación` (celdas E69-E72 y E75) usa:

    MAX(1; ROUND( promedio_de_niveles / nivel_máximo * 5 ; 0 ))

`LEFT(respuesta;1)` extrae el nivel, porque las opciones del Intake están prefijadas `0=`, `1=`, `2=`, `3=`.

**El mínimo es 1, nunca 0 — pero solo si la empresa respondió algo.** Si no declaró ninguna
pregunta del subcriterio, no se aplica la tabla y el subcriterio da **0**, que dispara el piso de No-Go.
Verificado: una respuesta enteramente vacía da 0 en los 26 subcriterios y global 0,0.

### T7 · % del máximo → puntaje

Reproduce esa fórmula **exactamente** (comprobado en 5.002 casos sintéticos y en las 42
combinaciones reales), y queda en el mismo formato "Desde → Pts" que T1-T6.

| Desde (%) | Pts |
|---|---|
| 0 | 1 |
| 30 | 2 |
| 50 | 3 |
| 70 | 4 |
| 90 | 5 |

---

## 2. Tablas nuevas para agregar a `Config` — todas 0-5

### T8 · Horas de uso del equipo
Menos horas es mejor. Reparto 5/3/1, el mismo que ya usan T4, T5 y T6 para tres bandas.

| Respuesta | Pts |
|---|---|
| 0 - 1.000 hs | 5 |
| 1.000 - 3.000 hs | 3 |
| más de 3.000 hs | 1 |

### T9 · Año-modelo del equipo
Un nivel por año. `otro` puntúa 1 porque es una respuesta válida, no una omisión.
Un año posterior a 2026 puntúa 5, para no tener que retocar la tabla el año que viene.

| Respuesta | Pts |
|---|---|
| 2026 | 5 |
| 2025 | 4 |
| 2024 | 3 |
| 2023 | 2 |
| 2022 | 1 |
| otro | 1 |

### T10 · Modalidad de mantenimiento (Q5.43)
La escala ya estaba definida en `Intake!G159` (`0=Propio S/R | 1=Propio C/R | 2=Tercerizado C/R`)
pero **ninguna fórmula la usaba**. Acá se reescala a 0-5 respetando ese orden.

| Respuesta | Pts |
|---|---|
| Tercerizado con registro | 5 |
| Propio con registro | 3 |
| Propio sin registro | 1 |

### T11 · Presupuestos de proveedores en zona (Q5.44 a Q5.47)

| Respuesta | Pts |
|---|---|
| 3 o más | 5 |
| 2 | 4 |
| 1 | 3 |
| Sin presupuesto | 0 |

---

## 3. Escalas 0-3 de la Dimensión IV — ya documentadas, no se tocan

Están en `Intake`, columna G, filas 101-113. T7 las normaliza por `nivel / máximo`.

| Ítem | 0 | 1 | 2 | 3 |
|---|---|---|---|---|
| Q4.1 | Sin experiencia | Entre 1 y 3 años | Entre 4 y 7 años | Más de 7 años |
| Q4.2 | Sin proyectos | 1 proyecto | Entre 2 y 4 proyectos | 5 proyectos o más |
| Q4.3 | 0 km | Menos de 20 km | Entre 20 y 100 km | Más de 100 km |
| Q4.4 | Sin experiencia | Experiencia parcial | Sí, con experiencia | Sí, con protocolo documentado |
| Q4.5 | Sin experiencia | Menos de 2 años | Entre 2 y 5 años | Más de 5 años |
| Q4.6 | Menos de 2.000 msnm | Entre 2.000 y 3.000 | Entre 3.000 y 4.000 | Más de 4.000 msnm |
| Q4.7 | Ninguno | 1 contrato | Entre 2 y 3 contratos | 4 contratos o más |
| Q4.8 | Sin experiencia | Experiencia esporádica | Experiencia recurrente | Con contratos formales |
| Q4.9 | No | Ocasionalmente | Sí | Sí, con guardias 24/7 |
| Q4.10 | Sin operadores | Menos de 2 años | Entre 2 y 5 años | Más de 5 años |
| Q4.11 | Ninguno | Entre 1 y 2 | Entre 3 y 5 | 6 o más |
| Q4.12 | Sin definir | Menos de 3 años | Entre 3 y 7 años | Más de 7 años |
| Q4.13 | No cuenta | Informal | Sí, cuenta | Sí, con registros de dictado |

---

## 4. Errores de la planilla que conviene corregir

### 4.1 · T3 está huérfana y, si se conecta, rompe el cálculo

`Config` dice `T3 · Años de experiencia vial → sugiere 4.1`, pero **ninguna fórmula la referencia**:
4.1 se calcula promediando Q4.1 a Q4.4 (fila 69 de `Matriz post verificación`).

Si se conectara T3, el subcriterio 4.1 pasaría a depender únicamente de Q4.1 e **ignoraría**
la estabilización de suelos (Q4.2), los kilómetros mantenidos (Q4.3) y el perfilado (Q4.4).
Conviene borrarla de `Config` o marcarla como no usada.

### 4.2 · Falta una tabla que las fórmulas ya invocan

Las filas 82 y 83 (subcriterios 6.4 y 6.5) hacen `VLOOKUP` contra `Conversiones!B46:C50` —
cinco tramos que `Config` no tiene. Por la reconstrucción del Marco Metodológico serían:

| Desde (%) | Pts |
|---|---|
| 0 | 1 |
| 25 | 2 |
| 50 | 3 |
| 75 | 4 |
| 100 | 5 |

### 4.3 · Las fórmulas apuntan a una hoja que no existe

Todos los `VLOOKUP` referencian una hoja **`Conversiones`**, pero la hoja se llama **`Config`**.
Hoy devuelven `#REF` en la planilla real.

### 4.4 · Mapeo de rangos → tabla, para reconectar los VLOOKUP

| Subcriterio | Fila | Rango citado | Tabla | ¿Se usa? |
|---|---|---|---|---|
| 2.2 | 61 | B40:C42 | T6 | sí |
| 3.1 | 64 | B6:C10 | T1 | sí |
| 3.4 | 67 | B28:C30 | T4 | sí |
| 4.1 | 69 | — | promedio de ítems | **T3 no se usa** |
| 5.1 | 74 | B14:C17 | T2 | sí |
| 6.4 y 6.5 | 82, 83 | B46:C50 | **no existe** | referencia rota |
| 7.1 | 85 | B34:C36 | T5 | sí |

---

## 5. Subcriterios sin fórmula en la planilla

Son de carga manual del evaluador: 1.1, 1.2, 1.3, 2.1, 2.3, 3.2, 3.3, 5.3, 5.4, 6.1, 6.2, 6.3, 7.2, 7.3, 7.4.

Las páginas HTML los aproximan por proporción de respuestas. **5.3 entra en esta lista**: T11 es un
agregado para que los presupuestos en zona puntúen de forma graduada, no una regla de la fuente.
