# FinanceOps AI – Control Center

## Ecosistema Inteligente para la Gestión Automatizada de Incidencias Financieras con IA

Proyecto desarrollado como entrega final del curso **IA Automation – Coderhouse**.

**FinanceOps AI – Control Center** es un prototipo funcional de automatización orientado a la recepción, clasificación, análisis y gestión de incidencias financieras y operativas mediante Inteligencia Artificial, automatización de procesos y control humano (Human-in-the-Loop).

---

## 🎯 Objetivo

Diseñar un ecosistema capaz de recibir incidencias financieras por correo electrónico, registrar la información en una base estructurada, analizar cada caso mediante IA y determinar automáticamente el tratamiento correspondiente.

Según el nivel de riesgo y las características de la incidencia, el sistema puede:

- Gestionar una ruta automática.
- Solicitar aprobación humana mediante HITL.
- Registrar errores técnicos.
- Mantener trazabilidad de las ejecuciones.
- Responder al usuario conservando el hilo original del correo.
- Registrar indicadores operativos para su posterior monitoreo.

---

## 🛠️ Tecnologías utilizadas

- **Make** – Orquestación y automatización de procesos.
- **Airtable** – Base de datos, trazabilidad operacional y dashboard.
- **OpenAI** – Clasificación y análisis inteligente de incidencias.
- **Gmail** – Canal de entrada y respuesta.
- **Slack** – Canal de aprobación humana (HITL).
- **JSON** – Intercambio estructurado de información entre módulos.

---

## 🏗️ Arquitectura general

El ecosistema está compuesto por dos escenarios principales.

### Escenario 1 – Procesamiento de incidencias

`Gmail → Airtable → OpenAI → JSON → Airtable → Router`

A partir del Router se generan dos rutas:

### Ruta Normal

`Router → Gmail → Airtable → Ejecuciones`

Permite continuar automáticamente con las incidencias que no requieren intervención humana.

### Ruta Crítica – HITL

`Router → Airtable → Slack`

Las incidencias consideradas críticas o que requieren validación son enviadas a Slack antes de ejecutar la acción final.

---

### Escenario 2 – Human-in-the-Loop

Slack funciona como punto de decisión humana.

`Slack → Router`

Desde allí se procesan dos posibles decisiones:

**APROBAR**

`Slack → Airtable → Gmail → Airtable → Ejecuciones`

**RECHAZAR**

`Slack → Airtable → Ejecuciones`

De esta manera, las operaciones que requieren supervisión no continúan automáticamente sin una decisión humana.

---

## 🗄️ Modelo de datos

La base **FinanceOps AI – Control Center** fue implementada en Airtable mediante tres tablas relacionadas.

### Incidencias

Almacena la información recibida desde Gmail y los resultados generados por IA.

Incluye información como:

- Identificador de incidencia.
- Fecha de recepción.
- Solicitante.
- Asunto y mensaje.
- Categoría.
- Prioridad.
- Riesgo.
- Monto y moneda.
- Resumen generado por IA.
- Acción recomendada.
- Respuesta propuesta.
- Nivel de confianza.
- Estado.
- Indicador HITL.
- Identificadores de Gmail.
- Fecha y tiempo de resolución.
- Resultado final.
- Relaciones con ejecuciones y errores.

### Ejecuciones

Registra la trazabilidad operacional de las automatizaciones:

- Identificador de ejecución.
- Incidencia relacionada.
- Fecha de inicio y finalización.
- Modelo de IA.
- Resultado de ejecución.
- Activación de HITL.
- Tokens de entrada y salida.
- Costo estimado.
- Errores relacionados.

### Errores

Permite registrar fallas producidas durante la ejecución:

- Identificador de error.
- Incidencia relacionada.
- Ejecución relacionada.
- Fecha del error.
- Módulo.
- Tipo y código de error.
- Mensaje.
- Acción aplicada.
- Reintento.
- Estado de resolución.

---

## 🤖 Procesamiento mediante IA

El módulo de OpenAI analiza dinámicamente la información recibida desde Gmail.

La salida se estructura mediante JSON e incluye:

- `categoria_ia`
- `prioridad_ia`
- `riesgo_ia`
- `monto`
- `moneda`
- `resumen_ia`
- `accion_recomendada`
- `respuesta_propuesta`
- `confianza_ia`
- `requiere_hitl`

El prompt utiliza variables dinámicas provenientes de los módulos anteriores.

Cuando faltan datos esenciales, el sistema está diseñado para evitar inventar información y derivar el caso a revisión cuando corresponde.

---

## 👤 Human-in-the-Loop (HITL)

Las incidencias que requieren supervisión humana son enviadas a un canal privado de Slack.

El aprobador puede indicar:

`APROBAR <Incident_ID>`

o

`RECHAZAR <Incident_ID>`

Make procesa posteriormente la decisión, localiza la incidencia correspondiente y ejecuta la ruta definida.

Una aprobación permite continuar el procesamiento y generar la respuesta correspondiente. Un rechazo actualiza el estado y registra la ejecución sin continuar con el envío automático.

Este mecanismo evita que acciones consideradas críticas continúen sin intervención humana.

---

## 🛡️ Seguridad y resiliencia

El prototipo incorpora diferentes controles:

- Filtro anti-loop para evitar reprocesamiento de correos generados por la propia automatización.
- Variables dinámicas entre módulos.
- Salida estructurada mediante JSON.
- Separación entre rutas automáticas y críticas.
- Human-in-the-Loop antes de acciones críticas.
- Registro de errores en Airtable.
- Registro de ejecuciones.
- Relaciones entre incidencias, ejecuciones y errores.
- Conservación del Gmail Thread ID para mantener la trazabilidad de las respuestas.
- Minimización de información sensible en los archivos destinados al repositorio público.

También se implementó una ruta específica de manejo de errores para fallas producidas durante el procesamiento mediante IA, permitiendo registrar el evento y mantener trazabilidad sobre la incidencia afectada.

---

## 🧪 Pruebas realizadas

Durante el desarrollo se realizaron pruebas funcionales sobre los diferentes componentes del ecosistema.

Entre ellas:

- Recepción de incidencias desde Gmail.
- Registro automático en Airtable.
- Procesamiento mediante OpenAI.
- Parseo de respuestas JSON.
- Evaluación de rutas mediante Router.
- Derivación de casos hacia HITL.
- Procesamiento de decisiones desde Slack.
- Manejo controlado de errores de API.
- Registro de errores en Airtable.
- Registro de ejecuciones.
- Validación del filtro anti-loop.
- Validación de variables dinámicas y relaciones entre registros.

Las pruebas generaron registros utilizados posteriormente como evidencia y como fuente para los indicadores del dashboard.

---

## 📊 Dashboard y KPIs

Se desarrolló un dashboard en Airtable para facilitar el monitoreo del ecosistema.

El dashboard incluye indicadores correspondientes a incidencias, ejecuciones y errores, entre ellos:

- Total de incidencias.
- Incidencias resueltas.
- Incidencias que requieren HITL.
- Distribución de incidencias por estado.
- Total de ejecuciones.
- Ejecuciones exitosas.
- Total de errores.
- Tasa de error (**Error Rate**).
- Comparación entre ejecuciones exitosas y ejecuciones con error.

En la evidencia final registrada para la tabla de ejecuciones se observan:

- **10 ejecuciones totales.**
- **5 ejecuciones exitosas.**
- **2 ejecuciones con resultado ERROR.**
- **Error Rate: 20%.**

La tasa de error se calcula como:

`(Ejecuciones ERROR / Total de ejecuciones) × 100`

Por lo tanto:

`(2 / 10) × 100 = 20%`

---

## 🔗 Airtable – Vistas públicas de solo lectura

Como evidencia de la persistencia de datos y trazabilidad del ecosistema, se encuentran disponibles las siguientes vistas públicas de solo lectura:

### Incidencias

https://airtable.com/appUQIwwn06Mzt8Nl/shrM48dYBoCiNRLkR

Permite consultar los registros de incidencias procesadas y los diferentes estados generados durante las pruebas.

### Ejecuciones

https://airtable.com/appUQIwwn06Mzt8Nl/shrg8w2DkzXVf0erK

Permite consultar la trazabilidad de las ejecuciones, incluyendo resultados, activación de HITL y registros utilizados para los KPIs.

### Errores

https://airtable.com/appUQIwwn06Mzt8Nl/shrEPWJHOuPZKULqh

Permite consultar los errores registrados durante las pruebas de resiliencia y manejo controlado de fallas.

> Las vistas compartidas son de solo lectura y se utilizan como evidencia del funcionamiento y persistencia de datos del prototipo.

---

## 📁 Contenido del repositorio

Este repositorio reúne la documentación y las evidencias técnicas del proyecto **FinanceOps AI – Control Center**.

Incluye:

- Informe final del proyecto en PDF.
- Blueprints JSON de los escenarios desarrollados en Make.
- Evidencias visuales de la automatización.
- Documentación de arquitectura y modelo de datos.
- Evidencias del dashboard y KPIs.
- Enlaces a vistas públicas de solo lectura de Airtable.

> Los archivos publicados se preparan aplicando criterios de minimización de datos y no deben contener claves API, tokens de acceso, contraseñas ni otras credenciales privadas.

---

## 📌 Estado del proyecto

**Prototipo funcional desarrollado como Proyecto Final de IA Automation.**

Se implementaron los componentes principales del ecosistema:

**Gmail → Make → Airtable → OpenAI → JSON → Router → Gmail / Slack → Airtable**

La solución incorpora persistencia de datos, procesamiento mediante IA, separación de rutas según el nivel de intervención requerido, Human-in-the-Loop, manejo de errores, trazabilidad de ejecuciones y monitoreo mediante KPIs.

El proyecto tiene carácter de prototipo académico y puede continuar evolucionando mediante nuevas pruebas, optimización de costos, ampliación de métricas y fortalecimiento de los mecanismos de resiliencia.

---

## 👩‍💻 Autora

**Joselin Veitia**

Proyecto Final – IA Automation  
Coderhouse – 2026
