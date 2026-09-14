# Diario de la experiencia con OpenClaw

## Día de pelea con OpenClaw 😈

Empecé este proyecto pensando que sería algo bastante directo: instalar OpenClaw, conectarlo a algunas herramientas, enseñarle unas skills y dejarlo funcionando.

Spoiler: no fue tan sencillo.

Lo que más me ha costado ha sido **pelearme con las reconexiones y la configuración de OpenClaw**. Reconectar ha sido una odisea y confiar en otros llm para guiarme confiando en ellos todo un error. 

Tenía configurado DeepSeek, terminé usando GPT-5.6 Luna, sigo echandole la culpa al pinchesupadregpt.

Antes de llegar al tema de skills "perdí" mucho tiempo peleandome con Calvo para que consiguiera conectarse y obtener los datos, supongo que no le di las instrucciones demasiado claras o amenazantes. 🔪

La parte de las skills fue probablemente donde más claro vi la idea del proyecto: **no intentar hacer una skill gigante que haga todo**, sino pedir una cosa concreta cada vez. Primero comprobar autenticación, después consultar proyectos, luego trabajo pendiente, progreso, deadlines y finalmente detalles de un proyecto.

Al final, la sensación es un poco contradictoria: he perdido bastante tiempo peleándome con cosas que inicialmente parecían secundarias, pero precisamente por eso ahora entiendo mucho mejor cómo está montado OpenClaw y he aprendido a si funciona ahora no lo dejes para luego. 

### Lo que me llevo

* Da instrucciones claras.
* Evitar circulos infinitos de consultas si algo no avanzar cortar y cambiar el guión.
* Las skills funcionan mejor cuando cada una tiene una responsabilidad concreta.
* Los tokens y credenciales hay que tratarlos como secretos y nunca meterlos en el repositorio.
* Cuando algo deja de funcionar después de una reconexión, primero hay que comprobar el estado real del gateway y del servicio.
* Y, sobre todo, **reiniciar OpenClaw probablemente arregla menos cosas de las que uno espera, pero a veces es exactamente lo que hacía falta.** 😅

En resumen: no ha sido una experiencia especialmente limpia, pero sí bastante educativa. Creo que ahora entiendo mucho mejor qué significa realmente "enseñar" a un agente a hacer cosas, en lugar de simplemente configurarlo y esperar que funcione.
