# Cómo se calcula la Matriz Integral de Evaluación (explicada con ejemplos reales)



## 1. La idea en un párrafo

Cada empresa se evalúa marcando evidencia (documentos, certificados, checklists) en 27 preguntas-grupo llamadas **subcriterios**, agrupadas en 7 **dimensiones**. Cada subcriterio saca un puntaje de 0 a 5, ese puntaje se pondera por el peso del subcriterio, se suman todos y da un **puntaje global sobre 100**. Aparte, hay una lista de condiciones excluyentes (**Gate HSE** y **pisos**) que puede frenar todo el proceso con un **No-Go**, sin importar cuán alto sea el puntaje. Al final, una **cascada de decisión** revisa esas condiciones en un orden fijo y entrega el dictamen: **GO**, **GO con Plan de Acción**, o **No-Go**.


## 3. Paso 1 — puntaje de cada subcriterio (0 a 5)

```
puntaje_sub = round((preguntas_verificadas / preguntas_totales) × 5)
```

Solo cuentan las preguntas marcadas `scoreable` (todas, salvo dos preguntas declarativas de la Sección V que no puntúan). **Excepción: 2.2** no usa esta fórmula — su puntaje sale de la tabla de conversión real T6 aplicada a la situación BCRA declarada (ver Sección 12).

**Ejemplo real — subcriterio 1.1 de Transportes del Norte** ("Conformación legal, radicación y arraigo provincial"):

| Ítem | ¿Verificado? |
|---|---|
| J.1 — Estatuto/contrato social | Sí |
| J.2 — Acta de designación + DNI socios | Sí |
| J.3 — Constancia AFIP | Sí |
| J.35 — Domicilio fiscal + habilitación ≥12 meses | **No** |

3 de 4 verificados →

```
puntaje_1.1 = round((3 / 4) × 5) = round(3.75) = 4
```

## 4. Paso 2 — ponderado de cada subcriterio

```
ponderado_sub = (puntaje_sub / 5) × peso_sub
```

**Siguiendo el ejemplo**, 1.1 tiene peso 4:

```
ponderado_1.1 = (4 / 5) × 4 = 3.2
```

Este es el paso donde una versión anterior del simulador tenía un bug: promediaba los puntajes 0-5 de los subcriterios de una dimensión y recién ahí aplicaba el peso de toda la dimensión — matemáticamente distinto a ponderar cada subcriterio por su propio peso. La fórmula real de la planilla (`Matriz E1 (GO)`, celda `G56`: `=(F56/5)*D56`) confirma que el cálculo correcto es por subcriterio.

*Por qué importa* — con los 3 subcriterios reales de la Dimensión II de Transportes (puntajes 2, 5, 3 — pesos 5, 6, 4):
- Fórmula incorrecta (promedio primero): `(2+5+3)/3 = 3.333` → `(3.333/5)×15 = 10.0`
- Fórmula correcta (por subcriterio, la que usa el simulador): `(2/5×5) + (5/5×6) + (3/5×4) = 2.0 + 6.0 + 2.4 = 10.4`

Son números parecidos pero no iguales — y en dimensiones con pesos más desparejos entre subcriterios la diferencia crece.

## 5. Paso 3 — sumar los ponderados de una dimensión

```
obtenido_dim = Σ ponderado_sub   (de los subcriterios de esa dimensión)
```

**Dimensión I completa de Transportes del Norte:**

| Subcriterio | Peso | Puntaje 0-5 | Ponderado |
|---|---|---|---|
| 1.1 Conformación legal | 4 | 4 | 3.2 |
| 1.2 Estructura de gobierno | 3 | 5 | 3.0 |
| 1.3 Integridad y libre conflictividad | 3 | 3 | 1.8 |
| **Obtenido Dimensión I** | **10** | | **8.0** |

## 6. Paso 4 — sumar las 7 dimensiones = puntaje global

```
puntaje_global = Σ obtenido_dim   (de las 7 dimensiones)
```

**Tabla completa de Transportes del Norte SA**, calculada subcriterio por subcriterio:

| Dim | Peso | Subcriterios (puntaje 0-5) | Obtenido | Score 0-5 de la dimensión* |
|---|---|---|---|---|
| I | 10 | 1.1=4 · 1.2=5 · 1.3=3 | 8.0 | 4.0 |
| II | 15 | 2.1=2 · 2.2=5 · 2.3=3 | 10.4 | 3.5 |
| III | 15 | 3.1=3 · 3.2=3 · 3.3=5 · 3.4=3 | 10.2 | 3.4 |
| IV | 18 | 4.1=3 · 4.2=3 · 4.3=3 · 4.4=3 | 10.8 | 3.0 |
| V | 20 | 5.1=3 · 5.2=3 · 5.3=3 · 5.4=5 | 12.8 | 3.2 |
| VI | 14 | 6.1=5 · 6.2=5 · 6.3=5 · 6.4=3 · 6.5=3 | 12.0 | 4.3 |
| VII | 8 | 7.1=2 · 7.2=3 · 7.3=3 · 7.4=3 | 4.2 | 2.6 |
| **Total** | **100** | | **68.4** | |

```
puntaje_global = 8.0 + 10.4 + 10.2 + 10.8 + 12.8 + 12.0 + 4.2 = 68.4
```

*El "score 0-5" que se muestra en los paneles de cada dimensión sale de despejar en la misma proporción que el global: `(obtenido_dim / peso_dim) × 5`. Por ejemplo, la Dimensión VI: `(12.0 / 14) × 5 = 4.3`. No es un promedio simple de sus subcriterios — es el % de logro de la dimensión, multiplicado por 5.

## 7. Cuando una misma pregunta cuenta para dos subcriterios (J.39 y J.40)

Los ítems `J.39` (¿garantiza 70-80% de personal de comunidades y el resto radicado en San Juan?) y `J.40` (¿aplica orden de preferencia geográfica?) se usan **dos veces**: dentro de 1.3 (Integridad, Dimensión I) y dentro de 7.1 (Empleo local, Dimensión VII). Es la misma evidencia contribuyendo a dos puntajes distintos — técnicamente hay un solo estado por `id` de pregunta, pero se muestra en dos acordeones distintos y ambas copias se sincronizan solas al tildar cualquiera de las dos.

**Por qué el subcriterio 1.3 de Transportes dio 3 y no 5**: de sus 7 preguntas (J.7, J.8, J.9, J.37, J.38, J.39, J.40), quedaron sin verificar J.37, J.39 y J.40 → 4 de 7 verificadas → `round((4/7)×5) = round(2.857) = 3`.

**El mismo J.39 y J.40 sin marcar también golpea a 7.1**: de sus 3 preguntas (J.39, J.40, J.25), solo J.25 quedó verificada → 1 de 3 → `round((1/3)×5) = round(1.667) = 2`. Por eso 7.1 es el subcriterio más bajo de toda la tabla de Transportes (puntaje 2) — no por un problema de empleo local en sí, sino porque arrastra el mismo faltante que ya afectó a 1.3.

## 8. El Gate HSE — cuando un solo "No" tira abajo todo, sin importar el puntaje

35 ítems en dos niveles, evaluados en paralelo a los 27 subcriterios:

- **Nivel 1 — excluyente (25 ítems, prefijo `G.`)**: un solo "No" verificado en cualquiera de ellos produce **No-Go inmediato**, sin promediar con nada más. Alimentan 6.1 (5 ítems), 6.2 (16 ítems) y 6.3 (4 ítems, incluye `G.35` — RGRL, agregado cuando el Form sumó esa pregunta).
- **Nivel 2 — brecha (10 ítems)**: un "No" no excluye, pero queda como discrepancia y limita el puntaje de 6.4 (4 ítems, incluye `G.34` — Economía circular, agregado cuando el Form sumó esa pregunta) y 6.5 (6 ítems).

**Ejemplo real — Constructora Cordillera SRL**: en el Intake declaró tener ART vigente (`G.1`). En la visita presencial, la póliza exhibida estaba **vencida hacía 40 días** → `declarado: Sí` / `verificado: No`. Como `G.1` es Gate Nivel 1, esto solo alcanza para dictaminar **No-Go** — y de hecho, si se recalculan sus 27 subcriterios con el resto de la evidencia (todo marcado), Cordillera llega a un puntaje global de **100/100**. No importa: el Gate se revisa *antes* que el puntaje en la cascada de decisión (Sección 10), así que el 100 nunca llega a evaluarse.

## 9. Los pisos — cuando un subcriterio en 0 tira abajo todo, sin importar el promedio

Un **piso** es una condición excluyente: si un subcriterio llega a 0/5, el dictamen es No-Go aunque el resto de la matriz esté perfecto. La versión actual del motor de `ejemplo.html` aplica esto de forma **general a los 27 subcriterios** (cualquiera que llegue a 0 frena todo), no solo a una lista corta — aunque hay 6 que son los que históricamente se destacan porque son los más fáciles de perder por completo:

| Subcriterio | Motivo |
|---|---|
| 1.3 · Integridad y libre conflictividad | Sin certificados de conflictividad/deuda sindical en regla |
| 2.1 · Solvencia, liquidez y capital de trabajo | Sin estados contables ni información contable verificable |
| 2.2 · Endeudamiento / financiamiento de flota | Sin capacidad demostrable de financiar la flota exigida (ver también el piso propio de situación BCRA, más abajo) |
| 3.4 · Roster 14x7 y reemplazo 24 h | Sin esquema de turnos ni back-up ante ausencias |
| 4.2 · Industria minera y alta montaña | Sin experiencia previa en minería o alta montaña |
| 5.1 · Flota disponible vs. mínima | No alcanza la flota mínima exigida por el pliego |

Más dos pisos "de bloque" (no de un solo subcriterio):
- **Salud Financiera** — promedio de 2.1, 2.2 y 2.3 `< 2` → No-Go.
- **SSMA excluyente** — mínimo de 6.1, 6.2 y 6.3 `≤ 1` → No-Go.

Y un piso adicional específico de **2.2** que usa el dato crudo declarado en `J.13` (situación BCRA, escala real 1-5 del Central de Deudores de empresa y socios): **situación 4 o 5 → No-Go**. Situación 5 = Irrecuperable (atrasos superiores a un año o insolvencia/instancia judicial). Situación 3 **no** es piso — es la banda mínima que alcanza para seguir en carrera (ver tabla T6, Sección 12).

**Ejemplo real — Servicios Viales del Oeste SA**: no marcó ninguno de los 11 equipos "core" de flota exigidos por el pliego (cargadora, motoniveladora, retro, etc. — ver Sección 11). Eso hace que:

```
puntaje_5.1 = round((0 / 11) × 5) = 0
```

Con el resto de la matriz en 5/5, Oeste llega a un puntaje global de **92/100** — un número que, aislado, calificaría cómodo para GO. Pero como 5.1 = 0 es un piso, el dictamen es **No-Go** igual. La cascada nunca llega a mirar el 92.

## 10. La cascada de decisión — el orden que manda

El motor revisa las condiciones en un orden fijo y **se detiene en la primera que aplica**. Una empresa puede tener puntaje alto y aun así recibir No-Go si incumple algo antes en el orden:

1. **Consentimiento de auditoría** (declarado = No → No-Go)
2. **Elegibilidad geográfica** (fuera de Iglesia/Jáchal → No-Go)
3. **Gate Nivel 1** (25 ítems — un "No" verificado → No-Go)
4. **Salud Financiera** (promedio 2.1–2.3 `< 2` → No-Go)
5. **Piso 2.2 · Situación BCRA** (situación declarada 3, 4 o 5 de empresa o socios → No-Go, independiente del puntaje 0-5 del subcriterio)
6. **SSMA excluyente** (mínimo 6.1/6.2/6.3 `≤ 1` → No-Go)
7. **Cualquier subcriterio en 0** (de los 27 — un solo 0/5 → No-Go)
8. **Puntaje global `< 55`** → No-Go
9. **Puntaje global 55 a 69,9** → GO con Plan de Acción
10. **Puntaje global `≥ 70`** → GO

> Nota: versiones anteriores de este documento describían varios de los pasos 7+ como chequeos separados y nombrados (5.1, 4.2, 3.4, 2.2, 2.1, 1.3, en ese orden). El motor real de `ejemplo_con_form.html` los generalizó en un solo paso que cubre los 27 subcriterios — el efecto práctico es el mismo (esos 6 siguen frenando todo si llegan a 0), pero ya no hace falta nombrarlos uno por uno: alcanza con "¿algún subcriterio quedó en 0?". El paso 5 (piso BCRA de 2.2) es la excepción: es un chequeo aparte porque no mira el puntaje 0-5 del subcriterio, sino el dato crudo declarado en `J.13`.

**Los 4 casos reales, trazados paso a paso por la cascada:**

| Empresa | Pasos 1-6 (auditoría, elegibilidad, Gate, financiero, BCRA, SSMA) | Paso 7 (subcriterio en 0) | Puntaje global | Dictamen |
|---|---|---|---|---|
| Vialidad Cuyana SRL | Todos OK | Ninguno en 0 | 100.0 | **GO** |
| Transportes del Norte SA | Todos OK (financiero: prom. 3.33 ≥ 2; BCRA en situación 1-2) | Ninguno en 0 | 68.4 | **GO con Plan de Acción** (68.4 está en la banda 55-69.9) |
| Constructora Cordillera SRL | **Falla en el paso 3** — `G.1` (ART) verificado en "No" | *(nunca se llega a evaluar)* | *(100.0, pero irrelevante)* | **No-Go — Gate HSE** |
| Servicios Viales del Oeste SA | Todos OK | **Falla en el paso 7** — 5.1 = 0 | *(92.0, pero irrelevante)* | **No-Go — piso de flota mínima** |

Esto es lo que hace a la cascada distinta de "sacar un promedio": Cordillera y Oeste tienen puntajes altísimos (100 y 92) y aun así son No-Go, porque el proceso corta antes de siquiera mirar el puntaje global.

## 11. Sección V — Flota en detalle

La Sección V expande el antiguo par de preguntas declarativas a un detalle de **13 tipos de equipo**, cada uno con hasta 3 preguntas: cantidad mínima cumplida, horas de uso, y registro de mantenimiento.

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

Los 11 equipos "core" (todos menos el 11 y el 13) son los que cuentan para **5.1**. Los 13 completos (incluidos los 2 no-core) alimentan **5.2** con sus horas de uso y registro de mantenimiento.

**5.1 — fórmula real vs. aproximación del simulador.** La fórmula real (tabla T4 de la planilla) cuenta cuántos de los 11 equipos cumplen su mínimo, calcula el % y lo convierte a 0-5 con esta tabla (reconstruida del Marco Metodológico, no son los valores originales exactos):

| % de los 11 equipos que cumplen | Puntaje real | Puntaje del simulador (`round(%×5)`) |
|---|---|---|
| 100% (11/11) | 5 | 5 ✔ |
| ~73%-91% (8, 9 o 10/11) | 4 | 10→**5** ✗, 9→4 ✔, 8→4 ✔ |
| ~45%-64% (5, 6 o 7/11) | 3 | 7→3 ✔, 6→3 ✔, 5→**2** ✗ |
| 1%-36% (1 a 4/11) | 2 | 4→2 ✔, 3→**1** ✗, 2→**1** ✗, 1→**0** ✗ |
| 0% | 0 o 1 (según declaración) | 0 |

El simulador usa una aproximación **lineal** (`round(cumplen/11×5)`) porque no cuenta con la tabla de conversión real — exacta en los extremos, pero diverge en los bordes de banda marcados con ✗. Es una limitación conocida, no un error de programación.

**5.2** usa la misma lógica genérica (`verificados/total×5`) sobre sus 28 ítems — una aproximación razonable, no la fórmula exacta de la planilla (que combina horas + mantenimiento con dos sub-promedios ponderados).

## 12. Tablas de conversión T1-T8 — documentadas; T6 activa en el motor, el resto no

5 de los 27 subcriterios (3.1, 3.4, 6.4, 6.5, 7.1) usan en la planilla real una **tabla de conversión propia** a partir de un dato numérico declarado (cantidad de empleados, horas de cobertura, % verificado, etc.) en vez de la proporción simple de checkboxes. La hoja "Conversiones" que contenía los valores originales de esas tablas **no existe** en ninguna de las dos planillas compartidas — todas las fórmulas `VLOOKUP` que la referencian devuelven `#REF!`. Lo que sigue para esos 5 es una reconstrucción aproximada a partir de las bandas cualitativas que sí describe el Marco Metodológico v4.1, no los valores originales exactos.

**2.2 — Situación BCRA (peor situación, empresa y socios, 1 a 5) — tabla T6, activa en el motor:**
| BCRA | Puntaje |
|---|---|
| 1 | 5 |
| 2 | 3 |
| 3 | 1 |
| 4 o 5 | 0 (además, piso → No-Go — ver Sección 9) |

A diferencia de las otras 5 tablas de esta sección, **T6 sí está implementada tal cual**: `ejemplo_con_form.html` la usa directamente (`scoreT6Bcra()`) para calcular el puntaje 0-5 de 2.2 a partir del valor crudo declarado en `J.13`, no de una proporción de checkboxes. `J.14` (manifestación de bienes) queda dentro de 2.2 como ítem de evidencia/checklist — el Marco Metodológico v4.1 la describe como refuerzo cualitativo de la misma banda BCRA, no como un eje de puntaje separado — pero ya no mueve el puntaje del subcriterio de forma independiente. Si todavía no se cargó ninguna respuesta de la hoja (sin dato de BCRA disponible), 2.2 cae a la aproximación genérica de checkboxes (`verificados/total×5` sobre J.13+J.14) para no mostrar un puntaje vacío.

**3.1 — Cantidad de empleados en relación de dependencia:**
| Empleados | Puntaje |
|---|---|
| ≥ 20 | 5 |
| 11-19 | 4 |
| 5-10 | 3 |
| 1-4 | 2 |
| 0 | 1 |
| Sin F.931 presentado | 0 |

**3.4 — Horas de cobertura de una vacante:**
| Horas | Puntaje |
|---|---|
| < 25 h | 5 |
| 25-48 h | 4 |
| ≥ 49 h | 2 |
| Sin roster ni back-up, con declaración de capacidad | 3 (depende de documentación, no solo horas) |
| Sin plan ni mecanismo | 1 |
| Nada presentado | 0 |

**6.4 y 6.5 — % de ítems Nivel 2 verificados "Sí":**
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

**5.1** ya se cubrió en la Sección 11 — es una aproximación lineal, no la tabla real. **2.2** es distinta: T6 (más arriba) es la tabla real, completa, implementada tal cual — no una aproximación.

## 13. Limitaciones conocidas

- 5 de 27 subcriterios (3.1, 3.4, 6.4, 6.5, 7.1) usan en el simulador la proporción simple de checkboxes en vez de su tabla de conversión real; 5.1 tiene una aproximación lineal (con divergencias documentadas en la Sección 11); 2.2 es el único que tiene su tabla de conversión real (T6) implementada sin aproximar.
- El puntaje que da el simulador para "Vialidad Cuyana" con todo marcado (100) no reproduce el 86,2 real de la planilla — es la misma simplificación checkbox-a-score, no una regresión nueva.
- El peso de la Dimensión VII se mantiene en 8% (3+1+2+2) pese a que la hoja "Intake" de la planilla marca "5 — REVISAR" en una tabla de referencia cruzada — inconsistencia de la fuente, no una decisión de este documento.

## 14. Cómo verificar el simulador

1. Abrir `ejemplo.html` con `file://` en el navegador — no requiere build ni servidor.
2. Clickear los 4 botones de empresa: deben dar GO (Vialidad), GO CON PLAN DE ACCIÓN (Transportes, ~68.4), NO-GO por Gate (Cordillera) y NO-GO por flota mínima (Oeste) — los mismos 4 casos recorridos en la Sección 10.
3. Tildar/destildar `J.39` dentro del acordeón de la Dimensión I: el checkbox gemelo dentro de la Dimensión VII cambia solo, y 1.3 y 7.1 se recalculan juntos (Sección 7).
4. Destildar "¿La empresa acepta ser sujeto de auditoría?": la cascada corta en el paso 1, sin importar el resto de los datos.
5. Destildar todos los ítems de un subcriterio de peso conocido (ej. 1.3, peso 3) y confirmar que el puntaje global baja exactamente 3 puntos — no una fracción diluida por un promedio de dimensión.
