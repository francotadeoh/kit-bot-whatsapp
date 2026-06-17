# 🤖 Kit Bot WhatsApp

Armá tu propio **asistente de WhatsApp para atención al cliente** — sin programar y
sin pelearte con interfaces. Respondés unas preguntas sobre tu negocio y el kit deja
el bot andando por vos.

Funciona sobre [Kapso](https://kapso.ai) (la plataforma que conecta tu número de
WhatsApp con la IA) y se instala conversando con [Claude Code](https://claude.com/claude-code).

---

## ¿Qué hace el bot?

- Responde las **preguntas frecuentes** de tu negocio 24/7.
- Mantiene tu **tono** y tus reglas (qué puede y qué no puede decir).
- **Deriva a una persona** cuando algo lo excede (un reclamo, un pedido especial).
- (Opcional) **Recuerda a cada cliente** y registra las conversaciones en tu Notion.

## ¿Qué necesitás?

1. La **app de Claude Code** (escritorio) — no hace falta saber usar la terminal.
2. Una cuenta gratis en **Kapso** + tu número de WhatsApp.
3. 15 minutos.

Hay **3 pasos que sí o sí hacés vos** (el resto lo hace el kit):
crear la cuenta de Kapso, conectar tu número (un login de Facebook, ~5 min), y
—cuando crezcas— cargar créditos. El plan gratis de Kapso alcanza para arrancar
(1 número + ~2.000 mensajes/mes).

## Cómo se usa

1. **Cloná o descargá este repo** y abrilo con Claude Code.
2. Pedile: **"armá mi bot de WhatsApp"** (o corré el skill `kit-bot-whatsapp`).
3. Seguí la conversación: el kit te guía para conectar tu número, te entrevista
   sobre tu negocio, construye el bot y lo prueba con vos.

Eso es todo. Para cambiar algo después (sumar una FAQ, cambiar el tono), volvé a
abrir Claude Code y decile qué querés ajustar.

## Qué hay adentro

```
kit-bot-whatsapp/
├─ README.md
├─ .claude/skills/kit-bot-whatsapp/SKILL.md   # el instalador conversacional
├─ config/
│  └─ mi-negocio.example.yaml                  # ejemplo de la configuración
├─ template/
│  ├─ system_prompt.template.md               # el "cerebro" del bot (plantilla)
│  └─ workflow.definition.json                # la estructura del bot en Kapso
└─ modules/
   └─ notion/                                  # módulo opcional: memoria + CRM
```

## Privacidad y seguridad

- Tu **API key de Kapso** queda en un archivo `.env` local que **no se sube** a
  ningún lado (está en `.gitignore`).
- El bot solo atiende a quien le escribe: la identidad se ancla al número
  verificado por WhatsApp/Meta, no a lo que el usuario diga ser. Nadie puede pedirle
  datos de otro cliente haciéndose pasar por otra persona.

## Límites de la v1

- Pensado para **atención al cliente** (responder, orientar, derivar). No toma pagos
  ni cierra ventas por sí solo.
- La base de conocimiento son tus FAQs (cuantas más cargues, mejor atiende).

---

Hecho para que cualquiera pueda tener su asistente de WhatsApp. Si te sirve, dejá una
⭐ y compartilo.
