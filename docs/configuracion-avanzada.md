# Configuración Avanzada

## Conectar con Slack (paso a paso)

### 1. Crear una app en Slack

1. Andá a [https://api.slack.com/apps](https://api.slack.com/apps)
2. Hacé clic en **"Create New App"**
3. Elegí **"From scratch"**
4. Poné el nombre: `Event Networking Agent`
5. Seleccioná tu workspace y hacé clic en **"Create App"**

### 2. Configurar Incoming Webhooks

1. En el menú lateral, andá a **"Incoming Webhooks"**
2. Activá el toggle **"Activate Incoming Webhooks"**
3. Hacé clic en **"Add New Webhook to Workspace"**
4. Elegí el canal donde querés recibir las notificaciones (ej: `#eventos-networking`)
5. Hacé clic en **"Allow"**
6. Copiá la **Webhook URL** que aparece (tiene este formato):
   ```
   https://hooks.slack.com/services/TXXXXX/BXXXXX/XXXXXXXXXXXXXX
   ```

### 3. Configurar en n8n

1. Abrí el workflow en n8n
2. Agregá un nodo **"HTTP Request"** después del nodo "Formatear"
3. Configurá así:

| Campo | Valor |
|-------|-------|
| Method | POST |
| URL | Tu webhook URL de Slack |
| Body Type | JSON |

4. En el body, poné:

```json
{
  "blocks": [
    {
      "type": "header",
      "text": {
        "type": "plain_text",
        "text": "🎯 Nuevo evento de networking"
      }
    },
    {
      "type": "section",
      "fields": [
        {
          "type": "mrkdwn",
          "text": "*Evento:*\n{{ $json.titulo }}"
        },
        {
          "type": "mrkdwn",
          "text": "*Fecha:*\n{{ $json.fecha }}"
        },
        {
          "type": "mrkdwn",
          "text": "*Fuente:*\n{{ $json.fuente }}"
        }
      ]
    },
    {
      "type": "section",
      "text": {
        "type": "mrkdwn",
        "text": "{{ $json.descripcion }}"
      }
    },
    {
      "type": "actions",
      "elements": [
        {
          "type": "button",
          "text": {
            "type": "plain_text",
            "text": "Ver evento"
          },
          "url": "{{ $json.url }}"
        }
      ]
    }
  ]
}
```

5. Activá el nodo y listo — cada vez que se detecte un evento nuevo, te llega a Slack con formato rico (título, fecha, botón de link).

### Tip: crear un canal dedicado

Recomendamos crear un canal `#eventos-networking` separado para no mezclar con las conversaciones del equipo. Así podés silenciar notificaciones cuando no te interese y revisar todo junto cuando quieras planificar tu semana.

---

## Conectar con Discord

### 1. Crear webhook en Discord

1. Abrí tu servidor de Discord
2. Andá a **Server Settings** → **Integrations** → **Webhooks**
3. Hacé clic en **"New Webhook"**
4. Ponele nombre: `Event Agent`
5. Elegí el canal (ej: `#eventos`)
6. Copiá la **Webhook URL**

### 2. Configurar en n8n

1. Abrí el nodo **"Discord - Notificar"** en el workflow
2. Reemplazá `YOUR_DISCORD_WEBHOOK_URL` con tu URL
3. Habilitá el nodo (sacá el "disabled")
4. Listo

---

## Conectar con Email (Gmail)

### 1. En n8n

1. Abrí el nodo **"Email - Notificar"**
2. En credenciales, elegí **Gmail OAuth2**
3. Seguí el proceso de autenticación con tu cuenta de Gmail
4. En "Send To" poné tu email
5. Habilitá el nodo

---

## Filtros avanzados

### Excluir eventos spam

Agregá un nodo **"Filter"** después de "Filtrar Relevantes" con estas condiciones:

```
titulo NOT contains "MLM"
titulo NOT contains "crypto"
titulo NOT contains "forex"
titulo NOT contains "multinivel"
```

### Solo eventos gratuitos

Agregá al filtro:

```
is_free equals true
```

### Solo eventos presenciales

```
online_event equals false
```

---

## Cambiar zona horaria

Por defecto el schedule corre en UTC. Para Argentina (UTC-3):

1. Abrí el nodo **"Schedule Trigger"**
2. En **"Timezone"** seleccioná `America/Argentina/Buenos_Aires`
3. Ajustá la hora (8:00 AM Argentina = 11:00 UTC)
