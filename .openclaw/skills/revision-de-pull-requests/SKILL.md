---
name: revision-de-pull-requests
description: 'Consultar pull requests pendientes de revisión en GitHub y, si existen, avisar por Telegram y crear un evento en Google Calendar para revisarlos al día siguiente a las 09:00. Usar cuando Miguel Ángel quiera comprobar revisiones pendientes o lanzar su rutina de seguimiento de PR.'
argument-hint: 'Opcionalmente indica el repositorio o alcance a revisar si no está implícito en el contexto'
user-invocable: true
---

# Revisión de Pull Requests

Usa esta skill para vigilar si hay pull requests pendientes de revisión y convertir ese dato en una acción concreta. No revisa el contenido técnico del PR. Detecta trabajo pendiente, avisa y reserva el hueco para que Miguel Ángel pase la guadaña mañana por la mañana.

## Cuándo usarla

- Cuando Miguel Ángel pida revisar si tiene PR pendientes.
- Cuando quiera una comprobación operativa rápida al empezar el día.
- Cuando exista contexto suficiente para saber qué repositorio o conjunto de PR es relevante.

## Antes de actuar

1. Confirma que el usuario autorizó esta ejecución.
2. Si no está claro qué repositorio, organización o lista de PR debes consultar, pregunta antes de tocar GitHub.
3. Explica en una frase el alcance de la acción externa: comprobar GitHub y, si hay PR, avisar por Telegram y crear un evento en Calendar.

## Procedimiento

1. Consultar GitHub y localizar los pull requests pendientes de revisión para Miguel Ángel dentro del alcance indicado.
2. Contar cuántos PR pendientes existen.
3. Si el total es mayor que cero:
   - enviar un mensaje breve por Telegram indicando el número de PR pendientes con tono de Calvo Malvado;
   - crear un evento en Google Calendar para las 09:00 del día siguiente con el título exacto Revisar Pull Requests pendientes;
   - responder al usuario confirmando cuántos PR había, que el mensaje salió y que el evento quedó creado.
4. Si el total es cero:
   - no crear ningún evento;
   - no enviar mensajes adicionales fuera de lo necesario;
   - responder al usuario que no hay PR pendientes.

## Tono del mensaje

El texto debe ser corto, en castellano y reconocible como Calvo Malvado. Ejemplos válidos de tono:

- "Miguel Ángel, hay 2 PR pendientes. El repositorio reclama tributo. Te dejé la emboscada mañana a las 09:00."
- "No hay PR pendientes. Hoy los bugs se escondieron bien."

No repitas fórmulas si el contexto pide otra variante, pero mantén siempre el mismo filo: útil, breve y con malicia simpática.

## Guardarraíles

- No crear más de un evento por ejecución.
- No crear evento si no hay PR.
- No revisar ni modificar el contenido de los PR.
- No comentar, aprobar ni solicitar cambios en GitHub.
- No inventar repositorios ni destinatarios.

## Resultado esperado

- Si hay PR: resultado verificable en Telegram y Google Calendar.
- Si no hay PR: solo confirmación al usuario de que no hay trabajo pendiente.