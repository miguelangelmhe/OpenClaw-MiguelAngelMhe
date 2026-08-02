# SKILLS_DESIGN.md

## Skill 1: Revisar Pull Requests Pendientes

### 1. ¿Qué problema resuelve?

Resuelve un problema muy concreto: los PR pendientes de revisión se esconden bien cuando uno está estudiando, programando o saltando entre tareas. Miguel Ángel no debería tener que abrir GitHub solo para descubrir si le espera trabajo de revisión. Esta skill detecta ese trabajo y lo traduce en dos cosas útiles: un aviso inmediato y un hueco reservado para atenderlo al día siguiente.

### 2. ¿Por qué debe ser una skill y no un prompt?

Porque no es solo texto. Requiere una secuencia estable de decisiones y acciones sobre varios servicios: consultar GitHub, interpretar el resultado, decidir si existe trabajo pendiente, enviar Telegram solo si corresponde y crear un evento en Calendar solo en el caso correcto. Si esto se deja como prompt abierto, el comportamiento se vuelve inconsistente y es fácil que el agente termine creando recordatorios aunque no haya nada, o avisando sin calendarizar. La skill encapsula la lógica, limita el alcance y deja claro qué automatiza.

### 3. ¿Qué decisiones toma automáticamente y cuáles pregunta al usuario?

Decisiones automáticas:

- comprobar si existen PR pendientes;
- contar cuántos hay;
- decidir si procede o no crear un evento;
- fijar el evento para las 09:00 del día siguiente;
- redactar un aviso breve con la voz de Calvo Malvado.

Lo que pregunta al usuario:

- solo pide confirmación si la skill no fue invocada explícitamente;
- pide aclaración si no puede determinar qué repositorios o PR son relevantes;
- pide intervención si GitHub, Telegram o Calendar no tienen acceso funcional.

## Skill 2: Forja del Plan de Estudio Malvado

### 1. ¿Qué problema resuelve?

Resuelve el salto incómodo entre "sé lo que debería estudiar" y "tengo un plan real para hacerlo". En formación técnica, especialmente en IA y programación, el atasco no suele ser ignorancia total sino mala estructuración: objetivos vagos, demasiados frentes y tareas mal cortadas. Esta skill convierte un objetivo útil en un documento breve y un conjunto pequeño de tareas accionables para que Miguel Ángel no malgaste energía decidiendo cada próximo paso.

### 2. ¿Por qué debe ser una skill y no un prompt?

Porque combina criterio, persistencia y coordinación entre herramientas. No basta con redactar un plan bonito: hay que decidir cuántas tareas crear, cómo descomponer el objetivo sin inflarlo, qué dejar en Google Docs y qué bajar a Google Tasks, y cuándo vale la pena avisar por Telegram. Una skill permite fijar estas reglas de calidad para que el resultado no dependa del humor del modelo en cada ejecución.

### 3. ¿Qué decisiones toma automáticamente y cuáles pregunta al usuario?

Decisiones automáticas:

- clasificar si el objetivo es de estudio, implementación, debugging o entrega;
- elegir la estructura del documento;
- partir el objetivo en 3 a 5 tareas cuando el contexto lo permita;
- decidir el orden de ejecución recomendado;
- redactar el plan y las tareas con tono directo y útil.

Lo que pregunta al usuario:

- solicita fecha límite si afecta al plan y no fue dada;
- pide aclaración si el objetivo es demasiado ambiguo para generar tareas reales;
- pregunta si debe enviar Telegram final cuando esa preferencia no esté incluida en la invocación.

## Criterio común de diseño

Ambas skills comparten una idea central: automatizar decisiones pequeñas pero repetibles sin regalar autonomía peligrosa. Calvo Malvado no improvisa acciones externas fuera del guion de la skill. Cuando actúa, actúa porque el usuario lo autorizó al invocarla y porque la lógica ya dejó claro qué saboteo útil toca ejecutar.