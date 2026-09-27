# FinanceOps AI – Control Center

## Ecosistema Inteligente para la Gestión Automatizada de Incidencias Financieras con IA

Proyecto desarrollado como entrega final del curso **IA Automation – Coderhouse**.

**FinanceOps AI – Control Center** es un prototipo de automatización orientado a la recepción, clasificación, análisis y gestión de incidencias financieras y operativas utilizando Inteligencia Artificial, automatización de procesos y control humano (Human-in-the-Loop).

---

## 🎯 Objetivo

Diseñar un ecosistema capaz de recibir incidencias financieras por correo electrónico, registrar la información en una base estructurada, analizar cada caso mediante IA y determinar automáticamente el tratamiento adecuado.

Según el nivel de riesgo y las características de la incidencia, el sistema puede:

- Gestionar una ruta automática.
- Solicitar aprobación humana mediante HITL.
- Registrar errores técnicos.
- Mantener trazabilidad de las ejecuciones.
- Responder al usuario conservando el hilo original del correo.

---

## 🛠️ Tecnologías utilizadas

- **Make** – Orquestación y automatización de procesos.
- **Airtable** – Base de datos y trazabilidad operacional.
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

**Ruta Normal**

`Router → Gmail → Airtable → Ejecuciones`

Permite continuar automáticamente con incidencias que no requieren intervención humana.

**Ruta Crítica – HITL**

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

La base **FinanceOps AI – Control Center** fue implementada en Airtable mediante tres tablas relacionadas:

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

### Ejecuciones

Registra la trazabilidad operacional de las automatizaciones:

- Incidencia relacionada.
- Fecha de inicio y finalización.
- Modelo de IA.
- Resultado de ejecución.
- Activación de HITL.
- Tokens y costo estimado.
- Errores relacionados.

### Errores

Permite registrar fallas producidas durante la ejecución:

- Incidencia relacionada.
- Fecha del error.
- Módulo.
- Tipo de error.
- Mensaje.
- Acción aplicada.
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

Cuando faltan datos esenciales, el sistema está diseñado para evitar inventar información y derivar el caso a revisión cuando corresponde.

---

## 👤 Human-in-the-Loop (HITL)

Las incidencias que requieren supervisión humana son enviadas a un canal privado de Slack.

El aprobador puede indicar:

`APROBAR <Incident_ID>`

o

`RECHAZAR <Incident_ID>`

Make procesa posteriormente la decisión y actualiza la incidencia correspondiente.

Este mecanismo evita que acciones consideradas críticas continúen sin intervención humana.

---

## 🛡️ Seguridad y resiliencia

El prototipo incorpora diferentes controles:

- Filtro anti-loop para evitar reprocesamiento de correos generados por la propia automatización.
- Variables dinámicas entre módulos.
- Salida estructurada mediante JSON.
- Separación entre rutas automáticas y críticas.
- Human-in-the-Loop.
- Registro de errores en Airtable.
- Registro de ejecuciones.
- Conservación del Gmail Thread ID para mantener la trazabilidad de las respuestas.

También se implementó una ruta específica de manejo de errores para fallas producidas durante el procesamiento mediante IA.

---

## 🧪 Pruebas realizadas

Durante el desarrollo se realizaron pruebas funcionales sobre diferentes componentes del ecosistema.

Entre ellas:

- Recepción de incidencias desde Gmail.
- Registro automático en Airtable.
- Procesamiento mediante OpenAI.
- Parseo de respuesta JSON.
- Evaluación de rutas mediante Router.
- Manejo controlado de errores de API.
- Registro de errores y ejecuciones en Airtable.

El proyecto se encuentra en etapa de prototipo funcional y validación final.

---

## 📁 Contenido del repositorio

Este repositorio contiene documentación y evidencias técnicas del proyecto:

- Informe del proyecto en PDF.
- Blueprints JSON de los escenarios desarrollados en Make.
- Evidencias visuales de la automatización.
- Documentación del modelo de datos.

> Los archivos publicados fueron preparados con criterios de minimización de datos y no deben contener claves API, tokens de acceso ni credenciales privadas.

---

## 📌 Estado del proyecto

**Prototipo funcional – etapa de validación final.**

La arquitectura principal, persistencia de datos, procesamiento mediante IA, HITL y manejo de errores fueron implementados.

Algunos componentes de validación, métricas y pruebas integrales permanecen como mejoras futuras del prototipo.

---

## 👩‍💻 Autora

**Joselin Veitia**

Proyecto Final – IA Automation  
Coderhouse – 2026
