# SKILLS_DESIGN.md

Diseño de las skills de Argos (👁️) en el VPS.  
Fecha de análisis: 2026-11-02.  
Basado en: `IDENTITY.md`, `SOUL.md`, `AGENTS.md`, `USER.md`, `TOOLS.md`, `openclaw.json`, `openclaw skills list --json` y los `SKILL.md` de cada skill.

---

## Tabla resumen

| Skill | Ámbito | Estado | Servicio conectado |
|---|---|---|---|
| add-model-provider | custodian | Activa | — |
| browser-automation | extra | Activa | — |
| canvas | extra | Activa | — (panel macOS) |
| clawhub | bundled | Activa | ClawHub |
| cloud-image-bake | custodian | Activa | Cloud Workers |
| configure-agent-identity | workshop | Activa | — |
| configure-audio-transcription | workshop | Activa | — |
| configure-channel | custodian | Activa | Telegram |
| control-ui | bundled | Activa | — |
| diagnose-gateway | custodian | Activa | — |
| diagram-maker | bundled | Activa | — |
| healthcheck | bundled | Activa | — |
| meme-maker | bundled | Activa | — |
| news-briefing | workshop | Activa | — (web_fetch) |
| node-connect | bundled | Activa | — |
| node-inspect-debugger | bundled | Activa | — |
| notion | bundled | Activa | Notion |
| python-debugpy | bundled | Activa | — |
| resumen-diario | workspace | Activa | — |
| resumen-diario-contextual | workshop | Activa | — |
| skill-creator | bundled | Activa | — |
| spike | bundled | Activa | — |
| tmux | bundled | Activa | — |
| visualize | bundled | Activa | — |
| weather | bundled | Activa | — |
| zapier-mcp-onboarding | workshop | Activa | Google Docs, Google Calendar (vía Zapier MCP) |

---

## add-model-provider (custodian)

### ¿Qué hace esta skill?
Añade un proveedor de modelos de IA (API key o suscripción) a la configuración de OpenClaw y lo prueba en vivo sin exponer credenciales.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** qué proveedor quiere añadir (nombre y modelo), y cómo prefiere suministrar la clave (variable de entorno o archivo seguro). No debe escribir la clave en el chat.
- **De los archivos de configuración:** `AGENTS.md` prohíbe exponer credenciales y exige preguntar antes de cambiar configuración. `IDENTITY.md` y `SOUL.md` definen el tono de Argos. `openclaw.json` muestra los proveedores actuales (LiteLLM con modelos Claude Opus 4.6 y otros vía OpenRouter).

### ¿Cómo es un buen output?
- **Formato:** texto en chat con el resultado de cada paso (Gather → Mutate → Prove → Report).
- **Destino:** la conversación actual.
- **Verificación:** el modelo responde exactamente `PROVIDER-PROOF-OK` en el comando de prueba. Se reporta el id del modelo y la latencia.

**Estado actual:** Activa — `openclaw skills list` la muestra como `✓ ready`, `eligible: true`, sin bins ni OS missing. No tiene errores reportados por `openclaw doctor` (los warnings del doctor son generales del sistema, no específicos de esta skill).

---

## browser-automation (extra)

### ¿Qué hace esta skill?
Controla páginas web mediante el navegador integrado de OpenClaw en flujos multipaso, logins, gestión de pestañas y recuperación de referencias obsoletas.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** una descripción de la tarea a realizar en el navegador. No necesita formato estructurado; basta lenguaje natural.
- **De los archivos de configuración:** `AGENTS.md` indica que el agente puede usar el navegador libremente, pero no debe enviar ni publicar nada sin permiso explícito. `TOOLS.md` no lista servicios web específicos para el navegador. `USER.md` indica que Félix usa VS Code, Python, Claude, ChatGPT, Pentaho Spoon y n8n, lo que puede ser relevante para identificar páginas.

### ¿Cómo es un buen output?
- **Formato:** texto en chat describiendo las acciones realizadas y el estado final de la página.
- **Destino:** la conversación actual.
- **Verificación:** el agente puede leer el estado de la página resultante con `read_page` y confirmar que la acción deseada se completó.

**Estado actual:** Activa — `openclaw skills list` la muestra como `✓ ready`, `eligible: true`, sin dependencias faltantes. Es una skill de plugin (`openclaw-extra`, symlink en `plugin-skills/`).

---

## canvas (extra)

### ¿Qué hace esta skill?
Muestra documentos/widget alojados en OpenClaw en un panel macOS conectado y controla la visibilidad o navegación del panel.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** qué contenido mostrar en el panel o qué acción de navegación realizar.
- **De los archivos de configuración:** `SOUL.md` indica al agente ser resolutivo pero no asumir permisos. `AGENTS.md` dice que puede leer archivos y crear contenido dentro del workspace. Esta skill necesita un panel macOS conectado; el VPS actual no tiene macOS, por lo que su uso práctico aquí es limitado.

### ¿Cómo es un buen output?
- **Formato:** texto en chat confirmando que el panel se actualizó o que no hay panel disponible.
- **Destino:** la conversación actual.
- **Verificación:** el agente puede leer el resultado devuelto por la skill o preguntar al usuario si el cambio se reflejó.

**Estado actual:** Activa — aparece como `✓ ready` y `eligible: true`. Sin embargo, depende de un panel macOS conectado que puede no estar disponible en este VPS Linux; no se puede verificar su funcionamiento sin un cliente macOS conectado (estado **Sin verificar** en la práctica si no hay tal panel).

---

## clawhub (bundled)

### ¿Qué hace esta skill?
Busca, instala, verifica, actualiza, desinstala, publica o sincroniza skills y plugins desde el registro ClawHub.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** qué acción realizar (buscar, instalar, actualizar…) y el nombre de la skill/plugin cuando aplique.
- **De los archivos de configuración:** `openclaw.json` contiene las skills instaladas y su estado (habilitada/deshabilitada). `AGENTS.md` exige preguntar antes de cambiar configuración o instalar software.

### ¿Cómo es un buen output?
- **Formato:** texto en chat con el resultado de la operación (skill encontrada, instalada, etc.).
- **Destino:** la conversación actual.
- **Verificación:** la skill usa comandos `openclaw skills` que devuelven códigos de éxito/error y listados verificables.

**Estado actual:** Activa — `openclaw skills list` la incluye como `✓ ready`, `eligible: true`. Es una skill bundled sin dependencias externas.

---

## cloud-image-bake (custodian)

### ¿Qué hace esta skill?
Prepara (bake), selecciona, prueba y retira de forma segura imágenes de Cloud Workers mediante crabbox y comandos de configuración.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** qué tooling instalar en la imagen cloud y confirmación explícita antes de retirar una imagen anterior.
- **De los archivos de configuración:** `openclaw.json` contiene la configuración de `cloudWorkers`. `AGENTS.md` exige preguntar antes de cualquier acción destructiva y antes de cambiar configuración.

### ¿Cómo es un buen output?
- **Formato:** informe en chat con: perfil, backend, ids de imagen nueva y anterior, resultado del smoke test, temporización warmup before/after y estado de rollback.
- **Destino:** la conversación actual.
- **Verificación:** la skill usa `crabbox warmup`, `crabbox run` y `openclaw config` que devuelven salidas con código de error. Se verifica que el tooling responde con su versión y que el warmup es más rápido que antes.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Skill custodian sin dependencias externas (crabbox viene con OpenClaw).

---

## configure-agent-identity (workshop)

### ¿Qué hace esta skill?
Cambia el nombre, emoji, avatar o saludo de un agente de OpenClaw aplicando solo los campos solicitados y verificando que se guardaron.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** qué agente modificar (por defecto `main`) y qué campo(s) cambiar. No necesita formato estructurado.
- **De los archivos de configuración:** `IDENTITY.md` define la identidad actual de Argos. `AGENTS.md` prohíbe editar archivos de configuración directamente y exige usar `openclaw config` para cambios. `openclaw.json` contiene la identidad actual en `agents.entries.main.identity`.

### ¿Cómo es un buen output?
- **Formato:** texto en chat listando qué campos se aplicaron, cuáles no eran soportados (ej. saludo persistente) y el resultado de la verificación.
- **Destino:** la conversación actual.
- **Verificación:** la skill lee la configuración con `gateway(action="config.get", path="agents.entries.<agentId>.identity")` y confirma que los valores coinciden con lo solicitado.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Skill workshop con SKILL.md completo y sin dependencias de sistema.

---

## configure-audio-transcription (workshop)

### ¿Qué hace esta skill?
Configura la transcripción de audio entrante en OpenClaw (selección de proveedor, backend local o cloud) y verifica que funciona.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** si prefiere un backend local (privacidad, offline) o cloud, y consentimiento explícito si el audio se envía a un proveedor externo. Si pide activar, basta una frase como "activa la transcripción de audio".
- **De los archivos de configuración:** `AGENTS.md` prohíbe enviar datos privados a terceros sin permiso y exige preguntar antes de cambiar configuración. `openclaw.json` contiene la configuración actual de `tools.media.audio`.

### ¿Cómo es un buen output?
- **Formato:** texto en chat distinguiendo: configuración habilitada, proveedor seleccionado, y transcripción verificada en vivo (o explicación de por qué no se pudo).
- **Destino:** la conversación actual.
- **Verificación:** se procesa una nota de voz de prueba del usuario y se devuelve el texto transcrito. No se da por válida solo la configuración.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Skill workshop con SKILL.md detallado.

---

## configure-channel (custodian)

### ¿Qué hace esta skill?
Configura y prueba un canal de chat (Telegram, Discord, etc.) en OpenClaw usando comandos no interactivos y SecretRefs para las credenciales.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** qué canal configurar. Para Telegram, necesita que Félix envíe un DM al bot para obtener su ID de usuario. No necesita el token (ya está en la configuración o el operador lo introduce por un prompt enmascarado).
- **De los archivos de configuración:** `openclaw.json` muestra que Telegram ya está configurado (`enabled: true`, `botToken` presente, `dmPolicy: pairing`). `TOOLS.md` no mencionaba Telegram antes pero ahora sabemos que sí está habilitado. `AGENTS.md` exige preguntar antes de cambiar configuración.

### ¿Cómo es un buen output?
- **Formato:** texto en chat con el canal configurado, las rutas de configuración escritas (nunca los valores), el destino de prueba y el resultado de entrega observado.
- **Destino:** la conversación actual.
- **Verificación:** se envía un mensaje de prueba real con `openclaw message send` y se confirma la entrega.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Skill custodian. Telegram ya está habilitado en `openclaw.json` y operativo (el usuario tiene ID `1518751102`, el bot responde).

---

## control-ui (bundled)

### ¿Qué hace esta skill?
Opera y soluciona problemas de la interfaz web de Control UI de OpenClaw: navegar clientes conectados, organizar sesiones, construir dashboards y manejar Gateways.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** qué acción realizar en el Control UI (ej. "muéstrame los clientes conectados", "organiza las sesiones").
- **De los archivos de configuración:** `openclaw.json` contiene la configuración del Gateway (puerto `18789`, bind `loopback`, token de autenticación). `AGENTS.md` permite al agente leer archivos y explorar.

### ¿Cómo es un buen output?
- **Formato:** texto en chat describiendo el estado del Control UI, los clientes/sesiones encontrados, o confirmando la acción realizada.
- **Destino:** la conversación actual.
- **Verificación:** la skill usa comandos de OpenClaw que devuelven datos estructurados verificables.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Skill bundled sin dependencias externas.

---

## diagnose-gateway (custodian)

### ¿Qué hace esta skill?
Diagnostica problemas del Gateway, configuración, secretos, canales y puertos de OpenClaw con comandos de solo lectura.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** una descripción del problema o simplemente "diagnostica el gateway".
- **De los archivos de configuración:** `openclaw.json` contiene la configuración completa del Gateway, canales y secretos. `AGENTS.md` prohíbe comandos destructivos y cambios de configuración sin permiso — esta skill es de solo lectura, consistente con esa regla.

### ¿Cómo es un buen output?
- **Formato:** informe en chat con hallazgos en orden causal, evidencia para cada uno, estado actual del Gateway/config/SecretRefs/canales/puertos, y una recomendación de siguiente paso.
- **Destino:** la conversación actual.
- **Verificación:** los comandos de diagnóstico (`openclaw doctor --lint`, `openclaw gateway status --deep`, etc.) devuelven salida verificable.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Skill custodian de solo lectura.

---

## diagram-maker (bundled)

### ¿Qué hace esta skill?
Crea diagramas SVG, HTML o Excalidraw para conceptos, arquitecturas, flujos y pizarras.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** qué diagramar (descripción en lenguaje natural). Puede ser vago ("el flujo de mi ETL") o concreto.
- **De los archivos de configuración:** `USER.md` indica que Félix usa Pentaho Spoon (ETLs), Python y n8n, lo que permite contextualizar los diagramas. `AGENTS.md` permite al agente leer archivos y crear contenido en el workspace.

### ¿Cómo es un buen output?
- **Formato:** archivo SVG/HTML/Excalidraw creado en el workspace (o mostrado inline si es pequeño), más una descripción en chat.
- **Destino:** archivo en el workspace (se puede abrir con el visor de VS Code) y/o la conversación.
- **Verificación:** el archivo se crea y puede visualizarse. El agente puede mostrarlo inline en la conversación si el formato lo permite.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Skill bundled que no necesita servicios externos.

---

## healthcheck (bundled)

### ¿Qué hace esta skill?
Audita y refuerza la seguridad de un host OpenClaw: SSH, cortafuegos, actualizaciones, exposición, backups, cifrado de disco y seguridad del Gateway.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** una solicitud como "audita la seguridad del servidor" o "healthcheck".
- **De los archivos de configuración:** `AGENTS.md` exige preguntar antes de cambiar configuración — esta skill es de auditoría. `openclaw.json` contiene la configuración del Gateway.

### ¿Cómo es un buen output?
- **Formato:** informe en chat con hallazgos por categoría (SSH, firewall, updates, exposure, backups, encryption, gateway) y recomendaciones.
- **Destino:** la conversación actual.
- **Verificación:** los comandos de auditoría (`openclaw doctor --lint`, etc.) devuelven salida con los hallazgos.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Skill bundled que ejecuta comandos locales de diagnóstico.

---

## meme-maker (bundled)

### ¿Qué hace esta skill?
Busca plantillas de memes, sugiere formatos y genera memes locales o alojados.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** qué meme crear (tema, tono, texto o descripción).
- **De los archivos de configuración:** `USER.md` informa sobre los intereses y proyectos de Félix (útiles para personalizar el meme). `AGENTS.md` permite crear archivos en el workspace.

### ¿Cómo es un buen output?
- **Formato:** archivo de imagen generado (local o URL) más una descripción en chat.
- **Destino:** archivo en el workspace o URL accesible.
- **Verificación:** el archivo se genera y puede abrirse/visualizarse.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Depende de `node` (disponible en el VPS).

---

## news-briefing (workshop)

### ¿Qué hace esta skill?
Investiga titulares de actualidad y redacta un resumen escueto con fuentes, fechas verificadas y diversidad temática.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** una petición como "¿qué noticias hay hoy?" o "resumen de actualidad". Opcionalmente puede especificar región/idioma.
- **De los archivos de configuración:** Para España, la skill usa por defecto Google News RSS España. `IDENTITY.md` indica que Argos se expresa en español. `SOUL.md` pide ser conciso cuando toca. `AGENTS.md` prohíbe incluir datos privados en búsquedas web — esta skill busca titulares públicos.

### ¿Cómo es un buen output?
- **Formato:** briefing en chat con no más de 2 frases por noticia, fecha y hora de corte, enlace al artículo original, y distinción entre hechos verificados y alegaciones.
- **Destino:** la conversación actual.
- **Verificación:** las noticias incluyen enlaces a fuentes y fechas de publicación verificables.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Skill workshop con SKILL.md detallado. Usa `web_fetch` sobre RSS públicos (no requiere API key).

---

## node-connect (bundled)

### ¿Qué hace esta skill?
Diagnostica fallos de conexión del Control UI de OpenClaw en navegadores y apps nativas (Android, iOS, macOS) analizando ruta, autenticación, emparejamiento, códigos QR y estados de reconexión.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** qué cliente tiene problemas de conexión y qué síntoma muestra.
- **De los archivos de configuración:** `openclaw.json` contiene la configuración del Gateway (puerto, bind, auth token, Tailscale). `AGENTS.md` prohíbe cambiar configuración sin permiso — esta skill es diagnóstica, no modifica nada.

### ¿Cómo es un buen output?
- **Formato:** informe en chat con el diagnóstico del problema (ruta, auth, pairing, QR, reconexión) y el siguiente paso recomendado.
- **Destino:** la conversación actual.
- **Verificación:** los comandos de diagnóstico (`openclaw gateway status`, `curl` a endpoints, etc.) producen salida analizable.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Skill bundled que usa comandos internos de OpenClaw y curl.

---

## node-inspect-debugger (bundled)

### ¿Qué hace esta skill?
Depura código Node.js con `node inspect`, breakpoints, perfiles CDP, heap y CPU.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** qué proceso Node.js depurar, o que analice un archivo concreto.
- **De los archivos de configuración:** `USER.md` no menciona Node.js específicamente, pero Félix usa programación en general. `AGENTS.md` permite leer archivos.

### ¿Cómo es un buen output?
- **Formato:** sesión de depuración o informe con hallazgos (uso de CPU/heap, stack traces, etc.).
- **Destino:** la conversación actual (o archivo exportado si procede).
- **Verificación:** los comandos `node inspect` producen salida que se puede analizar y confirmar.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Depende de `node` (disponible en el VPS).

---

## notion (bundled)

### ¿Qué hace esta skill?
Gestiona páginas, contenido Markdown, fuentes de datos, archivos, comentarios, búsquedas y Workers de Notion mediante su CLI/API oficial.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** qué acción realizar en Notion (crear página, buscar, listar, etc.) y el contenido cuando aplique.
- **De los archivos de configuración:** `AGENTS.md` exige preguntar antes de enviar datos a terceros. `SOUL.md` pide no asumir permisos. **No hay token de Notion en `openclaw.json`** ni en `TOOLS.md` — el agente debe preguntar si Notion está autenticado antes de usarlo.

### ¿Cómo es un buen output?
- **Formato:** texto en chat con el resultado de la operación (página creada, búsqueda completada, etc.) y enlace si aplica.
- **Destino:** la conversación actual y/o Notion (la API).
- **Verificación:** la skill usa la CLI de Notion o API que devuelve respuestas estructuradas con IDs y estados.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Skill bundled. Sin embargo, **el estado real de la conexión a Notion no se ha verificado** (no aparece en `TOOLS.md` ni en `openclaw.json` como servicio conectado). El SKILL.md indica que usa el CLI `notion`, que requiere autenticación previa. Se clasifica como **Activa** porque la skill está disponible, pero su funcionamiento depende de que Notion esté configurado (estado **Sin verificar** en cuanto a conectividad).

---

## python-debugpy (bundled)

### ¿Qué hace esta skill?
Depura código Python con pdb, `breakpoint()`, inspección póstuma y conexión remota debugpy.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** qué archivo o proceso Python depurar, o que analice un error concreto.
- **De los archivos de configuración:** `USER.md` indica que Félix usa Python a diario y que su proyecto principal es AI Engineering. `AGENTS.md` permite leer archivos en el workspace.

### ¿Cómo es un buen output?
- **Formato:** sesión de depuración en terminal con hallazgos, o informe con stack traces y valores de variables.
- **Destino:** la conversación actual.
- **Verificación:** los comandos de depuración producen salida (pdb, tracebacks) que el agente puede leer y resumir.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Depende de `python3` (presente en el VPS).

---

## resumen-diario (workspace)

### ¿Qué hace esta skill?
Genera un resumen organizado del día y ayuda a establecer prioridades.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** una frase como "resumen del día", "plan de hoy", "organiza mi día". No necesita estructura.
- **De los archivos de configuración:** `USER.md` contiene las prioridades y proyectos activos de Félix (AI Engineering, aplicaciones, herramientas ETL). Los archivos de memoria (`memory/YYYY-MM-DD.md`) contienen las actividades diarias. `AGENTS.md` indica que el agente debe leer los archivos de memoria antes de responder.

### ¿Cómo es un buen output?
- **Formato:** texto en chat con 3 prioridades numeradas y una acción recomendada. Máximo 80 palabras, bullets claros.
- **Destino:** la conversación actual.
- **Verificación:** las prioridades deben coincidir con lo que Félix ha mencionado. La skill misma dice que no debe inventar tareas ni asumir finalización sin confirmación.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Es la única skill en el directorio `workspace/skills/`. Tiene SKILL.md completo con todas las secciones (Descripción, Procedimiento, Formato de respuesta, Reglas). No tiene errores ni dependencias externas.

---

## resumen-diario-contextual (workshop)

### ¿Qué hace esta skill?
Cuando se pide un resumen diario, repasa la actividad real y los elementos no resueltos del contexto de hoy y ofrece un siguiente enfoque sin inventar prioridades.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** una frase como "resumen del día" o "¿qué tal el día?". No necesita estructura.
- **De los archivos de configuración:** Los mismos que `resumen-diario` (USER.md, memoria diaria). Además, la skill puede usar `memory_search`, `memory_get`, `sessions_search` y `sessions_history` para buscar actividad en conversaciones del mismo día.

### ¿Cómo es un buen output?
- **Formato:** breve resumen en español con bullets que distingan: trabajo completado confirmado, decisiones/enlaces no resueltos y prioridades futuras explícitas. Máximo 80 palabras. Si no hay actividad del día, lo dice sin inventar.
- **Destino:** la conversación actual.
- **Verificación:** los elementos marcados como completados deben estar confirmados por el contexto. Las prioridades no se inventan.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Skill workshop con SKILL.md completo y detallado.

---

## skill-creator (bundled)

### ¿Qué hace esta skill?
Crea, repara, valida o reestructura archivos `SKILL.md` y sus recursos empaquetados.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** qué skill crear/modificar y su descripción o cambios.
- **De los archivos de configuración:** `AGENTS.md` permite crear archivos en el workspace y leer los existentes. Los SKILL.md existentes sirven como referencia de formato.

### ¿Cómo es un buen output?
- **Formato:** archivo SKILL.md creado/actualizado en el directorio de skills correspondiente, más confirmación en chat.
- **Destino:** archivo en el workspace.
- **Verificación:** la skill puede autovalidar el SKILL.md generado. El agente puede leer el archivo creado y confirmar que tiene la estructura correcta.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Skill bundled que funciona sobre el sistema de archivos local.

---

## spike (bundled)

### ¿Qué hace esta skill?
Ejecuta prototipos desechables para validar viabilidad, comparar enfoques y emitir un veredicto.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** qué problema validar, enfoques a comparar, o hipótesis a probar.
- **De los archivos de configuración:** `AGENTS.md` indica que el agente debe preferir soluciones existentes antes de construir algo nuevo. `USER.md` contextualiza los proyectos de Félix.

### ¿Cómo es un buen output?
- **Formato:** prototipo ejecutado y veredicto en chat (viable / no viable, comparativa, recomendación).
- **Destino:** la conversación actual.
- **Verificación:** el prototipo se ejecuta y produce una salida verificable (código de retorno, resultado).

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Skill bundled sin dependencias externas.

---

## tmux (bundled)

### ¿Qué hace esta skill?
Controla sesiones y paneles de tmux para CLIs interactivos: listar, capturar salida, enviar teclas, pegar texto y monitorizar prompts.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** qué acción realizar en tmux (ej. "muéstrame las sesiones tmux", "ejecuta este comando en una nueva sesión").
- **De los archivos de configuración:** No necesita nada específico de los archivos de configuración. `AGENTS.md` permite al agente ejecutar comandos en terminal.

### ¿Cómo es un buen output?
- **Formato:** salida capturada de los paneles/sesiones de tmux, más descripción en chat.
- **Destino:** la conversación actual.
- **Verificación:** la salida capturada de tmux es directamente verificable por el agente y el usuario.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Depende de `tmux` (presente en el VPS Linux).

---

## visualize (bundled)

### ¿Qué hace esta skill?
Crea visualizaciones inline para código y explicaciones, o escribe widgets de dashboard persistentes de OpenClaw con `show_widget`.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** qué visualizar (datos, concepto, código) y si quiere un gráfico inline o un widget persistente.
- **De los archivos de configuración:** `USER.md` contextualiza los proyectos de Félix. `AGENTS.md` permite crear archivos en el workspace.

### ¿Cómo es un buen output?
- **Formato:** visualización inline (SVG/HTML/ASCII) en la conversación, o archivo/widget persistente.
- **Destino:** la conversación actual y/o el dashboard de OpenClaw.
- **Verificación:** la visualización se muestra y es correcta semánticamente.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Skill bundled que genera contenido renderizable.

---

## weather (bundled)

### ¿Qué hace esta skill?
Obtiene el tiempo actual y pronósticos usando `web_fetch` (con caída a `wttr.in` mediante curl) para localizaciones, lluvia, temperatura y planificación de viajes.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** una pregunta como "¿qué tiempo hace en Madrid?" o "¿lloverá mañana?". Puede especificar ubicación o el agente puede inferirla de `USER.md` (no hay ubicación guardada actualmente).
- **De los archivos de configuración:** `USER.md` no contiene ubicación de Félix; si no se especifica, el agente debe preguntar. `AGENTS.md` prohíbe incluir datos privados en búsquedas web — esta skill busca datos meteorológicos públicos, lo cual es seguro.

### ¿Cómo es un buen output?
- **Formato:** texto en chat con temperatura, condiciones, precipitación y periodo del pronóstico.
- **Destino:** la conversación actual.
- **Verificación:** los datos provienen de fuentes públicas (wttr.in / web_fetch) con atribución.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Skill bundled que usa APIs públicas gratuitas.

---

## zapier-mcp-onboarding (workshop)

### ¿Qué hace esta skill?
Conecta, audita o revisa la integración Zapier MCP: verifica herramientas activas, acciones de apps y cuentas conectadas, y completa la activación u ofrece un inventario.

### ¿Qué input necesita el agente?
- **Del usuario (Félix):** una solicitud como "conecta Zapier" o "audita las apps de Zapier". Para OAuth, necesita que Félix abra una URL de autorización (con posible túnel SSH). Para connection-token, que genere un token en la web de Zapier y lo inyecte de forma segura.
- **De los archivos de configuración:** `TOOLS.md` documenta qué servicios Google están habilitados (Docs ✅, Calendar ✅, Drive ❓, Gmail ❌). `openclaw.json` tiene configurado el egress proxy para `mcp.zapier.com`. `AGENTS.md` exige preguntar antes de cualquier acción que afecte a terceros, enviar datos o cambiar configuración. El `SKILL.md` de esta skill es muy detallado (22 pasos).

### ¿Cómo es un buen output?
- **Formato:** mensaje en chat con el resultado de la conexión/auditoría: apps conectadas, acciones disponibles, o siguiente paso si falta autorización.
- **Destino:** la conversación actual; las acciones de escritura (crear evento en Calendar, documento en Docs) se entregan a los servicios de Google.
- **Verificación:** la skill verifica con `mcporter list zapier --schema` que las herramientas están expuestas. Para escrituras, lee el recurso creado (ej. `google_docs_get_document_content`) y confirma que coincide con lo solicitado.

**Estado actual:** Activa — `✓ ready`, `eligible: true`. Skill workshop en `workshop-skills/zapier-mcp-onboarding/` con SKILL.md muy detallado. Es la skill que canaliza el acceso a Google Docs y Google Calendar a través de Zapier MCP. `openclaw.json` confirma que el egress proxy permite tráfico a `mcp.zapier.com`.

---

## Notas adicionales

### Servicios conectados (de TOOLS.md y openclaw.json)

| Servicio | Estado en TOOLS.md | Confirmado en openclaw.json |
|---|---|---|
| Google Docs | Enabled (Zapier) | — |
| Google Calendar | Enabled (Zapier) | — |
| Google Drive | No confirmado | — |
| Gmail | No habilitado | — |
| Telegram | No mencionado en TOOLS.md | ✅ Habilitado (bot token presente, dmPolicy: pairing) |
| Notion | No mencionado | No configurado |

### Información de Félix (de USER.md)

- Nombre: Félix.
- Proyecto principal: AI Engineering.
- Secundarios: AI-assisted application management y creation.
- Herramientas diarias: Python, Claude, ChatGPT, Pentaho Spoon (ETLs), aprendiendo n8n.
- Editor preferido: VS Code.
- Prefiere actualizaciones concisas durante implementación.

---

*Fin de SKILLS_DESIGN.md*