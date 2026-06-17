# Módulo opcional: Notion (memoria + CRM)

Con este módulo el bot **recuerda a cada cliente** entre conversaciones y guarda un
**historial** en una base de Notion. Es donde está el 80% del valor para un dueño:
un bot sin rastro es un juguete; uno que registra cada contacto y conversación es una
herramienta de negocio.

Es **opt-in**. Si recién arrancás, podés saltearlo y activarlo después.

## Cómo funciona

En cada mensaje, el bot:
1. Busca el contacto por su número de WhatsApp (`buscar_contacto`).
2. Responde usando lo que sabe del negocio + lo que recuerda de ese cliente.
3. Registra un resumen de la conversación al cerrar el turno (`registrar_contacto`).

🛡️ **Seguridad:** la búsqueda SOLO acepta el `phone_e164` del número que está
escribiendo (el filtro se arma del lado del servidor). El bot **no puede** traer
datos de otro cliente aunque alguien se lo pida o diga ser otra persona. La identidad
se ancla al número verificado por Meta, nunca a un texto.

## Paso 1 — Crear la base en Notion

Creá una base (database) con, como mínimo, estas propiedades:

| Propiedad             | Tipo        | Para qué |
|-----------------------|-------------|----------|
| `Nombre`              | Title       | Nombre del cliente |
| `WhatsApp`            | Phone/Text  | Número en formato E.164 (ej. `+5491166...`) — **la clave** |
| `Resumen charlas`     | Text (long) | Memoria narrativa de conversaciones previas |
| `Última interacción`  | Date        | Fecha del último contacto |
| `Notas privadas`      | Text        | Uso interno tuyo — el bot la lee para contexto pero NUNCA la repite al cliente |

Podés sumar las que quieras (Estado, Etiquetas, etc.).

## Paso 2 — Conectar Notion en Kapso

1. Creá un **internal integration token** en Notion
   (https://www.notion.so/my-integrations) y compartí la base con esa integración.
   *(Este paso lo hacés vos: requiere tu login de Notion.)*
2. En Kapso, conectá la integración de Notion (app integration) para que el workflow
   pueda consultar/actualizar la base.

## Paso 3 — Wirear las tools al agente

Sumá estas dos app-integration tools al nodo `agent_main` de la definición
(`flow_agent_app_integration_tools`), siguiendo el patrón probado:

- **`buscar_contacto`** → acción `notion-query-database` sobre tu base.
  Argumento ÚNICO: `phone_e164` (string). El filtro contra Notion se construye del
  lado del servidor por el campo `WhatsApp`. **No** acepta filtros por nombre u otros
  campos (es la barrera de seguridad).
- **`registrar_contacto`** → acción `notion-update-page`.
  Argumentos: `pageId` (de `buscar_contacto.results[0].id`), `ultima_interaccion`
  (fecha de hoy), `resumen_charlas` (append `[YYYY-MM-DD] resumen`). Nunca pisa
  `Nombre` ni `WhatsApp`.

> Referencia: en `template/system_prompt.template.md`, los pasos 2 y 4 del protocolo
> (bloques `MODULE:notion`) ya describen el comportamiento esperado. Al activar el
> módulo, esos bloques quedan en el prompt y `{{STEP_RESPOND}}=3`, `{{STEP_PERSIST}}=4`.

## Variante más segura (avanzado)

Si querés blindaje extra (defensa en 2 capas como el patrón de FrancOS), en lugar de
las app-integration tools podés usar una **función de Kapso** que lea el remitente
de `execution_context.context.phone_number` (lo setea Kapso desde Meta, no el LLM) y
rechace cualquier consulta cuyo número no coincida. Así, aunque el prompt sea
jailbreakeado, no hay fuga de datos. Es opcional; las app-integration tools con
filtro server-side ya cubren el caso común.
