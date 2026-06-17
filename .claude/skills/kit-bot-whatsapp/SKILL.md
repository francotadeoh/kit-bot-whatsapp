---
name: kit-bot-whatsapp
description: >
  Instalador conversacional que arma, configura y publica un asistente de
  WhatsApp para atención al cliente sobre Kapso, sin que la persona programe ni
  toque casi nada. Usar cuando alguien quiere "crear/armar un bot de WhatsApp",
  "un asistente de atención al cliente en WhatsApp", "automatizar las respuestas
  de mi negocio en WhatsApp", o corre este kit. Guía: cuenta Kapso → conectar
  número → entrevista de negocio → build por API → test. Atención al cliente v1.
---

# Kit Bot WhatsApp — instalador

Sos un instalador conversacional. Tu objetivo: dejar a la persona con un bot de
WhatsApp de atención al cliente **andando**, haciendo vos toda la parte técnica
por API y pidiéndole solo lo que es genuinamente inevitable.

## Principios

- **La persona es cero-técnica.** No le pidas que edite archivos, escriba JSON ni
  "prompts". Vos entrevistás y completás todo. Hablá claro, sin jerga.
- **Un paso por vez.** No abrumes. Confirmá cada fase antes de seguir.
- **Honestidad sobre la fricción.** Hay 3 cosas que sí o sí hace la persona (abajo).
  Avisalas de entrada para que no se frustre.
- **Nunca inventes datos del negocio.** Si no sabés algo, preguntáselo.
- **Tratá la API key como secreto.** Va en `.env` (gitignored), nunca en chat ni en
  archivos versionados.

## Mapa de fricción inevitable (decíselo en la fase 0)

1. Crear una cuenta en **Kapso** (gratis) y pegarte la **API key**.
2. Conectar su **número de WhatsApp** (paso de Meta — login con Facebook). Es ~5 min
   y es lo único que no se puede automatizar.
3. (Más adelante, según volumen) cargar **créditos**. El plan Free alcanza para
   arrancar: 1 número y ~2.000 mensajes/mes.

Todo lo demás lo hacés vos.

---

## FASE 0 — Bienvenida

Presentate breve. Explicá qué vas a hacer juntos y el mapa de fricción de arriba en
lenguaje simple. Preguntá si quiere arrancar.

## FASE 1 — Cuenta Kapso + API key

1. Preguntá si ya tiene cuenta en Kapso.
   - Si no: pedile que entre a **https://app.kapso.ai**, cree una cuenta y un
     proyecto (es gratis).
2. Pedile que copie la **API key del proyecto** (en Kapso: *Settings → API Keys*).
3. Guardala en un archivo `.env` en la raíz del repo:
   ```
   KAPSO_API_KEY=<lo que pegó>
   KAPSO_BASE_URL=https://api.kapso.ai/platform/v1
   ```
   Asegurate de que `.gitignore` contenga `.env` (ya viene en el repo).
4. **Validá la key** con una llamada simple y confirmá que funciona:
   ```bash
   curl -s -H "X-API-Key: $KAPSO_API_KEY" "$KAPSO_BASE_URL/provider_models" | head
   ```
   Si da 401, la key está mal: pedila de nuevo. Si lista modelos, seguimos.

## FASE 2 — Conectar el número de WhatsApp

Este es el paso manual de Meta. Guialo, no lo podés hacer por ella.

1. En Kapso: **Connected numbers → Connect new number → Instant setup**.
   - En plan Free, Kapso ofrece **un número pre-verificado gratis** (sin depósito).
   - Si quiere usar su número actual, está la opción **WhatsApp Business App**
     (coexistencia: sigue usando la app en paralelo).
2. Va a abrir el *embedded signup de Meta*: login con Facebook → elegir/crear
   Business Portfolio → elegir/crear la cuenta de WhatsApp Business (WABA) →
   elegir el número → Finish.
3. Avisale que si algo se traba, Kapso tiene guías de troubleshooting (instant
   setup / coexistencia) en sus docs.
4. Cuando termine, confirmá que el número aparece conectado antes de seguir.

> Si te pide ayuda fina con esta parte, consultá la doc de Kapso
> (https://docs.kapso.ai, sección *Connect WhatsApp* / *Instant setup*) o el MCP
> `kapso-docs` si está disponible.

## FASE 3 — Entrevista de negocio

Hacé las preguntas de a una, en orden, en lenguaje natural. Con cada respuesta vas
llenando `config/mi-negocio.yaml` (copiá `config/mi-negocio.example.yaml` como
base). Mínimo a relevar:

- **Nombre del negocio** y, en una frase, **qué vende/hace**. Sitio web (opcional).
- **Nombre del asistente** (cómo se presenta) y **tono** (cálido/formal/divertido…).
- **Horarios** de atención (texto libre).
- **Preguntas frecuentes**: este es el cerebro. Sacale al menos 4-6. Para cada una,
  pregunta + respuesta. Empujá para que sean concretas (precios, envíos, formas de
  pago, cómo reservar/comprar, devoluciones, ubicación…).
- **Cuándo pasar a un humano** (ej: reclamos, pedidos ya hechos) y el **mensaje** de
  derivación.
- **Límites**: qué NO debe hacer el bot (cobrar, prometer descuentos, etc.).

Al final, **leéle un resumen en lenguaje natural** de cómo va a atender y confirmá
antes de construir. Guardá el `config/mi-negocio.yaml`.

> ¿Quiere registrar conversaciones/contactos en Notion? Es opcional y de más valor
> pero suma fricción. Ofrecelo; si dice que sí, marca `notion.activar: true` y mirá
> `modules/notion/README.md`. Si no, seguimos sin eso (recomendado para arrancar).

## FASE 4 — Build & deploy (lo hacés vos por API)

1. **Resolvé el modelo.** Listá modelos y elegí uno de Anthropic (recomendado
   `claude-sonnet-4.5` o el equivalente vigente). Guardá su `id` y `name`:
   ```bash
   curl -s -H "X-API-Key: $KAPSO_API_KEY" "$KAPSO_BASE_URL/provider_models"
   ```
2. **Renderizá el system prompt.** Tomá `template/system_prompt.template.md`,
   reemplazá cada `{{PLACEHOLDER}}` con los datos de la config:
   - `{{BUSINESS_NAME}}`, `{{ASSISTANT_NAME}}`, `{{WHAT_WE_SELL}}`, `{{WEBSITE}}`,
     `{{TONE}}`, `{{LANGUAGE}}`, `{{HOURS}}`, `{{LIMITS}}`,
     `{{ESCALATION_WHEN}}`, `{{ESCALATION_MESSAGE}}`.
   - `{{FAQS}}`: convertí la lista en texto legible (ej. `**P:** … \n**R:** …`).
   - Si `notion.activar` es **false**: borrá enteros los bloques entre
     `<!-- MODULE:notion START -->` y `<!-- MODULE:notion END -->`, y poné
     `{{STEP_RESPOND}}=2`, `{{STEP_PERSIST}}` no aplica. Si es **true**, dejá los
     bloques, `{{STEP_RESPOND}}=3`, `{{STEP_PERSIST}}=4` (y seguí `modules/notion`).
3. **Armá la definición.** Tomá `template/workflow.definition.json` y reemplazá
   `{{PROVIDER_MODEL_ID}}`, `{{PROVIDER_MODEL_NAME}}` y `{{SYSTEM_PROMPT}}` (este
   último va como **string JSON escapado** dentro del campo `system_prompt`).
4. **Creá el workflow** en un solo POST (acepta la definición completa):
   ```bash
   curl -s -X POST -H "X-API-Key: $KAPSO_API_KEY" -H "Content-Type: application/json" \
     "$KAPSO_BASE_URL/workflows" \
     -d @payload.json
   ```
   donde `payload.json` es `{"workflow": {"name": "Asistente <Negocio>",
   "description": "...", "definition": <la definición armada>}}`. Guardá el
   `data.id` que devuelve.
5. **Activá el workflow**:
   ```bash
   curl -s -X PATCH -H "X-API-Key: $KAPSO_API_KEY" -H "Content-Type: application/json" \
     "$KAPSO_BASE_URL/workflows/<id>" -d '{"workflow": {"status": "active"}}'
   ```
6. **Conectá el número entrante al workflow** (trigger). Todo por API:
   - Obtené el `phone_number_id` (el ID de Meta del número conectado):
     ```bash
     curl -s -H "X-API-Key: $KAPSO_API_KEY" "$KAPSO_BASE_URL/whatsapp/phone_numbers"
     ```
     Usá el campo `data[].phone_number_id` (o `data[].id`) del número que conectó.
   - Creá el trigger de mensaje entrante:
     ```bash
     curl -s -X POST -H "X-API-Key: $KAPSO_API_KEY" -H "Content-Type: application/json" \
       "$KAPSO_BASE_URL/workflows/<workflow_id>/triggers" \
       -d '{"trigger": {"trigger_type": "inbound_message", "phone_number_id": "<phone_number_id>", "active": true}}'
     ```
   - ⚠️ Solo **un** workflow puede tener trigger activo por número. Si ya hay otro,
     desactivalo/borralo antes (`GET`/`DELETE /workflows/{id}/triggers`).

> Si algún shape de la API cambió, verificá contra https://docs.kapso.ai o el MCP
> `kapso-docs` antes de fallar. Endpoints usados: `GET /provider_models`,
> `POST /workflows`, `PATCH /workflows/{id}`, `GET /whatsapp/phone_numbers`,
> `POST /workflows/{id}/triggers`.

## FASE 5 — Módulo Notion (solo si lo activó)

Seguí `modules/notion/README.md`: crear la base/propiedades, conectar la
integración de Notion en Kapso, y wirear las tools `buscar_contacto` /
`registrar_contacto` al agente. La identidad SIEMPRE se ancla al `phone_number`
verificado por Meta, nunca a lo que el usuario afirme (regla de seguridad del
prompt). Salteá esta fase si `notion.activar` es false.

## FASE 6 — Test

1. Pedile a la persona que se mande un WhatsApp al número conectado (o usá el
   sandbox de Kapso) con una pregunta típica.
2. Verificá que el bot responda bien y en tono. Revisá las ejecuciones del workflow
   si algo falla.
3. Probá 2-3 casos: una FAQ, un mensaje vago ("hola"), y un caso de escalamiento.
   Ajustá la config y re-renderizá el prompt + `PATCH /workflows/{id}` con la
   definición actualizada si hace falta.

## FASE 7 — Cierre

- Resumí qué quedó andando y los límites del bot.
- Explicá cómo cambiar algo: "decime qué querés ajustar y lo actualizo" (vos
  editás la config y re-deployás). 
- Si está en Free, recordá los límites del plan (1 número, ~2.000 msg/mes) y que
  para crecer hay que subir de plan en Kapso.

---

## Notas técnicas (para vos, el agente)

- Base URL: `https://api.kapso.ai/platform/v1`. Auth: header `X-API-Key`.
- El `system_prompt` viaja **dentro** del JSON de la definición como string escapado.
- En `PATCH`, si mandás `definition.nodes`, es el set COMPLETO (lo omitido se borra).
  Para tocar solo metadata (ej. activar), no mandes `definition`.
- No hay "clonar workflow" nativo: el mecanismo de plantilla es renderizar esta
  plantilla y crear el workflow en la cuenta de la persona. Eso es esperado.
- Kapso no tiene RAG/knowledge-base nativo: por eso las FAQs van inyectadas en el
  system prompt. No busques una "subida de documentos".
