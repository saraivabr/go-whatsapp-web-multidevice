# Documentação de Payload do Webhook

Este documento fornece documentação abrangente para a estrutura de payload do webhook usada pelo aplicativo Go WhatsApp Web Multidevice.

## Visão Geral

O sistema de webhook envia requisições HTTP POST para URLs configuradas sempre que eventos do WhatsApp ocorrem. Cada requisição de webhook inclui dados do evento em formato JSON e cabeçalhos de segurança para verificação.

## Segurança

### Verificação de Assinatura HMAC

Todas as requisições de webhook incluem uma assinatura HMAC SHA256 para verificação de segurança:

- **Cabeçalho**: `X-Hub-Signature-256`
- **Formato**: `sha256={assinatura}`
- **Algoritmo**: HMAC SHA256
- **Segredo Padrão**: `secret` (configurável via `--webhook-secret` ou `WHATSAPP_WEBHOOK_SECRET`)

### Exemplo de Verificação (Node.js)

```javascript
const crypto = require('crypto');

function verifyWebhookSignature(payload, signature, secret) {
    const expectedSignature = crypto
        .createHmac('sha256', secret)
        .update(payload, 'utf8')
        .digest('hex');

    const receivedSignature = signature.replace('sha256=', '');
    return crypto.timingSafeEqual(
        Buffer.from(expectedSignature, 'hex'),
        Buffer.from(receivedSignature, 'hex')
    );
}
```

### Exemplo de Verificação (Python)

```python
import hmac
import hashlib

def verify_webhook_signature(payload, signature, secret):
    expected_signature = hmac.new(
        secret.encode('utf-8'),
        payload,
        hashlib.sha256
    ).hexdigest()
    
    received_signature = signature.replace('sha256=', '')
    return hmac.compare_digest(expected_signature, received_signature)
```

## Campos Comuns de Payload

Todos os payloads de webhook compartilham estes campos comuns:

| **Campo**   | **Tipo** | **Descrição**                                                      |
|-------------|----------|--------------------------------------------------------------------|
| `sender_id` | string   | Parte de usuário do JID do remetente (número de telefone, sem `@s.whatsapp.net`) |
| `chat_id`   | string   | Parte de usuário do JID do chat                                    |
| `from`      | string   | JID completo do remetente (ex: `628123456789@s.whatsapp.net`)     |
| `timestamp` | string   | Timestamp formatado RFC3339 (ex: `2023-10-15T10:30:00Z`)          |
| `pushname`  | string   | Nome de exibição do remetente                                      |

## Eventos de Mensagem

### Mensagem de Texto

```json
{
  "sender_id": "628123456789",
  "chat_id": "628987654321",
  "from": "628123456789@s.whatsapp.net",
  "timestamp": "2023-10-15T10:30:00Z",
  "pushname": "João Silva",
  "message": {
    "text": "Olá, como vai?",
    "id": "3EB0C127D7BACC83D6A1",
    "replied_id": "",
    "quoted_message": ""
  }
}
```

### Mensagem de Resposta

```json
{
  "sender_id": "628123456789",
  "chat_id": "628987654321",
  "from": "628123456789@s.whatsapp.net",
  "timestamp": "2023-10-15T10:35:00Z",
  "pushname": "João Silva",
  "message": {
    "text": "Estou ótimo, obrigado!",
    "id": "3EB0C127D7BACC83D6A2",
    "replied_id": "3EB0C127D7BACC83D6A1",
    "quoted_message": "Olá, como vai?"
  }
}
```

### Mensagem de Reação

```json
{
  "sender_id": "628123456789",
  "chat_id": "628987654321",
  "from": "628123456789@s.whatsapp.net",
  "timestamp": "2023-10-15T10:40:00Z",
  "pushname": "João Silva",
  "reaction": {
    "message": "👍",
    "id": "3EB0C127D7BACC83D6A1"
  },
  "message": {
    "text": "",
    "id": "88760C69D1F35FEB239102699AE9XXXX",
    "replied_id": "",
    "quoted_message": ""
  }
}
```

## Eventos de Confirmação de Leitura

Eventos de confirmação de leitura são acionados quando mensagens recebem confirmações como confirmações de entrega e recibos de leitura.
Esses eventos usam o tipo de evento `message.ack` e fornecem informações sobre mudanças de status de mensagem.

### Mensagem Entregue

Acionado quando uma mensagem é entregue com sucesso ao dispositivo do destinatário.

```json
{
  "event": "message.ack",
  "payload": {
    "chat_id": "120363402106XXXXX@g.us",
    "from": "6289685XXXXXX@s.whatsapp.net in 120363402106XXXXX@g.us",
    "ids": [
      "3EB00106E8BE0F407E88EC"
    ],
    "receipt_type": "delivered",
    "receipt_type_description": "significa que a mensagem foi entregue ao dispositivo (mas o usuário pode não ter notado).",
    "sender_id": "6289685XXXXXX@s.whatsapp.net"
  },
  "timestamp": "2025-07-18T22:44:20Z"
}
```

### Mensagem Lida

Acionado quando uma mensagem é lida pelo destinatário (eles abriram o chat e viram a mensagem).

```json
{
  "event": "message.ack",
  "payload": {
    "chat_id": "120363402106XXXXX@g.us",
    "from": "6289685XXXXXX@s.whatsapp.net in 120363402106XXXXX@g.us",
    "ids": [
      "3EB00106E8BE0F407E88EC"
    ],
    "receipt_type": "read",
    "receipt_type_description": "o usuário abriu o chat e viu a mensagem.",
    "sender_id": "6289685XXXXXX@s.whatsapp.net"
  },
  "timestamp": "2025-07-18T22:44:44Z"
}
```

### Campos de Evento de Confirmação

| **Campo**                          | **Tipo** | **Descrição**                                              |
|------------------------------------|----------|------------------------------------------------------------|
| `event`                            | string   | Sempre `"message.ack"` para eventos de confirmação         |
| `payload.chat_id`                  | string   | Identificador do chat (grupo ou chat individual)           |
| `payload.from`                     | string   | Informações do remetente com contexto do chat              |
| `payload.ids`                      | array    | Array de IDs de mensagem que receberam a confirmação       |
| `payload.receipt_type`             | string   | Tipo de confirmação: `"delivered"`, `"read"`, etc.         |
| `payload.receipt_type_description` | string   | Descrição legível do tipo de confirmação                   |
| `payload.sender_id`                | string   | JID do remetente da mensagem                               |
| `timestamp`                        | string   | Timestamp formatado RFC3339 quando a confirmação foi recebida |

## Eventos de Grupo

Eventos de grupo são acionados quando os metadados do grupo mudam, incluindo eventos de entrada/saída de membros, promoções/rebaixamentos de administradores e atualizações de configurações do grupo. Esses eventos usam o tipo de evento `group.participants` e fornecem informações abrangentes sobre mudanças no grupo.

### Entrada de Membro no Grupo

Acionado quando usuários entram ou são adicionados a um grupo.

```json
{
  "event": "group.participants",
  "payload": {
    "chat_id": "120363402106XXXXX@g.us",
    "type": "join",
    "jids": [
      "6289685XXXXXX@s.whatsapp.net",
      "6289686YYYYYY@s.whatsapp.net"
    ]
  },
  "timestamp": "2025-07-28T10:30:00Z"
}
```

### Saída de Membro do Grupo

Acionado quando usuários saem ou são removidos de um grupo.

```json
{
  "event": "group.participants",
  "payload": {
    "chat_id": "120363402106XXXXX@g.us",
    "type": "leave",
    "jids": [
      "6289687ZZZZZZ@s.whatsapp.net"
    ]
  },
  "timestamp": "2025-07-28T10:32:00Z"
}
```

### Promoção de Membro do Grupo

Acionado quando usuários são promovidos a administradores.

```json
{
  "event": "group.participants",
  "payload": {
    "chat_id": "120363402106XXXXX@g.us",
    "type": "promote",
    "jids": [
      "6289688AAAAAA@s.whatsapp.net"
    ]
  },
  "timestamp": "2025-07-28T10:33:00Z"
}
```

### Rebaixamento de Membro do Grupo

Acionado quando usuários são rebaixados de administrador.

```json
{
  "event": "group.participants",
  "payload": {
    "chat_id": "120363402106XXXXX@g.us",
    "type": "demote",
    "jids": [
      "6289689BBBBBB@s.whatsapp.net"
    ]
  },
  "timestamp": "2025-07-28T10:34:00Z"
}
```

### Campos de Evento de Grupo

| **Campo**         | **Tipo** | **Descrição**                                                 |
|-------------------|----------|---------------------------------------------------------------|
| `event`           | string   | Sempre `"group.participants"` para eventos de grupo          |
| `payload.chat_id` | string   | Identificador do grupo (ex: `"120363402106XXXXX@g.us"`)      |
| `payload.type`    | string   | Tipo de ação: `"join"`, `"leave"`, `"promote"` ou `"demote"` |
| `payload.jids`    | array    | Array de JIDs de usuário afetados por esta ação              |
| `timestamp`       | string   | Timestamp formatado RFC3339 quando o evento do grupo ocorreu |

## Mensagens de Mídia

### Mensagem de Imagem

```json
{
  "sender_id": "628123456789",
  "chat_id": "628123456789",
  "from": "628123456789@s.whatsapp.net",
  "timestamp": "2025-07-13T11:05:51Z",
  "pushname": "João Silva",
  "message": {
    "text": "",
    "id": "********************",
    "replied_id": "",
    "quoted_message": ""
  },
  "image": {
    "media_path": "statics/media/1752404751-ad9e37ac-c658-4fe5-8d25-ba4a3f4d58fd.jpe",
    "mime_type": "image/jpeg",
    "caption": "Veja esta foto"
  }
}
```

### Mensagem de Vídeo

```json
{
  "sender_id": "628123456789",
  "chat_id": "628123456789",
  "from": "628123456789@s.whatsapp.net",
  "timestamp": "2025-07-13T11:07:24Z",
  "pushname": "Sistema de Notificação",
  "message": {
    "text": "",
    "id": "********************",
    "replied_id": "",
    "quoted_message": ""
  },
  "video": {
    "media_path": "statics/media/1752404845-b9393cd1-8546-4df9-8a60-ee3276036aba.m4v",
    "mime_type": "video/mp4",
    "caption": "Ok"
  }
}
```

### Mensagem de Áudio

```json
{
  "sender_id": "628123456789",
  "chat_id": "628987654321",
  "from": "628123456789@s.whatsapp.net",
  "timestamp": "2023-10-15T10:55:00Z",
  "pushname": "João Silva",
  "message": {
    "text": "",
    "id": "3EB0C127D7BACC83D6A5",
    "replied_id": "",
    "quoted_message": ""
  },
  "audio": {
    "media_path": "statics/media/1752404905-b9393cd1-8546-4df9-8a60-ee3276036aba.m4v",
    "mime_type": "audio/ogg",
    "caption": "Ok"
  }
}
```

### Mensagem de Documento

```json
{
  "sender_id": "628123456789",
  "chat_id": "628987654321",
  "from": "628123456789@s.whatsapp.net",
  "timestamp": "2023-10-15T11:00:00Z",
  "pushname": "João Silva",
  "message": {
    "text": "",
    "id": "3EB0C127D7BACC83D6A6",
    "replied_id": "",
    "quoted_message": ""
  },
  "document": {
    "media_path": "statics/media/1752404965-b9393cd1-8546-4df9-8a60-ee3276036aba.m4v",
    "mime_type": "application/pdf",
    "caption": "Ok"
  }
}
```

### Mensagem de Sticker

```json
{
  "chat_id": "628968XXXXXXXX",
  "from": "628968XXXXXXXX@s.whatsapp.net",
  "message": {
    "text": "",
    "id": "446AC2BAF2061B53E24CA526DBDFBD4E",
    "replied_id": "",
    "quoted_message": ""
  },
  "pushname": "Aldino Kemal",
  "sender_id": "628968XXXXXXXX",
  "sticker": {
    "media_path": "statics/media/1752404986-ff2464a6-c54c-4e6c-afde-c4c925ce3573.webp",
    "mime_type": "image/webp",
    "caption": ""
  },
  "timestamp": "2025-07-13T11:09:45Z"
}
```

## Tipos Especiais de Mensagem

### Mensagem de Contato

```json
{
  "chat_id": "6289XXXXXXXXX",
  "contact": {
    "displayName": "3Care",
    "vcard": "BEGIN:VCARD\nVERSION:3.0\nN:;3Care;;;\nFN:3Care\nTEL;type=Mobile:+62 132\nEND:VCARD",
    "contextInfo": {
      "expiration": 7776000,
      "ephemeralSettingTimestamp": 1751808692,
      "disappearingMode": {
        "initiator": 0,
        "trigger": 1,
        "initiatedByMe": true
      }
    }
  },
  "from": "6289XXXXXXXXX@s.whatsapp.net",
  "message": {
    "text": "",
    "id": "56B3DFF4994284634E7AAFEEF6F1A0A2",
    "replied_id": "",
    "quoted_message": ""
  },
  "pushname": "Aldino Kemal",
  "sender_id": "6289XXXXXXXXX",
  "timestamp": "2025-07-13T11:10:19Z"
}
```

### Mensagem de Localização

```json
{
  "sender_id": "628123456789",
  "chat_id": "628987654321",
  "from": "João Silva",
  "timestamp": "2023-10-15T11:15:00Z",
  "pushname": "João Silva",
  "message": {
    "text": "",
    "id": "3EB0C127D7BACC83D6A9",
    "replied_id": "",
    "quoted_message": ""
  },
  "location": {
    "degreesLatitude": -23.5505,
    "degreesLongitude": -46.6333,
    "name": "São Paulo, Brasil",
    "address": "São Paulo, SP, Brasil"
  }
}
```

### Mensagem de Localização ao Vivo

```json
{
  "chat_id": "6289XXXXXXXXX",
  "from": "6289XXXXXXXXX@s.whatsapp.net",
  "location": {
    "degreesLatitude": -7.8050297,
    "degreesLongitude": 110.4549165,
    "JPEGThumbnail": "miniatura_imagem_base64",
    "contextInfo": {
      "expiration": 7776000,
      "ephemeralSettingTimestamp": 1751808692,
      "disappearingMode": {
        "initiator": 0,
        "trigger": 1,
        "initiatedByMe": true
      }
    }
  },
  "message": {
    "text": "",
    "id": "94D13237B4D7F33EE4A63228BBD79EC0",
    "replied_id": "",
    "quoted_message": ""
  },
  "pushname": "Aldino Kemal",
  "sender_id": "6289685XXXXXX",
  "timestamp": "2025-07-13T11:11:22Z"
}
```

## Mensagens de Protocolo

### Mensagem Revogada

```json
{
  "action": "message_revoked",
  "chat_id": "6289XXXXXXXXX",
  "from": "6289XXXXXXXXX@s.whatsapp.net",
  "message": {
    "text": "",
    "id": "F4062F2BBCB19B7432195AD7080DA4E2",
    "replied_id": "",
    "quoted_message": ""
  },
  "pushname": "Aldino Kemal",
  "revoked_chat": "6289XXXXXXXXX@s.whatsapp.net",
  "revoked_from_me": true,
  "revoked_message_id": "94D13237B4D7F33EE4A63228BBD79EC0",
  "sender_id": "6289XXXXXXXXX",
  "timestamp": "2025-07-13T11:13:30Z"
}
```

### Mensagem Editada

Quando uma mensagem é editada, o webhook inclui o ID da mensagem original para rastrear qual mensagem foi modificada.

```json
{
  "action": "message_edited",
  "chat_id": "6289XXXXXXXXX",
  "original_message_id": "94D13237B4D7F33EE4A63228BBD79EC0",
  "edited_text": "Olá mundo editado",
  "from": "6289XXXXXXXXX@s.whatsapp.net",
  "message": {
    "text": "Olá mundo editado",
    "id": "D6271D8223A05B4DA6AE9FE3CD632543",
    "replied_id": "",
    "quoted_message": ""
  },
  "pushname": "Aldino Kemal",
  "sender_id": "6289XXXXXXXXX",
  "timestamp": "2025-07-13T11:14:19Z"
}
```

**Campos:**
- `original_message_id`: O ID da mensagem que foi editada (use isto para atualizar a mensagem correta no seu banco de dados)
- `edited_text`: O novo conteúdo de texto após a edição
- `message.id`: O ID do evento de edição em si (diferente do ID da mensagem original)

## Flags Especiais

### Mensagem de Visualização Única

```json
{
  "sender_id": "628123456789",
  "chat_id": "628987654321",
  "from": "João Silva",
  "timestamp": "2023-10-15T11:40:00Z",
  "pushname": "João Silva",
  "message": {
    "text": "",
    "id": "3EB0C127D7BACC83D6B2",
    "replied_id": "",
    "quoted_message": ""
  },
  "image": {
    "media_path": "statics/media/1752405060-b9393cd1-8546-4df9-8a60-ee3276036aba.m4v",
    "mime_type": "image/jpeg",
    "caption": "Ok"
  },
  "view_once": true
}
```

### Mensagem Encaminhada

```json
{
  "sender_id": "628123456789",
  "chat_id": "628987654321",
  "from": "João Silva",
  "timestamp": "2023-10-15T11:45:00Z",
  "pushname": "João Silva",
  "message": {
    "text": "Esta é uma mensagem encaminhada",
    "id": "3EB0C127D7BACC83D6B3",
    "replied_id": "",
    "quoted_message": ""
  },
  "forwarded": true
}
```

## Guia de Integração

### Configurando o Endpoint de Webhook

1. **Configure a(s) URL(s) de webhook**:

   ```bash
   ./whatsapp rest --webhook="https://seuapp.com/webhook"
   ```

2. **Defina o segredo do webhook**:

   ```bash
   ./whatsapp rest --webhook-secret="sua-chave-secreta"
   ```

3. **Múltiplos webhooks**:

   ```bash
   ./whatsapp rest --webhook="https://app1.com/webhook,https://app2.com/webhook"
   ```

### Implementação do Endpoint de Webhook (Express.js)

```javascript
const express = require('express');
const crypto = require('crypto');
const app = express();

app.use(express.raw({type: 'application/json'}));

app.post('/webhook', (req, res) => {
    const signature = req.headers['x-hub-signature-256'];
    const payload = req.body;
    const secret = 'sua-chave-secreta';

    // Verificar assinatura
    if (!verifyWebhookSignature(payload, signature, secret)) {
        return res.status(401).send('Não autorizado');
    }

    // Parsear e processar dados do webhook
    const data = JSON.parse(payload);
    console.log('Webhook recebido:', data);

    // Lidar com diferentes tipos de evento
    if (data.event === 'message.ack') {
        console.log(`Mensagem ${data.payload.receipt_type}:`, {
            chat_id: data.payload.chat_id,
            message_ids: data.payload.ids,
            description: data.payload.receipt_type_description
        });
    } else if (data.event === 'group.participants') {
        console.log(`Evento ${data.payload.type} do grupo:`, {
            chat_id: data.payload.chat_id,
            type: data.payload.type,
            affected_users: data.payload.jids
        });
        
        // Lidar com ações específicas do grupo
        switch (data.payload.type) {
            case 'join':
                console.log(`${data.payload.jids.length} usuários entraram no grupo ${data.payload.chat_id}`);
                // Saudar novos membros automaticamente
                data.payload.jids.forEach(jid => {
                    console.log(`Bem-vindo ${jid} ao grupo!`);
                });
                break;
            case 'leave':
                console.log(`${data.payload.jids.length} usuários saíram do grupo ${data.payload.chat_id}`);
                // Atualizar banco de dados de membros
                break;
            case 'promote':
                console.log(`${data.payload.jids.length} usuários promovidos no grupo ${data.payload.chat_id}`);
                // Notificar sobre novos administradores
                break;
            case 'demote':
                console.log(`${data.payload.jids.length} usuários rebaixados no grupo ${data.payload.chat_id}`);
                // Lidar com remoção de administrador
                break;
        }
    } else if (data.action === 'message_deleted_for_me') {
        console.log('Mensagem deletada:', data.deleted_message_id);
    } else if (data.action === 'message_revoked') {
        console.log('Mensagem revogada:', data.revoked_message_id);
    } else if (data.message) {
        console.log('Nova mensagem:', data.message.text);
    }

    res.status(200).send('OK');
});

function verifyWebhookSignature(payload, signature, secret) {
    const expectedSignature = crypto
        .createHmac('sha256', secret)
        .update(payload, 'utf8')
        .digest('hex');

    const receivedSignature = signature.replace('sha256=', '');
    return crypto.timingSafeEqual(
        Buffer.from(expectedSignature, 'hex'),
        Buffer.from(receivedSignature, 'hex')
    );
}

app.listen(3001, () => {
    console.log('Servidor de webhook ouvindo na porta 3001');
});
```

### Tratamento de Erros

O sistema de webhook inclui lógica de retry com backoff exponencial:

- **Timeout**: 10 segundos por requisição
- **Máximo de Tentativas**: 5 retries
- **Backoff**: Exponencial (1s, 2s, 4s, 8s, 16s)

Garanta que seu endpoint de webhook:

- Responda dentro de 10 segundos
- Retorne status HTTP 2xx para processamento bem-sucedido
- Lide com eventos duplicados de forma adequada
- Valide assinaturas para segurança

## Configuração

### Variáveis de Ambiente

```bash
# URL única de webhook
WHATSAPP_WEBHOOK=https://seuapp.com/webhook

# Múltiplas URLs de webhook (separadas por vírgula)
WHATSAPP_WEBHOOK=https://app1.com/webhook,https://app2.com/webhook

# Segredo do webhook para verificação HMAC
WHATSAPP_WEBHOOK_SECRET=sua-chave-super-secreta
```

### Flags de Linha de Comando

```bash
# Webhook único
./whatsapp rest --webhook="https://seuapp.com/webhook"

# Múltiplos webhooks
./whatsapp rest --webhook="https://app1.com/webhook,https://app2.com/webhook"

# Segredo customizado
./whatsapp rest --webhook-secret="sua-chave-secreta"
```

## Melhores Práticas

1. **Sempre verifique assinaturas** para garantir a autenticidade do webhook
2. **Lide com duplicatas** - o mesmo evento pode ser enviado múltiplas vezes
3. **Processe rapidamente** - responda dentro de 10 segundos para evitar timeouts
4. **Registre erros** para depurar problemas de integração do webhook
5. **Use HTTPS** para URLs de webhook para garantir transmissão segura
6. **Armazene arquivos de mídia** localmente se você precisar processá-los depois
7. **Implemente tratamento adequado de erros** para diferentes tipos de evento

## Solução de Problemas

### Problemas Comuns

1. **Webhook não recebendo eventos**:
    - Verifique se a URL do webhook está acessível pela internet
    - Verifique a configuração do webhook
    - Verifique firewall e configurações de rede

2. **Falha na verificação de assinatura**:
    - Garanta que o segredo do webhook corresponde à configuração
    - Use corpo da requisição bruto para cálculo da assinatura
    - Verifique implementação do HMAC

3. **Timeouts**:
    - Otimize velocidade de processamento do webhook
    - Implemente processamento assíncrono
    - Retorne resposta rapidamente, processe em segundo plano

4. **Arquivos de mídia faltando**:
    - Verifique configuração do caminho de armazenamento de mídia
    - Garanta espaço em disco suficiente
    - Verifique permissões de arquivo

### Log de Debug

Habilite o modo debug para ver logs do webhook:

```bash
./whatsapp rest --debug=true --webhook="https://seuapp.com/webhook"
```

Isso mostrará logs detalhados de tentativas de entrega de webhook e erros.
