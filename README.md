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
3. ~15 minutos.

Hay **3 pasos que sí o sí hacés vos** (el resto lo hace el kit):
crear la cuenta de Kapso, conectar tu número (un login de Facebook, ~5 min), y
—cuando crezcas— cargar créditos. El plan gratis de Kapso alcanza para arrancar
(1 número + ~2.000 mensajes/mes).

## Cómo se usa

**Un comando, y después solo hablás.**

1. Instalá el kit (copiá y pegá esta línea una vez):
   ```bash
   npx skills add francotadeoh/kit-bot-whatsapp
   ```
2. Abrí Claude Code y decile (podés por **audio**): **"armá mi bot de WhatsApp"**.
3. Seguí la conversación: el kit instala solo lo que necesita de Kapso, te guía para
   conectar tu número, te **entrevista sobre tu negocio** (le dictás, él arma la
   config), construye el bot, lo conecta y lo prueba con vos.

Eso es todo. Para cambiar algo después (sumar una FAQ, cambiar el tono), volvé a
abrir Claude Code y decile qué querés ajustar.

> **¿Hiciste el programa La Instalación?** Si ya tenés tu negocio ordenado en Notion
> (Centro de Comando) y un procedimiento de atención escrito, decíselo al kit cuando
> te pregunte: usa eso de base — tus procesos se vuelven las respuestas del bot y tu
> Centro de Comando se vuelve su memoria. No empezás de cero.

## Qué hay adentro

```
kit-bot-whatsapp/
├─ README.md
└─ skills/
   └─ kit-bot-whatsapp/          # el skill instalable (autocontenido)
      ├─ SKILL.md                # el instalador conversacional
      ├─ config/
      │  └─ mi-negocio.example.yaml
      ├─ template/
      │  ├─ system_prompt.template.md   # el "cerebro" del bot
      │  └─ workflow.definition.json    # la estructura del bot en Kapso
      └─ modules/
         └─ notion/              # módulo opcional: memoria + CRM
```

Por debajo, el kit usa los **skills oficiales de Kapso**
(`gokapso/agent-skills`) para la parte técnica; los instala solo.

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
