# Troubleshooting

## Problemas comunes

### "401 Unauthorized" en Eventbrite

- Verificá que tu token esté activo en [Eventbrite API Keys](https://www.eventbrite.com/platform/api-keys)
- El token tiene que ir con el prefijo `Bearer`: `Bearer tu_token_aqui`

### No llegan notificaciones a Discord/Slack

1. Verificá que el nodo no esté deshabilitado (aparece en gris)
2. Verificá que la webhook URL sea correcta
3. Probá la URL manualmente con curl:
   ```bash
   curl -X POST "TU_WEBHOOK_URL" -H "Content-Type: application/json" -d '{"content":"Test"}'
   ```

### El workflow no se ejecuta

- Verificá que esté **Active** (toggle verde arriba a la derecha)
- Revisá el log de ejecuciones en n8n
- Si usás n8n Cloud free, verificá que no hayas excedido el límite de ejecuciones

### Eventos duplicados

El nodo "Merge + Deduplicar" filtra por nombre. Si aparecen duplicados, revisá que los nombres sean exactamente iguales (incluyendo mayúsculas y espacios).

### Rate limiting de APIs

- Eventbrite: 2000 requests/hora (más que suficiente)
- Meetup: ~200 requests/hora en plan free
- Si te bloquean, aumentá el intervalo del Schedule Trigger

## Soporte

Si tenés un problema que no está acá, [abrí un issue](https://github.com/hola684/event-networking-agent/issues).
