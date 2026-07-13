# Cómo se calcula la Matriz Integral de Evaluación

Referencia técnica del simulador `ejemplo.html` — versión actualizada a partir de las dos planillas de Google Sheets del proceso real (`Matriz_Veladero_Datos_Prueba_4_Empresas` y `Matriz de Evaluación de Proveedores`) y del `Marco_Metodologico_Matriz_v4_1.docx`.

## 1. Resumen ejecutivo

`ejemplo.html` es un simulador interactivo de la Matriz Integral de Evaluación de proveedores para el servicio de mantenimiento del camino de acceso a Mina Veladero (MAS SRL). Permite marcar evidencia ítem por ítem y ver, en vivo, cómo se llega al puntaje global y al dictamen (Go / Go con Plan de Acción / No-Go).

**Qué SÍ hace con precisión:** reproduce la estructura real — 7 dimensiones, 27 subcriterios, sus pesos exactos, el Gate HSE de 33 ítems en dos niveles, y el orden exacto de la cascada de decisión (verificado contra la fórmula viva de la planilla real, no contra una descripción). Además aplica una regla de piso universal: **cualquiera de los 27 subcriterios en 0/5 puntos produce No-Go**, no solo los 6 destacados históricamente (ver Sección 5).

**Qué NO hace:** el puntaje 0-5 de cada subcriterio se calcula como una simple proporción de checkboxes marcados (`verificados / total × 5`), mientras que la matriz real usa, para 6 de los 27 subcriterios, tablas de conversión propias (T1-T8) a partir de un dato numérico declarado (situación BCRA, cantidad de empleados, horas de cobertura, etc.). Esas tablas se documentan en la Sección 8, pero **no están activas en el motor de cálculo** (con la excepción parcial de 5.1, ver Sección 7). Esto ya estaba aclarado en el pie de página del simulador y sigue siendo así en esta versión.

## 2. Estructura general

7 dimensiones, 27 subcriterios, 100 puntos:

| Dim | Nombre | Peso | Subcriterios (peso) |
|---|---|---|---|
| I | Gobernanza, Cumplimiento e Integridad | 10 | 1.1 (4) · 1.2 (3) · 1.3 (3) |
| II | Salud Financiera | 15 | 2.1 (5) · 2.2 (6) · 2.3 (4) |
| III | RRHH y Régimen Laboral | 15 | 3.1 (4) · 3.2 (4) · 3.3 (3) · 3.4 (4) |
| IV | Experiencia Técnica | 18 | 4.1 (7) · 4.2 (5) · 4.3 (2) · 4.4 (4) |
| V | Flota, Capacidad Mecánica y Repuestos | 20 | 5.1 (8) · 5.2 (5) · 5.3 (5) · 5.4 (2) |
| VI | SSMA Operacional | 14 | 6.1 (3) · 6.2 (3) · 6.3 (3) · 6.4 (3) · 6.5 (2) |
| VII | Desarrollo Comunitario y Asociativismo | 8 | 7.1 (3) · 7.2 (1) · 7.3 (2) · 7.4 (2) |

El peso de cada dimensión es siempre la suma de los pesos de sus subcriterios (invariante que usa el motor de cálculo, ver Sección 3).

## 3. Cómo se calcula el puntaje, paso a paso

**Paso 1 — puntaje de cada subcriterio (0 a 5):**
```
puntaje_sub = round((preguntas_verificadas / preguntas_totales) × 5)
```
Solo cuentan las preguntas marcadas `scoreable` (por defecto todas lo son; dos preguntas "paraguas" de la Sección V — "¿posee equipos para operativo estival/invernal?" — están marcadas `scoreable:false` porque son declarativas, no evidencia puntuable).

**Paso 2 — ponderado de cada subcriterio:**
```
ponderado_sub = (puntaje_sub / 5) × peso_sub
```
Este es el punto donde una versión anterior de este simulador tenía un bug: calculaba el promedio simple de los subcriterios de una dimensión y recién ahí aplicaba el peso de la dimensión completa. Eso es matemáticamente distinto a ponderar cada subcriterio por su propio peso — y la fórmula real de la planilla (`Matriz E1 (GO)`, celda G56: `=(F56/5)*D56`) confirma que el cálculo correcto es por subcriterio, no por promedio de dimensión.

*Ejemplo numérico del bug corregido* — Dimensión I con puntajes 5, 4, 4 (pesos 4, 3, 3):
- Fórmula anterior (incorrecta): promedio = (5+4+4)/3 = 4.333 → (4.333/5)×10 = **8.667**
- Fórmula actual (correcta, igual a la planilla real): (5/5×4) + (4/5×3) + (4/5×3) = 4 + 2.4 + 2.4 = **8.8**

**Paso 3 — obtenido de cada dimensión:**
```
obtenido_dim = Σ ponderado_sub  (de los subcriterios de esa dimensión)
```

**Paso 4 — puntaje global:**
```
puntaje_global = Σ obtenido_dim  (de las 7 dimensiones)
                = Σ ponderado_sub (de los 27 subcriterios, directamente)
```
Ambas formas dan el mismo número — es la misma suma, solo que agrupada o no por dimensión.

**Para mostrar el "score 0-5" de una dimensión** (paneles de la derecha), se despeja de manera consistente con el global: `(obtenido_dim / peso_dim) × 5` — equivalente al % de logro de esa dimensión multiplicado por 5. Ya no es un promedio simple de sus subcriterios.

## 4. Gate HSE

33 ítems en dos niveles, evaluados en paralelo a los 27 subcriterios:

- **Nivel 1 — excluyente (24 ítems, G.1 a G.28)**: un solo "No" verificado en cualquiera de ellos produce No-Go inmediato, sin promediar con nada más. Alimentan los subcriterios 6.1 (5 ítems), 6.2 (16 ítems) y 6.3 (3 ítems).
- **Nivel 2 — brecha (9 ítems)**: un "No" no excluye, pero queda registrado como discrepancia/brecha y limita el puntaje de 6.4 (3 ítems) y 6.5 (6 ítems).

Todos los ítems del simulador (Gate y no-Gate) tienen dos estados — **declarado** (Intake) y **verificado** (visita presencial) — y una discrepancia entre ambos se marca automáticamente.

## 5. Pisos

**Regla general (vigente desde esta versión): cualquiera de los 27 subcriterios que llegue a 0/5 puntos produce No-Go**, sin importar cuán alto sea el puntaje global. No es una lista cerrada de excepciones — es un chequeo que recorre los 27 subcriterios de las 7 dimensiones (`subcriteriosEnCero()` en el motor) y detiene la cascada si encuentra al menos uno en 0.

Antes de este cambio, solo 6 subcriterios estaban marcados explícitamente como piso "0 = No-Go" en `DATA` (se mantienen destacados por ser los históricamente más frecuentes / los que trae la planilla de referencia con ese atributo):

| Subcriterio | Regla | Motivo |
|---|---|---|
| 2.1 · Solvencia, liquidez y capital de trabajo | Puntaje = 0 | Sin estados contables ni información contable verificable |
| 1.3 · Integridad y libre conflictividad | Puntaje = 0 | Sin certificados de conflictividad/deuda sindical en regla |
| 2.2 · Endeudamiento / financiamiento de flota | Puntaje = 0 | Sin capacidad demostrable de financiar la flota exigida |
| 3.4 · Roster 14x7 y reemplazo 24 h | Puntaje = 0 | Sin esquema de turnos ni back-up ante ausencias |
| 4.2 · Industria minera y alta montaña | Puntaje = 0 | Sin experiencia previa en minería o alta montaña |
| 5.1 · Flota disponible vs. mínima | Puntaje = 0 | No alcanza la flota mínima exigida por el pliego |

Pero con la regla general, los otros 21 subcriterios (1.1, 1.2, 2.3, 3.1, 3.2, 3.3, 4.1, 4.3, 4.4, 5.2, 5.3, 5.4, 6.1-6.5, 7.1-7.4) también producen No-Go si su puntaje cae a 0 — algo que antes NO ocurría (antes solo diluían el puntaje global, no cortaban la cascada).

Más dos pisos "de bloque" (no de un solo subcriterio):
- **Salud Financiera** (promedio de 2.1, 2.2, 2.3) < 2 → No-Go.
- **SSMA excluyente** (mínimo de 6.1, 6.2, 6.3) ≤ 1 → No-Go.

Y el **Gate Nivel 1 completo** (24 ítems): un solo "No" verificado en cualquiera → No-Go inmediato.

> **Nota sobre 2.1**: la planilla nueva le agrega el piso "0 = No-Go" a 2.1, algo que no tenía en la versión anterior de este simulador. Se adoptó tal cual. El texto exacto de la celda de la planilla ("No-Go si es puntuación entre 3 y 5" para 2.2) parece tener una redacción invertida/errónea en la fuente — se mantuvo el criterio ya validado de "0 = No-Go" para 2.2, que es consistente con el resto de los pisos y con el glosario del Marco Metodológico.

## 6. Cascada de decisión

Orden real (verificado contra la fórmula de la celda `D95` de `Matriz E1 (GO)`, no contra la Sección 7 del Word — ver nota al final):

1. Consentimiento de auditoría (declarado = No → No-Go)
2. Elegibilidad geográfica (fuera de Iglesia/Jáchal → No-Go)
3. Gate Nivel 1 (24 ítems — un "No" → No-Go)
4. Salud Financiera (promedio 2.1-2.3 < 2 → No-Go)
5. SSMA excluyente (mínimo de 6.1/6.2/6.3 ≤ 1 → No-Go)
6. **Cualquiera de los 27 subcriterios en 0 puntos → No-Go** (paso único que reemplaza los 6 chequeos individuales de la versión anterior — ver sección 5)
7. Puntaje global < 55 → No-Go
8. Puntaje global 55 a 69,9 → Go con Plan de Acción
9. Puntaje global ≥ 70 → Go

El proceso se detiene en la primera condición que aplique — una empresa puede tener puntaje global alto y aun así recibir un No-Go si incumple una condición anterior en el orden.

> **Cambio de orden respecto de una versión anterior**: en una revisión previa de este simulador, el orden se había puesto como "Gate → Auditoría → Elegibilidad", siguiendo la tabla descriptiva de la Sección 7 del Marco Metodológico. Al leer la fórmula real de la planilla, se confirmó que el orden verdadero es "Auditoría → Elegibilidad → Gate", así que se revirtió. Para los 4 casos de prueba precargados esto no cambia el dictamen final (ninguno falla simultáneamente en más de una de estas tres condiciones), pero sí determina cuál motivo se muestra como "el que frena" cuando hay más de un problema.

> **Cambio de regla (esta versión)**: el paso 6 antes verificaba individualmente 6 subcriterios (5.1, 4.2, 3.4, 2.2, 2.1, 1.3). Ahora es un único chequeo genérico (`subcriteriosEnCero()`) que recorre los 27 subcriterios de las 7 dimensiones — cualquiera en 0 detiene la cascada, no solo esos 6. El mensaje de resultado nombra el/los subcriterio(s) puntual(es) que causaron el corte (p. ej. "Subcriterio(s) en 0 pts: 5.1"), igual que antes.

## 7. Sección V — Flota en detalle

La planilla nueva expande la Sección V de 2 preguntas declarativas a un detalle completo de **13 tipos de equipo**, cada uno con hasta 3 preguntas: cantidad mínima cumplida, horas de uso, y registro de mantenimiento.

| # | Equipo | Mínimo pliego | Cuenta para 5.1 |
|---|---|---|---|
| 1 | Cargadora CAT 938K | 3 | Sí |
| 2 | Motoniveladora CAT 140K | 3 | Sí |
| 3 | Retroexcavadora CAT 416E | 1 | Sí |
| 4 | Vibrocompactador CAT CS54B | 1 | Sí |
| 5 | Camión volcador IVECO 410 (14 m³) | 1 | Sí |
| 6 | Minicargadora BobCat | 1 | Sí |
| 7 | Camión regador IVECO 410 (18 m³) | 2 | Sí |
| 8 | Camioneta 4x4 Toyota Hilux | 5 | Sí |
| 9 | Camión capilar/lubricador (4.000 l) | 2 | Sí |
| 10 | Barredora Fracchia N337 | 2 | Sí |
| 11 | Camión volcador Scania 440 XT (20 m³) — OPCIONAL | — | No (fuera del pliego) |
| 12 | Topadora CAT D8T | 3 | Sí |
| 13 | Camión barrenieve — PROVISTO POR VELADERO | — | No (equipo cedido) |

Esquema de IDs: `F.<n>-cant` (solo los 11 core, van a **5.1**), `F.<n>-horas` y `F.<n>-mant` (los 13, van a **5.2** — la fórmula real de 5.2 sí promedia horas/mantenimiento de los 13, incluidos los 2 no-core). Total: 11×3 + 2×2 = **37 preguntas nuevas**.

**5.1 — fórmula real vs. aproximación del simulador.** La real (tabla T4) cuenta cuántos de los 11 equipos core cumplen su mínimo individual, calcula el % y lo convierte a 0-5 con esta tabla (reconstruida del Marco Metodológico):

| % de los 11 equipos que cumplen | Puntaje real | Puntaje del simulador (`round(%×5)`) |
|---|---|---|
| 100% (11/11) | 5 | 5 ✔ |
| ~73%-91% (8, 9 o 10/11) | 4 | 10→**5** ✗, 9→4 ✔, 8→4 ✔ |
| ~45%-64% (5, 6 o 7/11) | 3 | 7→3 ✔, 6→3 ✔, 5→**2** ✗ |
| 1%-36% (1 a 4/11) | 2 | 4→2 ✔, 3→**1** ✗, 2→**1** ✗, 1→**0** ✗ |
| 0% | 0 o 1 (según declaración) | 0 |

El simulador usa una aproximación **lineal** (`round(cumplen/11×5)`) porque no cuenta con la tabla de conversión real (ver Sección 8) — es exacta en los extremos y en varios puntos intermedios, pero diverge en los bordes de banda marcados con ✗ arriba. Es una limitación conocida, no un error de programación.

**5.2 — misma lógica**: la fórmula real combina horas de uso + registro de mantenimiento de los 13 equipos con dos sub-promedios ponderados; el simulador usa la misma proporción genérica (`checked/total×5`) sobre los 28 ítems de 5.2 (2 declarativos + 26 de equipo). Es una aproximación razonable pero no reproduce la fórmula exacta.

## 8. Tablas de conversión T1-T8 (reconstruidas, documentadas — no activas en el motor)

La hoja "Conversiones" que contiene los valores originales de estas tablas **no existe** en ninguna de las dos planillas compartidas — todas las fórmulas `VLOOKUP` que la referencian devuelven `#REF!` en ambos archivos. Lo que sigue es una reconstrucción aproximada a partir de las bandas cualitativas 0-5 que sí describe el Marco Metodológico v4.1, **no los valores originales exactos** de esas tablas.

**2.2 — Situación BCRA (peor situación, empresa y socios, 1 a 5):**
| BCRA | Puntaje |
|---|---|
| 1 | 5 |
| 2 | 4 (band "Aceptable"=3 aplica si además hay atraso <90 días — matiz que la fuente no permite resolver sin ese dato adicional) |
| 3 | 2 |
| 4 o 5 | 1 |
| Sin informe | 0 |

**3.1 — Cantidad de empleados en relación de dependencia:**
| Empleados | Puntaje |
|---|---|
| ≥ 20 | 5 |
| 11-19 | 4 |
| 5-10 | 3 |
| 1-4 | 2 |
| 0 | 1 |
| Sin F.931 presentado | 0 |

**3.4 — Horas de cobertura de una vacante (tabla T4 "de personal", distinta de la de Flota):**
| Horas | Puntaje |
|---|---|
| < 25 h | 5 |
| 25-48 h | 4 |
| ≥ 49 h | 2 |
| (sin roster ni back-up, con declaración de capacidad) | 3 — depende de documentación presentada, no solo de horas |
| (sin plan ni mecanismo) | 1 |
| Nada presentado | 0 |

**6.4 y 6.5 — % de ítems Nivel 2 verificados "Sí" (tabla T8, compartida):**
| % verificado "Sí" | Puntaje |
|---|---|
| 100% | 5 |
| ≥ 75% | 4 |
| ≥ 50% | 3 |
| ≥ 25% | 2 |
| > 0% | 1 |
| 0% | 0 |

**7.1 — % de personal domiciliado en Iglesia/Jáchal:**
| % | Puntaje |
|---|---|
| 100% | 5 |
| 85-99% | 4 |
| 60-84% | 3 |
| 40-59% | 2 |
| < 40% | 1 |
| Sin declaración | 0 |

**5.1 — ver Sección 7** (única tabla parcialmente activa en el motor, como aproximación lineal).

## 9. Ítems compartidos — J.39 y J.40

La planilla nueva reutiliza los ítems `J.39` (¿garantiza 80% de nómina radicada en San Juan?) y `J.40` (¿aplica orden de preferencia geográfica?) en **dos subcriterios a la vez**: 1.3 (Integridad) y 7.1 (Empleo local). Es decir, la misma pregunta y la misma evidencia contribuyen a dos puntajes distintos.

Esto se implementó dejando el `state` único por `id` de pregunta (una sola fuente de verdad), pero el ítem se **renderiza dos veces** en la página — una dentro del acordeón de la Dimensión I, otra dentro de la VII. Para evitar que dos elementos del DOM compartan el mismo `id` (inválido en HTML), cada checkbox lleva un id de DOM prefijado con el subcriterio contenedor (`chk-1.3-J.39-declarado` vs. `chk-7.1-J.39-declarado`) más un atributo `data-item-id` común. Al marcar cualquiera de las dos copias, `onToggleItem()` sincroniza automáticamente todas las copias vía `querySelectorAll('[data-item-id="J.39"]')` y recalcula ambos subcriterios con un solo click.

## 10. Changelog de contenido de esta versión

- **1.3** gana `J.39` y `J.40` → pasa de 5 a 7 preguntas.
- **7.1** pierde `J.24` y `J.34` → pasa de 5 a 3 preguntas (queda con `J.39, J.40, J.25`).
- **2.2** pierde `J.14` → pasa de 2 a 1 pregunta (queda binario: 0 o 5 según la situación BCRA declarada).
- **2.1** gana el piso "0 = No-Go" (antes no era excluyente).
- **Sección V** gana 37 preguntas nuevas (13 tipos de equipo).
- Motor de cálculo corregido (ponderación por subcriterio, no por promedio de dimensión).
- Orden de la cascada revertido a Auditoría → Elegibilidad → Gate.
- Los 4 casos de prueba (Vialidad, Transportes, Cordillera, Oeste) se reajustaron para que sigan demostrando las 4 categorías de dictamen (GO / PAI / No-Go por Gate / No-Go por piso) con la nueva estructura — sin este reajuste, la expansión de Flota diluía el puntaje de 5.1/5.2 con las 37 preguntas nuevas (todas "sí" por defecto) y rompía el resultado esperado de "Transportes" y "Oeste".
- **Regla de piso generalizada a los 27 subcriterios**: antes solo 6 subcriterios (5.1, 4.2, 3.4, 2.2, 2.1, 1.3) frenaban la cascada al llegar a 0 puntos; ahora cualquiera de los 27 lo hace (`subcriteriosEnCero()`), a pedido explícito del usuario.

## 11. Limitaciones conocidas

- 6 de 7 tablas de conversión (T1, T2 lo mismo T4-personal, T6, T8, T5) están documentadas pero no activas en el motor; solo 5.1 tiene una aproximación lineal (con divergencias documentadas en la Sección 7).
- El puntaje que arroja el simulador para "Vialidad Cuyana" (100 con todo marcado) no reproduce el 86.2 real de la planilla — la simplificación checkbox-a-score ya lo impedía desde la versión anterior; no es una regresión de este cambio.
- El peso de la Dimensión VII se mantiene en 8% (3+1+2+2) pese a que la hoja "Intake" de la planilla nueva lo marca "5 — REVISAR" en una tabla de referencia cruzada dentro de la misma hoja — es una inconsistencia no resuelta en la fuente, no una decisión de este simulador.
- La fundamentación normativa de los ítems G.19/G.20/G.31 del Gate Nivel 2 parece estar desplazada una fila en el documento Word original (detectado en una revisión anterior) — no afecta a este simulador porque no muestra ese campo, pero queda como nota si se reutiliza ese texto en el futuro.

## 12. Cómo verificar

1. Abrir `ejemplo.html` con `file://` en el navegador — no requiere build ni servidor.
2. Consola de DevTools sin errores al cargar.
3. Clickear los 4 botones de empresa uno por uno: deben dar GO (Vialidad), GO CON PLAN DE ACCIÓN (Transportes), NO-GO por Gate (Cordillera) y NO-GO por flota mínima (Oeste), sin ningún `TypeError` en consola.
4. Tildar/destildar `J.39` dentro del acordeón de la Dimensión I: el checkbox gemelo dentro de la Dimensión VII debe cambiar solo, y las cajas de puntaje de 1.3 y 7.1 deben actualizarse juntas.
5. Destildar "¿La empresa acepta ser sujeto de auditoría?": la cascada debe cortar en el primer paso, sin importar el resto de los datos.
6. Destildar todos los ítems de un subcriterio (ej. 1.3, peso 3) y confirmar que el puntaje global baja exactamente 3 puntos, no una fracción diluida por el promedio de la dimensión.
