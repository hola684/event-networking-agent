# Event Networking Agent

Agente de IA que monitorea automáticamente eventos de networking, tecnología, emprendimiento y negocios en Argentina. Recibe notificaciones diarias en Discord, Slack o email con los próximos eventos relevantes para tu industria.

![Event Networking Agent](https://www.wemakeitlab.com/images/claude-code-resource.webp)

## Qué hace

Este agente se conecta a múltiples fuentes de eventos y te envía un resumen diario con los eventos más relevantes para hacer networking, aprender y conectar con otros profesionales.

### Fuentes que monitorea

| Fuente | Tipo de eventos |
|--------|----------------|
| **Eventbrite** | Tech meetups, conferencias, workshops |
| **AllEvents.in** | Networking PyMEs, seminarios, capacitaciones |
| **Meetup** | Grupos tech, startups, desarrollo profesional |

### Ejemplo de notificación

```
Eventos-Networking [08:00]

Nuevo evento: Buenos Aires Tech Mixer and Social (Tech / AI / Data / IT)
Fecha: 2026-08-21
Fuente: Eventbrite
Descripción: Join us at our TECH MIXER AND SOCIAL for afterwork drinks,
networking with tech / IT workers and connect with others in tech

Nuevo evento: eCommerce Day Argentina 2026
Fecha: 2026-08-27
Fuente: Eventbrite
Descripción: eCommerce Day es el principal encuentro de la industria
de los Negocios por Internet.

Recordatorio: evento en 3 días
Evento: Silicon Valley Business Networking (Online)
Fecha: 2026-08-17
Fuente: Meetup
```

## Requisitos

- [n8n](https://n8n.io/) (self-hosted o cloud) — orquestador de workflows
- Cuenta gratuita en [Eventbrite](https://www.eventbrite.com/) (para API key)
- Canal de Discord, Slack o email para recibir notificaciones
- Node.js 18+ (solo si corrés n8n self-hosted)

## Instalación paso a paso

### 1. Cloná el repositorio

```bash
git clone https://github.com/hola684/event-networking-agent.git
cd event-networking-agent
```

### 2. Importá el workflow en n8n

1. Abrí tu instancia de n8n (cloud o self-hosted)
2. Andá a **Workflows** → **Import from file**
3. Seleccioná el archivo `workflow/event-networking-agent.json`
4. El workflow se importa con todos los nodos pre-configurados

### 3. Configurá las credenciales

#### Eventbrite API

1. Andá a [Eventbrite API Keys](https://www.eventbrite.com/platform/api-keys)
2. Creá una nueva app y copiá tu **Private Token**
3. En n8n, abrí el nodo "Eventbrite" y pegá el token

#### Discord (opcional)

1. Creá un webhook en tu servidor de Discord: **Server Settings** → **Integrations** → **Webhooks**
2. Copiá la URL del webhook
3. Pegala en el nodo "Discord" del workflow

#### Slack (opcional)

1. Creá una app en [Slack API](https://api.slack.com/apps)
2. Agregá el scope `chat:write` y `incoming-webhook`
3. Instalá la app en tu workspace
4. Copiá el webhook URL y pegalo en el nodo "Slack"

#### Email (opcional)

1. En el nodo "Send Email", configurá tus credenciales SMTP o Gmail OAuth
2. Definí el email destino donde querés recibir las notificaciones

### 4. Personalizá las búsquedas

Abrí el nodo **"Configuración"** y editá:

```json
{
  "keywords": [
    "networking",
    "tech",
    "startup",
    "emprendimiento",
    "pymes",
    "marketing digital",
    "inteligencia artificial",
    "ecommerce"
  ],
  "location": "Buenos Aires, Argentina",
  "radius": "50km",
  "language": "es",
  "maxResults": 20
}
```

Podés agregar o quitar keywords según tu industria. El agente busca eventos que matcheen al menos una keyword.

### 5. Activá el workflow

1. Hacé clic en el toggle **"Active"** en la esquina superior derecha
2. El workflow se ejecuta automáticamente todos los días a las 8:00 AM (configurable)
3. Vas a recibir las notificaciones en el canal que configuraste

## Estructura del proyecto

```
event-networking-agent/
├── README.md                              # Esta documentación
├── workflow/
│   └── event-networking-agent.json        # Workflow n8n importable
├── docs/
│   ├── configuracion-avanzada.md          # Filtros, horarios, multi-ciudad
│   └── troubleshooting.md                 # Problemas comunes y soluciones
└── examples/
    ├── discord-notification.png           # Ejemplo de notificación Discord
    └── email-notification.png             # Ejemplo de notificación email
```

## Configuración avanzada

### Cambiar la frecuencia

Por defecto el agente corre **2 veces al día** (8:00 y 17:00). Para cambiarlo, editá el nodo **"Schedule Trigger"**:

- **Cada hora**: `0 * * * *`
- **Una vez al día (9 AM)**: `0 9 * * *`
- **Solo días de semana**: `0 8 * * 1-5`

### Filtrar por categoría

Podés filtrar eventos por categoría de Eventbrite:

| Categoría | ID |
|-----------|-----|
| Business & Professional | 101 |
| Science & Technology | 102 |
| Music | 103 |
| Education | 115 |

Agregá el `category_id` en el nodo de Eventbrite para filtrar.

### Multi-ciudad

Para monitorear eventos en múltiples ciudades, duplicá el nodo de búsqueda y cambiá el parámetro `location`:

```json
{
  "locations": [
    { "city": "Buenos Aires", "country": "AR" },
    { "city": "Córdoba", "country": "AR" },
    { "city": "Rosario", "country": "AR" },
    { "city": "Mendoza", "country": "AR" }
  ]
}
```

### Recordatorios

El agente envía recordatorios automáticos **3 días antes** y **1 día antes** de cada evento guardado. Para cambiar esto, editá el nodo **"Check Reminders"**.

## Cómo funciona internamente

```
Schedule Trigger (8:00 AM / 17:00 PM)
    │
    ├── Eventbrite API → Buscar eventos por keywords + ubicación
    ├── AllEvents.in → Scraping de eventos locales
    └── Meetup API → Grupos y eventos cercanos
    │
    ▼
Merge + Deduplicar (por título + fecha)
    │
    ▼
Filtrar (keywords, fecha, relevancia)
    │
    ▼
Formatear notificación
    │
    ├── Discord webhook
    ├── Slack webhook
    └── Email SMTP/Gmail
```

## FAQ

**¿Es gratis?**
Sí. El agente es open source. Solo necesitás una instancia de n8n (el plan gratuito de n8n Cloud alcanza) y las APIs gratuitas de Eventbrite/Meetup.

**¿Funciona fuera de Argentina?**
Sí. Cambiá el parámetro `location` en la configuración para cualquier ciudad del mundo.

**¿Puedo agregar más fuentes?**
Sí. El workflow está diseñado para ser extensible. Podés agregar nodos para Lu.ma, LinkedIn Events, Facebook Events, etc.

**¿Necesito saber programar?**
No. Todo se configura visualmente desde n8n. Solo necesitás pegar API keys y configurar los filtros.

**¿Cómo evito eventos spam o irrelevantes?**
El nodo "Filtrar" tiene reglas de exclusión. Podés agregar palabras a la lista negra (ej: "MLM", "crypto scam") para filtrar automáticamente.

## Soporte

- **Issues**: [Reportá un problema](https://github.com/hola684/event-networking-agent/issues)
- **Web**: [wemakeitlab.com](https://www.wemakeitlab.com)
- **WhatsApp**: [Contactanos](https://wa.me/5491156684895?text=Hola,%20tengo%20una%20consulta%20sobre%20el%20Event%20Networking%20Agent)

## Hecho por

**[We Make It Lab](https://www.wemakeitlab.com)** — Growth marketing agency. Automatización, paid media, desarrollo web y agentes de IA.

## Licencia

MIT License — usalo, modificalo, compartilo.
