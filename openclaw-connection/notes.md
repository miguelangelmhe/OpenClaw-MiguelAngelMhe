Notas de configuración OpenClaw
1. Configuración de Telegram
Problema

El bot mostraba:

OpenClaw: access not configured.

y generaba un código de pairing.

Solución

Se aprobó el acceso mediante:

openclaw pairing approve telegram CODIGO

Después el bot quedó conectado.

También se revisó la política DM:

Por seguridad se recomienda usar allowlist.
Para pruebas de evaluación se dejó acceso más abierto temporalmente.

2. Configuración del MCP de Composio
Problema

El primer intento de añadir Composio falló:

OpenClaw does not recognize option "--name"
Solución

Se utilizó la sintaxis correcta:

openclaw mcp add composio \
--transport streamable-http \
--url https://connect.composio.dev/mcp

3. Error de autenticación con Composio
Problema

El MCP respondía:

Authorization required
No Authorization: Bearer
Solución

Se añadió la API key de Composio mediante header:

--header "Authorization=Bearer API_KEY"

Después:

openclaw mcp probe composio

confirmó:

composio: 7 tools

La conexión MCP quedó funcionando.

4. Problema con Google Calendar
Problema

Google Docs funcionaba, pero Calendar no podía crear eventos.

El agente indicó:

La cuenta de Google Calendar solo tiene permisos de lectura.
Solución

Fue necesario reconectar Google Calendar en Composio con permisos de escritura:

Leer calendarios
Crear eventos
Editar eventos

5. Creación de documentos
Problema

La primera prueba creó:

/root/Plan de proyecto OpenClaw.md

Esto era un archivo local del VPS y no un Google Doc.

Solución

Se especificó al agente que debía crear un documento directamente en Google Docs y devolver el enlace de Google Drive.