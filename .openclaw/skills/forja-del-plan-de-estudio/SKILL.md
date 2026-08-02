---
name: forja-del-plan-de-estudio
description: 'Convertir un objetivo de estudio o programación en un plan breve en Google Docs y entre 3 y 5 tareas accionables en Google Tasks. Usar cuando Miguel Ángel necesite organizar una práctica de IA, un proyecto o una sesión de estudio sin inflar el trabajo con burocracia.'
argument-hint: 'Indica el objetivo principal y, si existe, la fecha límite o entrega'
user-invocable: true
---

# Forja del Plan de Estudio

Usa esta skill cuando Miguel Ángel tenga un objetivo técnico claro pero todavía no lo haya convertido en un plan decente. La misión es sabotear la improvisación: dejar un documento útil en Google Docs y una secuencia corta de tareas reales en Google Tasks.

## Cuándo usarla

- Para organizar una práctica de 4Geeks Academy.
- Para preparar un bloque de estudio de IA, agentes, debugging o programación.
- Para dividir un objetivo difuso en pocos pasos concretos.

## Antes de actuar

1. Confirma que el usuario autorizó crear contenido externo.
2. Si falta un dato crítico, pregúntalo antes de generar basura con formato. Los datos críticos son: objetivo principal, alcance mínimo y fecha límite si afecta a la prioridad.
3. Explica que se crearán dos artefactos: un documento en Google Docs y entre 3 y 5 tareas en Google Tasks.

## Procedimiento

1. Leer el objetivo principal y clasificarlo como estudio, implementación, debugging o entrega.
2. Definir el resultado útil que Miguel Ángel debería conseguir.
3. Redactar en Google Docs, en Markdown y en castellano, un plan breve con estas secciones:
   - objetivo;
   - criterio de éxito;
   - riesgos técnicos previsibles;
   - orden recomendado de trabajo;
   - advertencia sobre qué no complicar inútilmente.
4. Crear entre 3 y 5 tareas en Google Tasks.
5. Cada tarea debe empezar con un verbo, ser accionable y poder ejecutarse sin releer toda la conversación.
6. Responder al usuario con un resumen corto de lo creado e incluir el enlace al documento si la herramienta lo devuelve.

## Tono del contenido

El documento y el cierre deben sonar a Calvo Malvado: criterio técnico, poca paciencia para el adorno y un remate con filo cuando encaje. Ejemplos válidos:

- "Plan forjado. Ahora toca conquistar el problema, no contemplarlo."
- "Te dejé tareas decentes. La improvisación ha sido oficialmente saboteada."

## Guardarraíles

- No crear tareas si el objetivo sigue siendo ambiguo.
- No superar 5 tareas salvo que el usuario lo pida explícitamente.
- No inventar fechas límite ni requisitos.
- No crear eventos de calendario desde esta skill.
- No enviar Telegram salvo que el usuario lo pida aparte.

## Resultado esperado

- Un Google Doc en Markdown con un plan breve y útil.
- Entre 3 y 5 tareas accionables en Google Tasks.
- Un resultado verificable en servicios conectados mediante Composio.