# Documentación: qué se evalúa desde el Form y qué se verifica en la visita

Reconciliación entre **`Especificacion_Google_Form_Intake_Veladero_v4.pdf`** (la especificación que se le dio a Gemini para construir el Form) y los **encabezados reales** de `Intake de Proveedores — Mantenimiento del Camino de Veladero (respuestas).xlsx` (208 columnas, `A1:GZ1`, leído el 2026-07-21 — la hoja todavía no tiene respuestas cargadas, solo el encabezado).

El Form que quedó publicado **no es igual** al que pedía la especificación v4: alguien le agregó carga de archivo real a buena parte de las preguntas de Seguridad y Gobernanza (la v4 decía explícitamente *"NINGUNA pregunta lleva carga de archivo"*), pero dejó el resto de las secciones (Flota, Experiencia Técnica, Comunidad, la mayoría de RRHH) tal como estaban: autodeclaradas, sin adjunto. Esta tabla registra, ítem por ítem, cuál de las dos cosas pasó — para que sepan qué pueden dar por evaluado ya en el intake y qué depende 100% de la visita presencial.

## Cómo leer las tablas

| Símbolo | Significa |
|---|---|
| 📎 **Form con adjunto** | La hoja de respuestas tiene una columna `Carga de archivo: …` a continuación de la pregunta. Hay un documento real subido *antes* de la visita — se puede prechequear en gabinete. |
| 📝 **Form autodeclarado** | La pregunta está en el Form (Sí/No, desplegable, número o texto), pero **sin** columna de adjunto. Es la palabra de la empresa nomás; el documento que la respalda no se ve hasta la visita. |
| 🏗 **Solo visita** | El Form no pregunta nada de esto (aplica a Sección 1 y Sección 10, que la propia v4 marca "No requiere documentación de respaldo"). |

La columna **Nota** marca cuando el ítem real del Form se desvía de la especificación v4 (texto de pregunta distinto, código con otro significado, columna faltante, etc.).

---

## Hallazgos clave (antes de entrar a las tablas)

1. **El Gate N1 completo (24/24 ítems) ahora tiene adjunto real.** Todo lo excluyente de Seguridad y Salud Ocupacional (matrícula de HyS, ART, Programa de Seguridad, IPERC, aptitud médica, etc.) se puede prechequear en gabinete antes de agendar la visita — es la divergencia más importante respecto de la v4, y la más útil: permite descartar un No-Go por Gate sin esperar al viaje a Iglesia/Jáchal.
2. **Dos ítems del Gate N2 se quedaron sin adjunto pese a estar en el mismo bloque que sí lo tiene**: `G.33` (canteras habilitadas/remitos) y `G.20` (cotización de EPP a proveedores locales). Todo lo demás de Ambiente y Equipamiento (7/9) sí tiene carga de archivo.
3. **Gobernanza: `J.1` (estatuto/contrato social) y `J.2` (acta de designación + DNI socios) perdieron su columna de adjunto.** El resto del bloque (11/13, desde ARCA hasta la DDJJ de huelgas) sí la tiene. Además esas dos preguntas aparecen **duplicadas** en la hoja (columnas `BW` y `BY`, con el texto reformulado en tercera persona) sin prefijo `Carga de archivo:` — es un resto de una edición del Form, no una pregunta nueva real.
4. **Flota (Sección 8, 48 ítems), Experiencia Técnica (Sección 7, 13 ítems) y Desarrollo Comunitario (Sección 9, 9 ítems) siguen 100% autodeclaradas — cero adjuntos.** Coincide con lo que pedía la v4 para esas tres secciones (títulos/cédulas/contratos "se verifican en la visita presencial"), así que ahí no hay divergencia: la visita sigue siendo la única instancia de verificación real.
5. **RRHH quedó casi entero sin adjunto (8/9)** — solo `J.17` (libro de sueldos digital) tiene carga de archivo. Los CV de operarios, el carnet/IERIC, los CV de jefe de obra y el listado de back-up se declaran pero no se suben (razonable: la propia v4 dice que "se revisan mejor en original" en la visita).
6. **Seis tipos de equipo cambiaron la pregunta "Horas de uso" por "Modelo del equipo/vehículo"**: camión volcador IVECO 410 (14 m³), camión regador IVECO 410 (18 m³), camioneta Hilux, camión capilar/lubricador, camión volcador Scania 440 XT y camión barrenieve. Si el simulador (`ejemplo_con_form.html`) sigue esperando `F.x-horas` para esos seis equipos en el subcriterio 5.2, no va a encontrar la columna — hay que decidir si 5.2 usa "modelo" como proxy o si se pierde ese dato para esos 6 equipos.
7. **`J.32` cambió de tipo**: la v4 pedía un número ("¿En cuántas horas puede cubrir una vacante?"), el Form real pregunta "¿Puede cubrir una vacante de personal en menos de 24 horas?" (Sí/No). Afecta la tabla de conversión T de `Como_se_calcula_la_Matriz.md` §12 (que espera un valor en horas, no un booleano).
8. **Los códigos `J.30`/`J.31` significan cosas distintas según la fuente.** La v4 y el Form real usan `J.30` = Certificaciones ISO y `J.31` = Alianzas estratégicas (ambos sin adjunto). Pero el `DATA` del simulador (`ejemplo_con_form.html`, subcriterio 7.4) tiene `J.30` = "historial de auditorías previas" y `J.31` = "carta de compromiso gerencial" — preguntas que **no existen en el Form actual** (por eso están en `ITEMS_SIN_PREGUNTA_EN_FORM`). Si se quiere que 7.4 puntúe con datos reales del Form, hay que decidir entre remapear a Certificaciones ISO/Alianzas (lo que el Form sí pregunta) o agregar esas dos preguntas al Form.
9. **Columnas basura por ediciones del Form**: `Columna 204` y `Columna 201` (sin pregunta asociada, típico de Google Sheets cuando se borra una pregunta pero la columna de respuestas queda huérfana), más tres versiones de la misma pregunta de aceptación de la DDJJ (`GK`, `GT` casi idénticas con el párrafo completo como título, y `GX` con el texto corto "¿Aceptá la Declaración Jurada?"). Cualquier emparejamiento por texto exacto de columna (como el `FORM_ITEM_MAP` del simulador) hay que rehacerlo contra estos encabezados reales o se cae en cuanto entren respuestas.
10. ~~Dos preguntas nuevas sin código en la Matriz~~ — **resuelto** (ver hoja canónica "MATRIZ INTEGRAL DE EVALUACIÓN"): "¿Cuenta con un Proyecto de ECONOMÍA CIRCULAR y/o Plan de minimización de residuos?" ahora es `G.34` (Gate N2, subcriterio 6.4) y "¿Cuenta con el Relevamiento General de Riesgos Laboral (RGRL)?" es `G.35` (Gate N1, subcriterio 6.3; la pregunta está duplicada en dos columnas con el mismo texto). Ninguna de las dos tiene columna de adjunto.
11. **Metadata de Sección 1 partida**: la v4 pedía un solo campo "Email y teléfono de contacto"; el Form real lo separó en "Email del Representante Legal" (`G`) y "Teléfono de contacto del Representante Legal" (`GU`, al final de la hoja, no al lado de Email). Y el campo "Fecha de presentación" quedó con el texto del encabezado cortado a mitad: `"Fecha de presentación del formulario ("` — es así en la fuente, no un error de lectura.

**Resumen numérico** (sobre los 133 ítems que sí requieren algún documento — se excluyen Sección 1 "Identificación" y Sección 10 "DDJJ", que la v4 marca sin documentación):

| Bloque | Ítems | Con adjunto 📎 | Sin adjunto 📝 |
|---|---:|---:|---:|
| Gate N1 · Seguridad (Sección 2) | 25 | 24 | 1 (`G.35` RGRL) |
| Gate N2 · Ambiente y equipamiento (Sección 3) | 10 | 7 | 3 (incl. `G.34` Economía circular) |
| Gobernanza (Sección 4) | 12 | 10 | 2 |
| Salud Financiera (Sección 5) | 7 | 6 | 1 (consentimiento, no aplica) |
| RRHH (Sección 6) | 9 | 1 | 8 |
| Experiencia Técnica (Sección 7) | 13 | 0 | 13 |
| Flota (Sección 8) | 48 | 0 | 48 |
| Desarrollo Comunitario (Sección 9) | 9 | 0 | 9 |
| **Total** | **133** | **48 (36%)** | **85 (64%)** |

---

## Sección 1 · Identificación de la empresa — 🏗 Solo visita

La v4 marca explícitamente "No requiere documentación de respaldo". Confirmado: ningún campo de esta sección tiene columna de adjunto en la hoja real.

| Campo | Columna real en la hoja |
|---|---|
| Razón social | `B` |
| CUIT | `C` |
| Departamento | `D` |
| Localidad | `E` — texto cambiado a "Localidad donde está ubicada la empresa." |
| Representante legal | `F` — texto cambiado a "Representante legal - Indique nombre completo." |
| Email de contacto | `G` — separado del teléfono (antes un solo campo) |
| Teléfono de contacto | `GU` — separado y movido al final de la hoja |
| Fecha de presentación | `H` — encabezado truncado en la fuente: `"Fecha de presentación del formulario ("` |
| Antigüedad (años) | `I` |

---

## Sección 2 · Seguridad y salud en el trabajo (Gate N1 — excluyente) — 📎 24/25 con adjunto

| Cód. | Pregunta | Documento esperado | Form actual | Nota |
|---|---|---|---|---|
| G.3 | Profesional de HyS matriculado en San Juan | Matrícula vigente (Consejo de HyS SJ) | 📎 | |
| G.4 | Profesional con ≥4 años de experiencia | CV y antecedentes | 📎 | |
| G.5 | Técnicos de HyS para supervisión en campo | Nómina del equipo + CV | 📎 | |
| G.6 | Compromiso de completar equipo técnico | Nota de compromiso / cotización / plan de incorporación | 📎 | |
| G.7 | Legajo Técnico de Obra actualizado | Índice del legajo | 📎 | |
| G.1 | ART vigente 100% del personal | Póliza o constancia de afiliación | 📎 | |
| G.2 | Seguros de la actividad (RC, automotor, equipos) | Pólizas vigentes | 📎 | |
| G.8 | Programa de Seguridad | Programa + constancia de presentación/aprobación | 📎 | |
| G.9 | Política de HSSMA | Política firmada | 📎 | |
| G.10 | ≥3 procedimientos de trabajo seguro | Procedimientos aplicables | 📎 | |
| G.11 | Checklist diario preoperacional | Modelo de checklist + registros | 📎 | |
| G.12 | Matriz IPERC/IPER | Matriz de riesgos vigente | 📎 | |
| G.13 | Señalización y control de tránsito | Procedimiento (croquis, cartelería, banderilleros) | 📎 | |
| G.14 | Protocolo de clima adverso | Protocolo por viento/nieve/hielo/frío extremo | 📎 | |
| G.15 | Plan de mantenimiento preventivo | Plan + registros de servicio | 📎 | |
| G.16 | Documentación habilitante de vehículos/operadores | Licencias, RTO, cédulas, aptos médicos | 📎 | |
| G.21 | Plan Anual de Capacitación | Plan anual | 📎 | |
| G.22 | Plan de Contingencias | Plan de contingencias | 📎 | |
| G.23 | Traslado médico / cobertura de emergencias | Contrato, convenio o cotización | 📎 | |
| G.25 | Servicio de Medicina Laboral | Contrato o cotización | 📎 | |
| G.28 | Aptitud médica del personal expuesto | Certificados de aptitud médica | 📎 | |
| G.24 | Índice de Siniestralidad ART (3 años) | Índice emitido por la ART | 📎 | |
| G.26 | Nómina RAR vigente | RAR/NTE o constancia ante ART | 📎 | |
| G.27 | Monitoreos del personal expuesto en RAR | Mediciones/estudios | 📎 | |
| G.35 | Relevamiento General de Riesgos Laborales (RGRL) presentado | RGRL / constancia ante ART | 📝 | **Sin adjunto** — pregunta agregada al Form, duplicada en dos columnas con el mismo texto |

## Sección 3 · Ambiente y equipamiento (Gate N2 — brecha) — 📎 7/10 con adjunto

| Cód. | Pregunta | Documento esperado | Form actual | Nota |
|---|---|---|---|---|
| G.29 | Profesional / asesoramiento ambiental | CV, contrato o cotización | 📎 | |
| G.30 | Plan de gestión de residuos (incl. peligrosos) | Procedimiento, manifiestos o contrato con operador habilitado | 📎 | |
| G.33 | Ripio/áridos de canteras habilitadas | Constancia de cantera + remitos de compra | 📝 | **Sin adjunto** — se quedó sin `Carga de archivo`, a verificar en visita |
| G.34 | Proyecto de Economía Circular / minimización de residuos | Plan o proyecto documentado | 📝 | **Sin adjunto** — pregunta agregada al Form |
| G.17 | Registros de entrega de EPP | Registros de entrega | 📎 | |
| G.18 | Certificación de los EPP | Certificados/fichas técnicas/declaración de conformidad | 📎 | |
| G.19 | EPP de invierno para alta montaña | Stock, registros, fichas o cotización | 📎 | |
| G.20 | Compra de EPP a proveedores locales | Cotización de 2 proveedores locales | 📝 | **Sin adjunto** — se declara la intención, la cotización se muestra en visita |
| G.31 | Equipamiento de contingencia | Inventario / registro de inspección | 📎 | |
| G.32 | Comunicación de alta montaña | Registro de equipos disponibles | 📎 | Pregunta duplicada sin adjunto en `GW` (col. 205), resto de una edición del Form |

## Sección 4 · Datos legales y de gobierno de la empresa — 📎 10/12 con adjunto

| Cód. | Pregunta | Documento esperado | Form actual | Nota |
|---|---|---|---|---|
| J.1 | Estatuto o contrato social inscripto | Contrato social/estatuto inscripto | 📝 | **Sin adjunto** — pregunta duplicada sin `Carga de archivo` en `BW` |
| J.2 | Acta de designación + DNI socios | Acta + DNI de los socios | 📝 | **Sin adjunto** — pregunta duplicada sin `Carga de archivo` en `BY` |
| J.3 | Inscripción ante ARCA (ex AFIP) | Constancia de inscripción | 📎 | |
| J.35 | Domicilio/establecimiento en SJ ≥12 meses | Constancia de domicilio fiscal + habilitación | 📎 | Puede demorar — spec recomienda gestionar cuanto antes |
| J.4 | Organigrama institucional | Organigrama | 📎 | |
| J.5 | Matriz de contactos y escalamiento | Listado formal de contactos | 📎 | |
| J.6 | Poder de representación / matriz de responsabilidades | DDJJ, anexo o poderes firmados | 📎 | |
| J.7 | Libre Deuda Previsional (ARCA) | Certificado | 📎 | Puede demorar |
| J.8 | Libre Conflictividad (Subsec. Trabajo SJ) | Certificado | 📎 | Puede demorar |
| J.9 | Último F.931 con constancia de pago | F.931 pagado | 📎 | |
| J.37 | Libre Deuda Sindical | Certificado (lo emite el gremio) | 📎 | Puede demorar |
| J.38 | DDJJ de huelgas/bloqueos (2 años) + encuadre sindical | DDJJ + constancia de encuadre | 📎 | |

## Sección 5 · Situación económica y financiera — 📎 6/7 con adjunto

| Cód. | Pregunta | Documento esperado | Form actual | Nota |
|---|---|---|---|---|
| J.10 | Últimos 2 EECC legalizados por el CPCE | Estados contables | 📎 | |
| J.11 | Ventas/compras post-balance (libro IVA) | Detalle post-balance | 📎 | |
| J.12 | Últimas 6 DDJJ de IVA e IIBB | DDJJ | 📎 | |
| J.13 | Situación BCRA (empresa y socios, 1-5) | Informe del BCRA | 📎 | |
| J.14 | Manifestación de bienes validada por CPCE | Manifestación de bienes | 📎 | |
| J.15 | Informe comercial VERAZ/NOSIS | Informe | 📎 | |
| J.33 | Acepta auditoría (financiera/técnica/flota) | — | 📝 | Sin adjunto por diseño: es consentimiento, no documento. "No" saca a la empresa del proceso |

## Sección 6 · Personal y organización del trabajo — 📎 1/9 con adjunto

| Cód. | Pregunta | Documento esperado | Form actual | Nota |
|---|---|---|---|---|
| J.16 | Dotación (según último F.931) | (dato numérico, respaldado por J.9) | 📝 | |
| J.17 | Libro de sueldos digital / altas tempranas ARCA | Libro de sueldos del último mes | 📎 | Único ítem con adjunto de la sección |
| J.18 | CV de operarios clave de maquinaria | CV de operarios | 📝 | "Se revisan mejor en original" — spec pide tenerlos en visita |
| J.19 | Carnet profesional / tarjeta IERIC | Carnets/tarjetas | 📝 | |
| J.20 | CV jefe de obra, capataces, supervisor fijo | CV | 📝 | |
| J.21 | Esquema de turnos 14x7 y relevos | Plan de roster (texto descriptivo, sin adjunto esperado) | 📝 | |
| J.22 | Procedimiento de traslado del personal | Procedimiento logístico | 📝 | |
| J.23 | Listado de personal de reemplazo (back-up) | Listado de back-up | 📝 | |
| J.32 | Cobertura de vacante de personal | Registro de tiempo de cobertura | 📝 | **Cambió de tipo**: v4 pedía horas (número); el Form real pregunta "¿en menos de 24 hs?" (Sí/No) |

## Sección 7 · Experiencia de la empresa en trabajos similares — 📝 0/13 con adjunto

Coincide con la v4: toda la sección es autodeclarada, respaldada por "contratos y actas de recepción" que **se revisan únicamente en la visita presencial**.

| Cód. | Pregunta (resumen) | Documento esperado (para la visita) |
|---|---|---|
| Q4.1 | Años de experiencia en caminos no pavimentados | Contratos/actas de recepción |
| Q4.2 | Estabilización de suelos — cantidad de proyectos | Contratos y fichas técnicas del tratamiento aplicado |
| Q4.3 | Km de caminos no pavimentados mantenidos (5 años) | Contratos con tramos y kilometraje |
| Q4.4 | Perfilado y control de polvo | Registros de obra, fotos, informes técnicos |
| Q4.5 | Experiencia en industria minera (antigüedad) | Contratos u órdenes de compra con mineras |
| Q4.6 | Altitud máxima operada de forma sostenida | Contratos, ubicación de obras, informes de operación |
| Q4.7 | Contratos con empresas mineras (5 años) | Listado de contratos con referencias verificables |
| Q4.8 | Vialidad invernal (nieve/hielo) | Contratos o actas de servicios invernales |
| Q4.9 | Operación 24 hs en nieve/hielo bajo cero | Registros de operación invernal, planillas de guardia |
| Q4.10 | Años de experiencia promedio de operadores | Legajos/CV de operadores |
| Q4.11 | Operadores certificados/habilitados | Certificados/licencias vigentes |
| Q4.12 | Años de experiencia del responsable técnico/jefe de obra | CV + matrícula profesional (si aplica) |
| Q4.13 | Programa de capacitación e inducción documentado | Programa + registros de asistencia |

## Sección 8 · Equipos y capacidad mecánica (flota) — 📝 0/48 con adjunto

Coincide con la v4: "Los títulos, cédulas y registros de cada unidad se verifican en la visita presencial." Ningún ítem de flota tiene `Carga de archivo` en la hoja real — la totalidad de títulos, cédulas, boletos de compra-venta y registros de mantenimiento se revisa en sitio.

**Cambio a marcar aparte**: en 6 de los 13 tipos de equipo, la pregunta "Horas de uso" fue reemplazada por "Modelo del equipo/vehículo" en el Form real:

| Equipo | Pregunta v4 | Pregunta real en el Form |
|---|---|---|
| Camión volcador IVECO 410 (14 m³) | Horas de uso | Modelo del vehículo |
| Camión regador IVECO 410 (18 m³) | Horas de uso | Modelo del equipo |
| Camioneta 4x4 Toyota Hilux | Horas de uso | Modelo del vehículo |
| Camión capilar/lubricador (4.000 l) | Horas de uso | Modelo del equipo |
| Camión volcador Scania 440 XT (20 m³, opcional) | Horas de uso | Modelo del equipo |
| Camión barrenieve (provisto por Veladero) | Horas de uso | Modelo del equipo |

Los otros 7 equipos (Cargadora CAT 938K, Motoniveladora CAT 140K, Retroexcavadora CAT 416E, Vibrocompactador CS54B, Minicargadora BobCat, Barredora Fracchia N337, Topadora CAT D8T) mantienen las 3 preguntas originales (cantidad, horas de uso, registro de mantenimiento) sin cambios ni adjunto.

Resto de la sección (`Q5.1`, `Q5.2`, `Q5.36`, `Q5.43`–`Q5.48`) también 📝 sin adjunto, sin cambios de tipo respecto a la v4.

## Sección 9 · Empleo local y desarrollo de la comunidad — 📝 0/9 con adjunto

Coincide con la v4: toda la documentación de respaldo (DNI de residencia, política de contratación, listados de subproveedores, convenios, certificados ISO) **se revisa en la visita**.

| Cód. | Pregunta (resumen) | Documento esperado (para la visita) |
|---|---|---|
| J.39 | ≥70-80% de personal de comunidades + resto radicado en San Juan | DNI y constancia de residencia efectiva (≥12 meses) |
| J.40 | Prioridad de contratación Iglesia/Jáchal | Política de contratación |
| J.25 | Plan de capacitación para la comunidad | Plan de capacitación |
| J.26 | Política de compra/contratación local | Política o procedimiento |
| J.27 | Listado de subproveedores/talleres/comercios locales | Listado |
| J.28 | Contrato de UT / acuerdo inscripto (si se presenta en consorcio) | Contrato de UT con % de cada firma |
| J.29 | Convenios con cámaras/cooperativas/universidades (si es empresa individual) | Convenios vigentes |
| J.30 | Certificaciones ISO (9001/14001/45001 u otras) | Certificados | *ver Hallazgo 8 — en `DATA` del simulador este código apunta a otra pregunta* |
| J.31 | Disposición a alianzas estratégicas | Carta de compromiso | *ver Hallazgo 8 — en `DATA` del simulador este código apunta a otra pregunta* |

## Sección 10 · Declaración Jurada y firma — 🏗 Solo visita

La v4 marca "No requiere documentación de respaldo" — la firma en papel se completa en la visita. Confirmado, sin adjuntos.

| Campo | Columna real |
|---|---|
| Acepto la DDJJ (casilla) | `GK` — el título de la pregunta es el párrafo completo de la DDJJ, no "Acepto la declaración jurada precedente" como pedía la v4. Hay 2 duplicados sin usar: `GT` (idéntica) y `GX` ("¿Aceptá la Declaración Jurada?") |
| Aclaración (nombre y apellido) | `GL` |
| DNI | `GM` |
| Cargo | `GN` |
| Empresa | `GO` |
| Lugar | `GP` |
| Fecha | `GQ` |

## Preguntas nuevas sin código en la Matriz

No estaban en la v4 ni tienen subcriterio asignado en `Como_se_calcula_la_Matriz.md`:

| Pregunta | Columna | Adjunto | Candidata natural |
|---|---|---|---|
| ¿Cuenta con un Proyecto de ECONOMÍA CIRCULAR y/o Plan de minimización de residuos? | `GR` | 📝 No | 6.4 Gestión ambiental (junto a G.29/G.30/G.33) |
| ¿Cuenta con el Relevamiento General de Riesgos Laboral (RGRL)? | `GS` (duplicada en `GV`) | 📝 No | 6.1 o 6.2 SSHyS (junto al resto del Gate N1) |

## Columnas huérfanas (sin pregunta activa)

`Columna 204` (`GY`) y `Columna 201` (`GZ`) — típico resto de Google Sheets cuando se borra una pregunta del Form pero la columna de respuestas ya generada queda. No requieren acción sobre documentación, pero conviene limpiarlas antes de que alguien las confunda con un campo real al mapear respuestas.
