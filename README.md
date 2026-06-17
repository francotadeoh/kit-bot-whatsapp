# 🤖 Kit Bot WhatsApp

Armá tu propio **asistente de WhatsApp** — de atención al cliente, asesor de ventas,
o las dos cosas — sin programar y sin pelearte con interfaces. Respondés unas
preguntas sobre tu negocio y el kit deja el bot andando por vos.

Funciona sobre [Kapso](https://kapso.ai) (la plataforma que conecta tu número de
WhatsApp con la IA) y se instala conversando con [Claude Code](https://claude.com/claude-code).

---

## ¿Qué hace el bot?

- **Atiende, asesora o vende** según lo que necesites (vos elegís el tipo y el kit lo adapta).
- Responde con **lo que sabe de tu negocio** 24/7 (FAQs, info de producto, lo que cargues).
- Mantiene tu **tono** y tus reglas (qué puede y qué no puede decir).
- **Deriva a una persona** cuando algo lo excede (un reclamo, cerrar una venta, un caso especial).
- (Opcional) **Recuerda a cada cliente** y registra las conversaciones en tu Notion.

## ¿Qué necesitás?

1. La **app de Claude Code** (escritorio) — no hace falta saber usar la terminal.
2. Una cuenta gratis en **Kapso** + tu número de WhatsApp.
3. ~15 minutos.

Hay **3 pasos que sí o sí hacés vos** (el resto lo hace el kit):
crear la cuenta de Kapso, conectar tu número (un login de Facebook, ~5 min), y
—cuando crezcas— cargar créditos. El plan gratis de Kapso alcanza para arrancar
(1 número + ~2.000 mensajes/mes).

## 🚀 Creá tu bot en 3 pasos

**1. Copiá este mensaje y pegáselo a Claude Code** (después le podés contestar por **audio**):

> Instalá el kit de este repo y guiame para armar mi bot de WhatsApp paso a paso:
> https://github.com/francotadeoh/kit-bot-whatsapp

**2. Respondé las preguntas.** Claude te va guiando: qué tipo de bot querés (atención,
ventas o mixto), conectar tu número de WhatsApp y contarle de tu negocio. Él arma,
conecta y publica todo por vos.

**3. Probá tu bot.** Mandале un WhatsApp y mirá cómo responde. ¿Algo no te cierra?
Decíselo a Claude y lo ajusta al toque.

Eso es todo. Para cambiar algo más adelante (sumar una respuesta, cambiar el tono),
abrí Claude Code y decile qué querés ajustar.

> 💡 **Atajo técnico** (opcional): corré `npx skills add francotadeoh/kit-bot-whatsapp`
> y después decile a Claude *"armá mi bot de WhatsApp"*.

> **¿Hiciste el programa La Instalación?** Si ya tenés tu negocio ordenado en Notion
> (Centro de Comando) y un procedimiento escrito, decíselo al kit cuando te pregunte:
> usa eso de base — tus procesos se vuelven las respuestas del bot y tu Centro de
> Comando se vuelve su memoria. No empezás de cero.

## Para Claude (si alguien te pasó este link)

Si una persona te compartió este repositorio y te pidió ayuda para instalarlo o armar
su bot de WhatsApp:

1. Ejecutá `npx skills add francotadeoh/kit-bot-whatsapp` (instala este kit; y, si
   faltan, los skills oficiales de Kapso `gokapso/agent-skills`).
2. Activá el skill **`kit-bot-whatsapp`** y seguí su `SKILL.md` al pie.
3. Guiá a la persona **paso a paso, cero-técnico, una pregunta a la vez**. Puede
   contestarte por audio. No le pidas que edite archivos ni que escriba comandos: la
   parte técnica la hacés vos.

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

## Alcance y límites

- Sirve para **atención al cliente, asesoramiento de ventas o mixto**. Lo que el bot
  hace bien es **conversar, informar, asesorar y derivar** — el cierre final
  (precio, pago, contrato) lo hace una persona del equipo.
- La base de conocimiento es lo que vos le cargás (FAQs, info de producto): cuanto
  más completa, mejor responde. No inventa lo que no sabe — lo deriva.

---

Hecho para que cualquiera pueda tener su asistente de WhatsApp. Si te sirve, dejá una
⭐ y compartilo.
