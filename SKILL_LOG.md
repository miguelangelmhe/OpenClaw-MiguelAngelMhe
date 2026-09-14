# SKILL_LOG.md — Habilidades 4Geeks Academy

---

## Skill 1: Autenticar (auth-check)

### 📋 Ficha

| Campo | Valor |
|---|---|
| **Nombre** | `4geeks-auth-check` |
| **Script** | `bin/auth-check.sh` |
| **Versión** | 1.0.0 |
| **Creada** | 2026-09-10 |
| **Propósito** | Verificar que el `4g_tok` del estudiante es válido y su sesión sigue activa |

### 🧠 Necesidad original

> "Quiero que puedas comprobar si mi cuenta de 4Geeks está correctamente autenticada y si mi sesión de estudiante sigue activa."

El usuario necesitaba una skill que hiciera exclusivamente la validación de autenticación, sin mezclar proyectos, tareas ni progreso.

### 💬 Conversación que la originó

El usuario revisó la rúbrica completa de la tarea y decidió que las 4 skills obligatorias debían construirse **una por una mediante conversación**. La Skill 1 (Autenticar) fue la primera.

Se partió del `4g_tok` ya almacenado de forma segura en `/root/.config/4geeks/4g_tok` (descubierto y configurado en una conversación previa de ingeniería inversa de la API).

### 🔧 Endpoints utilizados

| Endpoint | Método | Propósito |
|---|---|---|
| `/v1/auth/token/{4g_tok}` | GET | Intercambiar el `4g_tok` cookie por un login token de sesión |

**Headers**: Ninguno adicional — el `4g_tok` se pasa como path parameter.

### ⚙️ Implementación

**Flujo:**
```
/root/.config/4geeks/4g_tok (archivo, chmod 600)
    ↓
GET /v1/auth/token/{4g_tok}
    ↓
Valida: HTTP 200 + token generado + user_id
    ↓
Calcula tiempo restante hasta expires_at
    ↓
Muestra: ✅ Autenticación correcta / ❌ Error / ❌ Expirado
```

**Modos de salida:**
- `--telegram`: Markdown optimizado para Telegram
- `--json`: JSON estructurado con `authenticated`, `user_id`, `session_expires_at`, `session_remaining_hours`
- (sin flag): Texto legible

**Seguridad:**
- El `4g_tok` nunca aparece en la salida
- El login token temporal se limpia tras usarlo (`rm -f /tmp/4g_auth_check.json`)
- Solo lectura: nunca se envía POST/PUT/DELETE

### ✅ Prueba real (2026-09-10)

```
$ ./auth-check.sh --telegram

✅ *Autenticación correcta*

Usuario ID: `21550`
Sesión activa hasta: `2026-09-14T16:21:26`
⏳ Tiempo restante: ~4 días

_Skill: 4geeks-auth-check 🤖_
```

**Lo que demuestra:**
1. `HTTP 200` en `/v1/auth/token/{4g_tok}` → el `4g_tok` almacenado es válido
2. Se generó un login token → el intercambio funciona
3. `user_id: 21550` → el token está asociado a la cuenta del usuario
4. `expires_at: 2026-09-14` → sesión activa con 4 días restantes (~98h)

---

## Skill 2: Obtener mis proyectos (get-projects)

### 📋 Ficha

| Campo | Valor |
|---|---|
| **Nombre** | `4geeks-get-projects` |
| **Script** | `bin/get-projects.sh` |
| **Versión** | 1.0.0 |
| **Creada** | 2026-09-10 |
| **Propósito** | Recuperar los proyectos asignados (PROJECT type) con su estado: pendiente, entregado o calificado |

### 🧠 Necesidad original

> "Quiero que puedas consultar mi cuenta de 4Geeks y decirme cuáles son los proyectos que tengo asignados actualmente, indicando para cada uno si está pendiente, entregado o calificado."

El usuario necesitaba una skill independiente para consultar únicamente proyectos, sin mezclar trabajo pendiente general ni resumen de progreso.

### 💬 Conversación que la originó

Tras completar la Skill 1 (Autenticar), el usuario indicó que pasáramos a la Skill 2. Se realizó una **consulta exploratoria primero** para entender la estructura de datos de los proyectos en la API, identificando:

- **51 proyectos** (task_type = PROJECT) en total
- **Estados posibles**: 
  - `PENDIENTE`: `task_status=PENDING`, no entregado
  - `ENTREGADO`: entregado (`delivered_at` presente) pero no revisado
  - `CALIFICADO`: `revision_status=APPROVED` con `reviewed_at` presente

Se verificó que todos los proyectos calificados tienen `revision_status: APPROVED` y fecha de revisión, mientras que los pendientes no tienen fecha de entrega.

### 🔧 Endpoints utilizados

| Endpoint | Método | Propósito |
|---|---|---|
| `/v1/auth/token/{4g_tok}` | GET | Obtener login token de sesión (reutiliza la lógica de Skill 1) |
| `/v1/assignment/user/me/task` | GET | Listar todas las tasks del usuario autenticado |

**Headers de autenticación**: `Authorization: Token <login_token>`

### ⚙️ Implementación

**Flujo:**
```
/root/.config/4geeks/4g_tok
    ↓
GET /v1/auth/token/{4g_tok} → login token
    ↓
GET /v1/assignment/user/me/task (Authorization: Token <login_token>)
    ↓
Filtrar task_type = PROJECT (51 de 219 tasks totales)
    ↓
Clasificar cada proyecto:
  - PENDIENTE: no entregado
  - ENTREGADO: entregado, pendiente de revisión
  - CALIFICADO: aprobado con revisión
    ↓
Ordenar: PENDIENTES > ENTREGADOS > CALIFICADOS
Agrupar por cohort dentro de cada estado
    ↓
Mostrar por estado
```

**Criterios de clasificación (extraídos de los datos reales):**
- `PENDIENTE`: `task_status != 'DONE'` y sin `delivered_at`
- `ENTREGADO`: `task_status = 'DONE'` con `delivered_at` pero sin `reviewed_at`
- `CALIFICADO`: `task_status = 'DONE'` y `revision_status = 'APPROVED'` con `reviewed_at`

**Modos de salida:**
- `--telegram`: Markdown optimizado para Telegram
- `--json`: JSON estructurado con totales y lista detallada
- (sin flag): Texto legible con tabla de proyectos

### ✅ Prueba real (2026-09-10)

```
$ ./get-projects.sh --telegram

📦 *Mis Proyectos — 4Geeks*
Total: 51 | 🔴 27 pendientes | 🟡 0 entregados | ✅ 24 calificados

🔴 *PENDIENTES (27):*
• AI basic Inventory Agent Loop
  _Cohort: Backend development with Coding Agents_
• Backend Architecture Proposal
  _Cohort: Backend development with Coding Agents_
• Background Processes
  _Cohort: Asynchronous processing and offloading_
  ... (24 más)

✅ *CALIFICADOS (24):*
• A simple Dashboard with Tailwind CSS
  _Cohort: Web UI fundamentals with Tailwind_
  _Calificado: 14/06/2026_
• AgentHub Admin Panel Specs and Prompt-Driven Prototype
  _Cohort: Frontend development with Coding Agents_
  _Calificado: 13/07/2026_
  ... (22 más)
```

**Lo que demuestra:**
1. Se autenticó correctamente (reutilizando lógica de Skill 1)
2. Se recuperaron 219 tasks totales del endpoint
3. Se filtraron 51 proyectos (PROJECT type)
4. Se clasificaron correctamente en 3 estados: 27 pendientes, 0 entregados, 24 calificados
5. Cada proyecto muestra su cohort de origen y fecha de calificación cuando aplica
6. Sin entregados pendientes de revisión (todos los `DONE` están aprobados)

---

## Skill 3: Obtener trabajo pendiente (pending-work)

### 📋 Ficha

| Campo | Valor |
|---|---|
| **Nombre** | `4geeks-pending-work` |
| **Script** | `bin/pending-work.sh` |
| **Versión** | 1.0.0 |
| **Creada** | 2026-09-10 |
| **Propósito** | Mostrar todo el trabajo pendiente del estudiante: proyectos, ejercicios y lecciones no completados, organizados por tipo y cohort |

### 🧠 Necesidad original

> "Quiero que puedas decirme específicamente qué trabajo me falta completar en 4Geeks, para saber qué debería hacer a continuación."

El usuario necesitaba una skill que respondiera «¿qué toca ahora?» sin mezclar proyectos ya hechos, sin resumen general de progreso. Solo lo pendiente.

### 💬 Conversación que la originó

Tras completar Skills 1 y 2, se realizó una **consulta exploratoria** de todas las tasks pendientes para entender la estructura. Se descubrió que:

- **152 tasks pendientes** en total (de 219)
- **Tipos**: 27 PROYECTOS, 118 EJERCICIOS, 7 LECCIONES
- Distribuidas en **20 cohorts diferentes**

Se decidió que el modo Telegram debía ser compacto: proyectos en lista completa (27 cabe), ejercicios resumidos por cohort (118 es demasiado para lista completa), lecciones en lista (solo 7).

### 🔧 Endpoints utilizados

| Endpoint | Método | Propósito |
|---|---|---|
| `/v1/auth/token/{4g_tok}` | GET | Obtener login token de sesión (reutiliza Skill 1) |
| `/v1/assignment/user/me/task` | GET | Listar todas las tasks del usuario y filtrar PENDING |

**Headers de autenticación**: `Authorization: Token <login_token>`

**Sin filtro de cohort** — a diferencia de skills anteriores, esta muestra absolutamente todo el trabajo pendiente a nivel de cuenta.

### ⚙️ Implementación

**Flujo:**
```
4g_tok (archivo seguro, chmod 600)
    ↓
GET /v1/auth/token/{4g_tok} → login token
    ↓
GET /v1/assignment/user/me/task (Authorization: Token)
    ↓
Filtrar task_status = PENDING (152 de 219)
    ↓
Clasificar: PROYECTOS / EJERCICIOS / LECCIONES
    ↓
Agrupar por cohort dentro de cada tipo
    ↓
Calcular "siguiente": lo más relevante para hacer ahora
```

**Modo Telegram compacto:**
- Proyectos → lista completa (27 items)
- Ejercicios → resumen por cohort con contador ("Frontend development: 15")
- Lecciones → lista completa (7 items)
- `💡 Siguiente:` → tarea prioritaria

**Modo texto detallado:**
- Proyectos y lecciones: lista completa con cohort
- Ejercicios: hasta 3 ejemplos por cohort con "… y X más"

### ✅ Prueba real (2026-09-10)

```
$ ./pending-work.sh --telegram

📋 *Trabajo Pendiente — 4Geeks*
152 pendientes: 🚀 27 proyectos | 📝 118 ejercicios | 📖 7 lecciones

🚀 *PROYECTOS (27):*
• My 4Geeks Assistant — Teaching OpenClaw to Track Your Progress [02/08/2026]
• Support Agent with LangGraph — Part 1 of 2: Migration and Agent Flow
• Support Agent with LangGraph — Part 2 of 2: Tools Outside the RAG
• Milestone 9 — Agentic Workflow Generation (Part 1 of 3): RFP Intake & Routing
• Company's Telemetry plan design
• Frontend Performance Audit
• ... (22 más)

📝 *EJERCICIOS (118):*
• Advanced personal assistants with Openclaw: 4
• Agentic Engineering: 3
• Agentic Workflows: 5
• Application telemetry: 3
• Architecture optimization: 2
• Asynchronous processing and offloading: 3
• Authentication in web applications: 5
• Backend development with Coding Agents: 11
• Coding Fundamentals with Typescript: 13
• Coding fundamentals with Python: 5
• ... (11 cohorts más)

📖 *LECCIONES (7):*
• Logical conditions in Python explained
• Learning to program with Python
• Introduction to dictionaries in Python
• Sorting and Searching Algorithms in Python
• Working with Lists in Python
• Working with Functions in Python
• Global state with the Context API

💡 *Siguiente:* Background Processes (Asynchronous processing and offloading)
```

**Lo que demuestra:**
1. Autenticación exitosa (reutilizando Skill 1)
2. Se recuperaron 219 tasks totales
3. Se filtraron correctamente 152 pendientes
4. Clasificación por tipo: 27 proyectos, 118 ejercicios, 7 lecciones
5. Agrupación por cohort en 20 cohorts diferentes
6. Cálculo de "siguiente" tarea prioritaria
7. Modo Telegram compacto cabe en un solo mensaje sin saturar

---

---

## Skill 4: Progreso del curso AI Engineer (course-progress)

### 📋 Ficha

| Campo | Valor |
|---|---|
| **Nombre** | `4geeks-course-progress` |
| **Script principal** | `bin/course-progress.sh` |
| **Relación con `progress-summary.sh`** | `course-progress.sh` es la implementación específica de esta Skill 4. `progress-summary.sh` queda como script auxiliar de resumen general y no se presenta como una skill adicional. |
| **Propósito** | Consultar únicamente el progreso del programa AI Engineer (`spain-aie-pt-3`), separando tareas pendientes y completadas. |

### 🧠 Prompt exacto que la originó

> Sí. Pasemos a la Skill 4 — Obtener resumen de progreso.
> Necesidad en lenguaje natural:
> "Quiero que puedas darme un resumen de cuánto he avanzado en mi curso de 4Geeks, incluyendo de forma clara lo que he completado y lo que me queda."
> Construye esta skill mediante conversación.
> Debe centrarse únicamente en proporcionar un resumen general de mi progreso en el curso AI Engineer.
> Quiero que, cuando los datos de la API lo permitan, incluya cantidades o porcentajes de trabajo completado y pendiente.
> No mezcles todavía deadlines, detalles de proyectos ni otras funcionalidades.
> Usa la autenticación ya creada y el 4g_tok almacenado de forma segura en el VPS.
> Cuando termines:
> 1. Haz una prueba real con mi cuenta.
> 2. Muéstrame el resumen obtenido.
> 3. Indica los endpoints utilizados.
> 4. Actualiza /root/skills/4geeks/SKILL_[LOG.md](http://log.md/) con el prompt exacto, descripción, endpoints y resultado de prueba.
> 5. No guardes credenciales en el log.

### 🔧 Qué hace exactamente

Lee de forma local la credencial ya configurada, obtiene una sesión de API, consulta las tareas del usuario y filtra exclusivamente el cohort `spain-aie-pt-3` (AI Engineer). Devuelve solo el total del cohort y sus tareas pendientes/completadas. No consulta deadlines, no muestra el detalle de un proyecto y no modifica datos.

### 🔧 Endpoints utilizados

| Endpoint | Método | Propósito |
|---|---|---|
| `/v1/auth/token/{4g_tok}` | GET | Obtener el login token de sesión. |
| `/v1/assignment/user/me/task` | GET | Obtener las tareas del usuario para filtrar el programa AI Engineer. |

### ✅ Prueba real (2026-09-14)

Comando ejecutado: `./course-progress.sh --telegram`

Resultado real:

```
📊 AI Engineer - Progreso
Total: 5 tasks

🔴 PENDIENTES (2):
1. 🚀 PROYECTO - Showcase your friend's artist talent with a website
2. 📝 EJERCICIO - Token Efficiency with Coding Agents: Mastering Auto Mode

✅ COMPLETADAS (3):
1. 📝 EJERCICIO - HTML Fundamentals: Building Web Structure
2. 📝 EJERCICIO - CSS Mastery from Scratch
3. 📝 EJERCICIO - SEO and GEO - Making Your Website Discoverable
```

La prueba demuestra que el script limita el resultado al cohort AI Engineer y separa correctamente 2 tareas pendientes y 3 completadas.

---

## Skill 5: Próximas fechas límite (deadlines)

### 📋 Ficha

| Campo | Valor |
|---|---|
| **Nombre** | `4geeks-deadlines` |
| **Script** | `bin/deadlines.sh` |
| **Propósito** | Consultar únicamente tareas pendientes ordenadas por urgencia/próxima fecha estimada. |

### 🧠 Prompt exacto que la originó

> Sí. Quiero crear una skill extendida para consultar mis próximos deadlines.
> Necesidad en lenguaje natural:
> "Quiero que puedas decirme qué tareas o proyectos de 4Geeks tienen una fecha límite próxima, para saber qué debería priorizar."
> Construye esta skill mediante conversación.
> Debe centrarse únicamente en consultar fechas límite de mis tareas o proyectos.
> No debe modificar nada de mi cuenta ni mezclar otras funcionalidades.
> Usa la autenticación existente y el 4g_tok almacenado de forma segura en el VPS.
> Cuando termines:
> 1. Haz una prueba real.
> 2. Muéstrame mis próximos deadlines.
> 3. Indica los endpoints utilizados.
> 4. Actualiza /root/skills/4geeks/SKILL_[LOG.md](http://log.md/) con el prompt exacto, descripción, endpoints y resultado de prueba.
> 5. No guardes ningún token ni credencial.

### 🔧 Qué hace exactamente

Consulta las tareas pendientes y las ordena por urgencia usando `opened_at` o, si no existe, `created_at`, porque la API no expone un campo `due_at` explícito. Presenta las recién abiertas, los proyectos pendientes y un top de prioridades. No muestra el resumen de progreso del curso ni el detalle de un proyecto concreto y no modifica datos.

### 🔧 Endpoints utilizados

| Endpoint | Método | Propósito |
|---|---|---|
| `/v1/auth/token/{4g_tok}` | GET | Obtener el login token de sesión. |
| `/v1/assignment/user/me/task` | GET | Consultar tareas y sus fechas disponibles para calcular la prioridad. |

**Limitación verificada:** no se registra ni se afirma un `due_at` real; la prioridad se calcula con las fechas disponibles (`opened_at`/`created_at`).

### ✅ Prueba real (2026-09-14)

Comando ejecutado: `./deadlines.sh --telegram`

Resultado real resumido:

```
📅 Próximos Deadlines — 4Geeks
Prioridad desde opened_at (API no expone due_at).
152 pendientes: 27 proyectos, 118 ejercicios, 7 lecciones
Recién abiertas (≤3d): Measuring Frontend Performance: Core Web Vitals
Top 10: mostró 10 tareas ordenadas por urgencia
```

La prueba demuestra que la skill consulta únicamente las tareas pendientes y devuelve prioridades/fechas disponibles, sin presentar una fecha límite inventada.

---

## Skill 6: Detalle de un proyecto concreto (project-detail)

### 📋 Ficha

| Campo | Valor |
|---|---|
| **Nombre** | `4geeks-project-detail` |
| **Script** | `bin/project-detail.sh` |
| **Propósito** | Obtener únicamente la información detallada del proyecto que se indique. |

### 🧠 Prompt exacto que la originó

> Sí. Quiero crear una segunda skill extendida para consultar el detalle de un proyecto.
> Necesidad en lenguaje natural:
> "Quiero poder preguntarte por un proyecto concreto de 4Geeks y que me muestres su descripción, requisitos y estado actual."
> Construye esta skill mediante conversación.
> Debe centrarse únicamente en obtener los detalles de un proyecto que yo indique.
> No debe modificar el proyecto ni entregar/subir nada en mi nombre.
> Usa la autenticación existente y el 4g_tok almacenado de forma segura en el VPS.
> Cuando termines:
> 1. Haz una prueba real con uno de mis proyectos.
> 2. Muéstrame la información obtenida.
> 3. Indica los endpoints utilizados.
> 4. Actualiza /root/skills/4geeks/SKILL_[LOG.md](http://log.md/) con el prompt exacto, descripción, endpoints y resultado de prueba.
> 5. No guardes ningún token ni credencial en el log.

### 🔧 Qué hace exactamente

Recibe el título o slug de un único proyecto, localiza ese proyecto entre las tareas del usuario, solicita su detalle y muestra descripción/README disponible, estado, fechas, cohort, enlace y feedback cuando existen. No lista proyectos como capacidad principal, no consulta deadlines y no entrega, modifica ni sube nada.

### 🔧 Endpoints utilizados

| Endpoint / recurso | Método | Propósito |
|---|---|---|
| `/v1/auth/token/{4g_tok}` | GET | Obtener el login token de sesión. |
| `/v1/assignment/user/me/task` | GET | Localizar el proyecto concreto por título o slug. |
| `/v1/assignment/task/{id}` | GET | Obtener el detalle ampliado del proyecto seleccionado. |
| `https://raw.githubusercontent.com/{owner}/{repo}/{branch}/{README path}` | GET | Leer el README público asociado cuando existe; es un recurso auxiliar de GitHub, no un endpoint de la API de 4Geeks. |

### ✅ Prueba real (2026-09-14)

Comando ejecutado: `./project-detail.sh --telegram "A simple Dashboard with Tailwind CSS"`

Resultado real:

```
📋 A simple Dashboard with Tailwind CSS
📌 ✅ CALIFICADO | 📂 Web UI fundamentals with Tailwind
🏷️ simple-dashboard-tailwind-css | 🆔 932520
Creado: 2026-06-09 | Abierto: 2026-06-13
✅ Entregado: 2026-06-13
📋 Revisado: 2026-06-14
Feedback: "Objetivo alcanzado!"
README: disponible (1621 chars)
```

La prueba demuestra que la skill obtiene el detalle de un proyecto concreto, incluyendo estado, fechas, feedback y README disponible, sin ejecutar ninguna operación de modificación.

---

## Resumen de skills creadas

| # | Skill | Script | Endpoint(s) principal(es) | Prompt | Descripción | Prueba real |
|---|---|---|---|---|---|---|
| 1 | Autenticar | `bin/auth-check.sh` | `/v1/auth/token/{4g_tok}` | ✅ | ✅ | ✅ |
| 2 | Obtener proyectos | `bin/get-projects.sh` | `/v1/auth/token/{4g_tok}`, `/v1/assignment/user/me/task` | ✅ | ✅ | ✅ |
| 3 | Trabajo pendiente | `bin/pending-work.sh` | `/v1/auth/token/{4g_tok}`, `/v1/assignment/user/me/task` | ✅ | ✅ | ✅ |
| 4 | Progreso AI Engineer | `bin/course-progress.sh` | `/v1/auth/token/{4g_tok}`, `/v1/assignment/user/me/task` | ✅ | ✅ | ✅ |
| 5 | Próximos deadlines | `bin/deadlines.sh` | `/v1/auth/token/{4g_tok}`, `/v1/assignment/user/me/task` | ✅ | ✅ | ✅ |
| 6 | Detalle de proyecto | `bin/project-detail.sh` | `/v1/auth/token/{4g_tok}`, `/v1/assignment/user/me/task`, `/v1/assignment/task/{id}` | ✅ | ✅ | ✅ |

`bin/progress-summary.sh` permanece como script auxiliar de resumen general y no se cuenta como una séptima skill.

---

*Documentado por Calvo Malvado 😈 — actualizado 2026-09-14*
