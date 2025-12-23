<!-- markdownlint-disable MD041 -->
<!-- markdownlint-disable-next-line MD033 -->
<div align="center">
  <!-- markdownlint-disable-next-line MD033 -->
  <img src="src/views/assets/gowa.svg" alt="GoWA Logo" width="200" height="200">

## Golang WhatsApp - Construído com Go para uso eficiente de memória

</div>

[![Patreon](https://img.shields.io/badge/Apoie%20no-Patreon-orange.svg)](https://www.patreon.com/c/aldinokemal)
**Se você está usando esta ferramenta para gerar renda, considere apoiar seu desenvolvimento tornando-se um membro Patreon!**
Seu apoio ajuda a garantir que a biblioteca permaneça mantida e receba atualizações regulares!
___

![versão de lançamento](https://img.shields.io/github/v/release/aldinokemal/go-whatsapp-web-multidevice)
![Build Image](https://github.com/aldinokemal/go-whatsapp-web-multidevice/actions/workflows/build-docker-image.yaml/badge.svg)
![Binary Release](https://github.com/aldinokemal/go-whatsapp-web-multidevice/actions/workflows/release.yml/badge.svg)

## Suporte para Arquitetura `ARM` & `AMD` e Suporte `MCP`

Download:

- [Versões](https://github.com/aldinokemal/go-whatsapp-web-multidevice/releases/latest)
- [Docker Hub](https://hub.docker.com/r/aldinokemal2104/go-whatsapp-web-multidevice/tags)
- [GitHub Container Registry](https://github.com/aldinokemal/go-whatsapp-web-multidevice/pkgs/container/go-whatsapp-web-multidevice)

## Suporte para pacote n8n (n8n.io)

- [Pacote n8n](https://www.npmjs.com/package/@aldinokemal2104/n8n-nodes-gowa)
- Vá em Configurações -> Community Nodes -> Digite `@aldinokemal2104/n8n-nodes-gowa` -> Instalar

## Mudanças Importantes

- `v6`
  - Para o modo REST, você precisa executar `<binário> rest` em vez de `<binário>`
    - por exemplo: `./whatsapp rest` em vez de ~~./whatsapp~~
  - Para o modo MCP, você precisa executar `<binário> mcp`
    - por exemplo: `./whatsapp mcp`
- `v7`
  - A partir da versão 7.x, estamos usando goreleaser para construir o binário, então você pode baixar o binário
      de [versões](https://github.com/aldinokemal/go-whatsapp-web-multidevice/releases/latest)

## Recursos

- Enviar mensagens WhatsApp via API HTTP, consulte [docs/openapi.yml](./docs/openapi.yaml) para mais detalhes
- **Suporte ao Servidor MCP (Model Context Protocol)** - Integre com agentes de IA e ferramentas usando protocolo padronizado
- Mencionar alguém
  - `@numeroTelefone`
  - exemplo: `Olá @628974812XXXX, @628974812XXXX`
- Postar Status do WhatsApp
- **Enviar Stickers** - Converte automaticamente imagens para o formato de sticker WebP
  - Suporta formatos JPG, JPEG, PNG, WebP e GIF
  - Redimensionamento automático para 512x512 pixels
  - Preserva transparência para imagens PNG
- Comprimir imagem antes de enviar
- Comprimir vídeo antes de enviar
- Alterar o nome do SO para o seu app (é o nome do dispositivo ao conectar via celular)
  - `--os=Chrome` ou `--os=MeuAplicativo`
- Autenticação Básica (pode adicionar múltiplas credenciais)
  - `--basic-auth=kemal:secret,toni:password,usuario:senhaSecreta`, ou você pode simplificar
  - `-b=kemal:secret,toni:password,usuario:senhaSecreta`
- Suporte a implantação em subpasta
  - `--base-path="/gowa"` (permite implantação em um caminho específico como `/gowa/sub/path`)
- Porta e modo debug customizáveis
  - `--port 8000`
  - `--debug true`
- Resposta automática de mensagens
  - `--autoreply="Não responda esta mensagem"`
- Marcar automaticamente mensagens recebidas como lidas
  - `--auto-mark-read=true` (marca automaticamente as mensagens recebidas como lidas)
- Download automático de mídia de mensagens recebidas
  - `--auto-download-media=false` (desabilitar downloads automáticos de mídia, padrão: `true`)
- Webhook para mensagens recebidas
  - `--webhook="http://seuwebhook.site/handler"`, ou você pode simplificar
  - `-w="http://seuwebhook.site/handler"`
  - para mais detalhes, veja [Documentação de Payload do Webhook](./docs/webhook-payload.md)
- Segredo do Webhook
  Nosso webhook será enviado a você com um cabeçalho HMAC e uma chave padrão sha256 `secret`.

  Você pode modificar isso usando a opção abaixo:
  - `--webhook-secret="segredo"`
- **Documentação de Payload do Webhook**
  Para esquemas detalhados de payload do webhook, implementação de segurança e exemplos de integração,
  veja [Documentação de Payload do Webhook](./docs/webhook-payload.md)
- **Configuração TLS do Webhook**

  Se você encontrar erros de verificação de certificado TLS ao usar webhooks (por exemplo, com túneis Cloudflare ou certificados autoassinados):
  ```
  tls: failed to verify certificate: x509: certificate signed by unknown authority
  ```

  Você pode desabilitar a verificação de certificado TLS usando:
  - `--webhook-insecure-skip-verify=true`
  - Ou variável de ambiente: `WHATSAPP_WEBHOOK_INSECURE_SKIP_VERIFY=true`

  **Aviso de Segurança**: Esta opção desabilita a verificação de certificado TLS e deve ser usada apenas em:
  - Ambientes de desenvolvimento/teste
  - Túneis Cloudflare (que fornecem sua própria camada de segurança)
  - Redes internas com certificados autoassinados

  **Para ambientes de produção**, é fortemente recomendado usar certificados SSL adequados (por exemplo, Let's Encrypt) em vez de desabilitar a verificação.

## Configuração

Você pode configurar o aplicativo usando flags de linha de comando (mostradas acima) ou variáveis de ambiente. A configuração
pode ser definida de três maneiras (em ordem de prioridade):

1. Flags de linha de comando (maior prioridade)
2. Variáveis de ambiente
3. Arquivo `.env` (menor prioridade)

### Variáveis de Ambiente

Para usar variáveis de ambiente:

1. Copie `.env.example` para `.env` na raiz do seu projeto (`cp src/.env.example src/.env`)
2. Modifique os valores em `.env` de acordo com suas necessidades
3. Ou defina as mesmas variáveis como variáveis de ambiente do sistema

#### Variáveis de Ambiente Disponíveis

| Variável                      | Descrição                                 | Padrão                                      | Exemplo                                     |
|-------------------------------|-------------------------------------------|----------------------------------------------|---------------------------------------------|
| `APP_PORT`                    | Porta do aplicativo                       | `3000`                                       | `APP_PORT=8080`                             |
| `APP_DEBUG`                   | Habilitar log de debug                    | `false`                                      | `APP_DEBUG=true`                            |
| `APP_OS`                      | Nome do SO (nome do dispositivo no WhatsApp)| `Chrome`                                  | `APP_OS=MeuApp`                             |
| `APP_BASIC_AUTH`              | Credenciais de autenticação básica        | -                                            | `APP_BASIC_AUTH=user1:pass1,user2:pass2`    |
| `APP_BASE_PATH`               | Caminho base para implantação em subpasta | -                                            | `APP_BASE_PATH=/gowa`                       |
| `APP_TRUSTED_PROXIES`         | Faixas de IP de proxy confiável para proxy reverso| -                                   | `APP_TRUSTED_PROXIES=0.0.0.0/0`             |
| `DB_URI`                      | URI de conexão do banco de dados          | `file:storages/whatsapp.db?_foreign_keys=on` | `DB_URI=postgres://user:pass@host/db`       |
| `WHATSAPP_AUTO_REPLY`         | Mensagem de resposta automática           | -                                            | `WHATSAPP_AUTO_REPLY="Mensagem automática"` |
| `WHATSAPP_AUTO_MARK_READ`     | Marcar mensagens recebidas como lidas automaticamente| `false`                           | `WHATSAPP_AUTO_MARK_READ=true`              |
| `WHATSAPP_AUTO_DOWNLOAD_MEDIA`| Download automático de mídia de mensagens recebidas| `true`                              | `WHATSAPP_AUTO_DOWNLOAD_MEDIA=false`        |
| `WHATSAPP_WEBHOOK`            | URL(s) de webhook para eventos (separadas por vírgula)| -                                 | `WHATSAPP_WEBHOOK=https://webhook.site/xxx` |
| `WHATSAPP_WEBHOOK_SECRET`     | Segredo do webhook para validação         | `secret`                                     | `WHATSAPP_WEBHOOK_SECRET=chave-super-secreta`|
| `WHATSAPP_WEBHOOK_INSECURE_SKIP_VERIFY`| Pular verificação TLS para webhooks (inseguro)| `false`                        | `WHATSAPP_WEBHOOK_INSECURE_SKIP_VERIFY=true`|
| `WHATSAPP_ACCOUNT_VALIDATION` | Habilitar validação de conta              | `true`                                       | `WHATSAPP_ACCOUNT_VALIDATION=false`         |

Nota: Flags de linha de comando irão sobrescrever quaisquer valores definidos em variáveis de ambiente ou arquivo `.env`.

- Para mais comandos `./whatsapp --help`

## Requisitos

### Requisitos do Sistema

- **Go 1.24.0 ou superior** (para compilar do código-fonte)
- **FFmpeg** (para processamento de mídia)

### Suporte de Plataforma

- Linux (x86_64, ARM64)
- macOS (Intel, Apple Silicon)
- Windows (x86_64) - WSL recomendado

### Dependências (sem docker)

- Mac OS:
  - `brew install ffmpeg`
  - `export CGO_CFLAGS_ALLOW="-Xpreprocessor"`
- Linux:
  - `sudo apt update`
  - `sudo apt install ffmpeg`
- Windows (não recomendado, prefira usar [WSL](https://docs.microsoft.com/pt-br/windows/wsl/install)):
  - instale o ffmpeg, [baixe aqui](https://www.ffmpeg.org/download.html#build-windows)
  - adicione o ffmpeg às [variáveis de ambiente](https://www.google.com/search?q=windows+adicionar+ao+caminho+de+ambiente)

## Como usar

### Básico

1. Clone este repositório: `git clone https://github.com/aldinokemal/go-whatsapp-web-multidevice`
2. Abra a pasta que foi clonada via cmd/terminal.
3. execute `cd src`
4. execute `go run . rest` (para o modo API REST)
5. Abra `http://localhost:3000`

### Docker (você não precisa instalar as dependências)

1. Clone este repositório: `git clone https://github.com/aldinokemal/go-whatsapp-web-multidevice`
2. Abra a pasta que foi clonada via cmd/terminal.
3. execute `docker-compose up -d --build`
4. abra `http://localhost:3000`

### Construa seu próprio binário

1. Clone este repositório `git clone https://github.com/aldinokemal/go-whatsapp-web-multidevice`
2. Abra a pasta que foi clonada via cmd/terminal.
3. execute `cd src`
4. execute
    1. Linux & MacOS: `go build -o whatsapp`
    2. Windows (CMD / PowerShell): `go build -o whatsapp.exe`
5. execute
    1. Linux & MacOS: `./whatsapp rest` (para o modo API REST)
        1. execute `./whatsapp --help` para mais detalhes sobre as flags
    2. Windows: `.\whatsapp.exe rest` (para o modo API REST)
        1. execute `.\whatsapp.exe --help` para mais detalhes sobre as flags
6. abra `http://localhost:3000` no navegador

### Servidor MCP (Model Context Protocol)

Este aplicativo também pode ser executado como um servidor MCP, permitindo que agentes de IA e ferramentas interajam com o WhatsApp através de um
protocolo padronizado.

1. Clone este repositório `git clone https://github.com/aldinokemal/go-whatsapp-web-multidevice`
2. Abra a pasta que foi clonada via cmd/terminal.
3. execute `cd src`
4. execute `go run . mcp` ou compile o binário e execute `./whatsapp mcp`
5. O servidor MCP será iniciado em `http://localhost:8080` por padrão

#### Opções do Servidor MCP

- `--host localhost` - Define o host para o servidor MCP (padrão: localhost)
- `--port 8080` - Define a porta para o servidor MCP (padrão: 8080)

#### Ferramentas MCP Disponíveis

O servidor MCP do WhatsApp fornece ferramentas abrangentes para que agentes de IA interajam com o WhatsApp através de um protocolo padronizado. Abaixo está a lista completa de ferramentas disponíveis:

##### **📱 Gerenciamento de Conexão**

- `whatsapp_connection_status` - Verificar se o cliente WhatsApp está conectado e logado
- `whatsapp_login_qr` - Iniciar fluxo de login baseado em código QR com saída de imagem
- `whatsapp_login_with_code` - Gerar código de pareamento para login multi-dispositivo usando número de telefone
- `whatsapp_logout` - Desconectar a sessão atual do WhatsApp
- `whatsapp_reconnect` - Tentar reconectar ao WhatsApp usando a sessão armazenada

##### **💬 Mensagens & Comunicação**

- `whatsapp_send_text` - Enviar mensagens de texto com suporte a resposta e encaminhamento
- `whatsapp_send_contact` - Enviar cartões de contato com nome e número de telefone
- `whatsapp_send_link` - Enviar links com legendas personalizadas
- `whatsapp_send_location` - Enviar coordenadas de localização (latitude/longitude)
- `whatsapp_send_image` - Enviar imagens com legendas, compressão e opções de visualização única
- `whatsapp_send_sticker` - Enviar stickers com conversão automática para WebP (suporta JPG/PNG/GIF)

##### **📋 Gerenciamento de Chats & Contatos**

- `whatsapp_list_contacts` - Recuperar todos os contatos em sua conta do WhatsApp
- `whatsapp_list_chats` - Obter chats recentes com paginação e filtros de busca
- `whatsapp_get_chat_messages` - Buscar mensagens de chats específicos com filtro de tempo/mídia
- `whatsapp_download_message_media` - Baixar imagens/vídeos de mensagens

##### **👥 Gerenciamento de Grupos**

- `whatsapp_group_create` - Criar novos grupos com participantes iniciais opcionais
- `whatsapp_group_join_via_link` - Entrar em grupos usando links de convite
- `whatsapp_group_leave` - Sair de grupos por ID do grupo
- `whatsapp_group_participants` - Listar todos os participantes em um grupo
- `whatsapp_group_manage_participants` - Adicionar, remover, promover ou rebaixar membros do grupo
- `whatsapp_group_invite_link` - Obter ou redefinir links de convite do grupo
- `whatsapp_group_info` - Obter informações detalhadas do grupo
- `whatsapp_group_set_name` - Atualizar nome de exibição do grupo
- `whatsapp_group_set_topic` - Atualizar descrição/tópico do grupo
- `whatsapp_group_set_locked` - Alternar edição de informações do grupo apenas para administradores
- `whatsapp_group_set_announce` - Alternar modo apenas anúncios
- `whatsapp_group_join_requests` - Listar solicitações de entrada pendentes
- `whatsapp_group_manage_join_requests` - Aprovar ou rejeitar solicitações de entrada

#### Endpoints MCP

- Endpoint SSE: `http://localhost:8080/sse`
- Endpoint de mensagem: `http://localhost:8080/message`

### Configuração MCP

Certifique-se de ter o servidor MCP em execução: `./whatsapp mcp`

Para ferramentas de IA que suportam MCP com SSE (como Cursor), adicione esta configuração:

```json
{
  "mcpServers": {
    "whatsapp": {
      "url": "http://localhost:8080/sse"
    }
  }
}
```

### Modo de Produção REST (docker)

Usando Docker Hub:

```bash
docker run --detach --publish=3000:3000 --name=whatsapp --restart=always --volume=$(docker volume create --name=whatsapp):/app/storages aldinokemal2104/go-whatsapp-web-multidevice rest --autoreply="Não responda esta mensagem, por favor"
```

Usando GitHub Container Registry:

```bash
docker run --detach --publish=3000:3000 --name=whatsapp --restart=always --volume=$(docker volume create --name=whatsapp):/app/storages ghcr.io/aldinokemal/go-whatsapp-web-multidevice rest --autoreply="Não responda esta mensagem, por favor"
```

### Modo de Produção REST (docker compose)

crie o arquivo `docker-compose.yml` com a seguinte configuração:

Usando Docker Hub:

```yml
services:
  whatsapp:
    image: aldinokemal2104/go-whatsapp-web-multidevice
    container_name: whatsapp
    restart: always
    ports:
      - "3000:3000"
    volumes:
      - whatsapp:/app/storages
    command:
      - rest
      - --basic-auth=admin:admin
      - --port=3000
      - --debug=true
      - --os=Chrome
      - --account-validation=false

volumes:
  whatsapp:
```

Usando GitHub Container Registry:

```yml
services:
  whatsapp:
    image: ghcr.io/aldinokemal/go-whatsapp-web-multidevice
    container_name: whatsapp
    restart: always
    ports:
      - "3000:3000"
    volumes:
      - whatsapp:/app/storages
    command:
      - rest
      - --basic-auth=admin:admin
      - --port=3000
      - --debug=true
      - --os=Chrome
      - --account-validation=false

volumes:
  whatsapp:
```

ou com arquivo env (Docker Hub):

```yml
services:
  whatsapp:
    image: aldinokemal2104/go-whatsapp-web-multidevice
    container_name: whatsapp
    restart: always
    ports:
      - "3000:3000"
    volumes:
      - whatsapp:/app/storages
    environment:
      - APP_BASIC_AUTH=admin:admin
      - APP_PORT=3000
      - APP_DEBUG=true
      - APP_OS=Chrome
      - APP_ACCOUNT_VALIDATION=false

volumes:
  whatsapp:
```

ou com arquivo env (GitHub Container Registry):

```yml
services:
  whatsapp:
    image: ghcr.io/aldinokemal/go-whatsapp-web-multidevice
    container_name: whatsapp
    restart: always
    ports:
      - "3000:3000"
    volumes:
      - whatsapp:/app/storages
    environment:
      - APP_BASIC_AUTH=admin:admin
      - APP_PORT=3000
      - APP_DEBUG=true
      - APP_OS=Chrome
      - APP_ACCOUNT_VALIDATION=false

volumes:
  whatsapp:
```

### Modo de Produção (binário)

- baixe o binário das [versões](https://github.com/aldinokemal/go-whatsapp-web-multidevice/releases)

Você pode fazer fork ou editar este código-fonte!

## API Atual

### API MCP (Model Context Protocol)

- O servidor MCP fornece ferramentas padronizadas para que agentes de IA interajam com o WhatsApp
- Suporta transporte Server-Sent Events (SSE)
- Ferramentas disponíveis: `whatsapp_send_text`, `whatsapp_send_contact`, `whatsapp_send_link`, `whatsapp_send_location`
- Compatível com ferramentas de IA e agentes habilitados para MCP

### API REST HTTP

- [Documento de Especificação da API](https://bump.sh/aldinokemal/doc/go-whatsapp-web-multidevice).
- Verifique [docs/openapi.yml](./docs/openapi.yaml) para especificações detalhadas da API.
- Use [SwaggerEditor](https://editor.swagger.io) para visualizar a API.
- Gere clientes HTTP usando [openapi-generator](https://openapi-generator.tech/#try).

| Recurso | Menu                                   | Método | URL                                 |
|---------|----------------------------------------|--------|-------------------------------------|
| ✅       | Login com Scan QR                      | GET    | /app/login                          |
| ✅       | Login com Código de Pareamento        | GET    | /app/login-with-code                |
| ✅       | Logout                                 | GET    | /app/logout                         |  
| ✅       | Reconectar                             | GET    | /app/reconnect                      |
| ✅       | Dispositivos                           | GET    | /app/devices                        |
| ✅       | Informações do Usuário                 | GET    | /user/info                          |
| ✅       | Avatar do Usuário                      | GET    | /user/avatar                        |
| ✅       | Alterar Avatar do Usuário              | POST   | /user/avatar                        |
| ✅       | Alterar PushName do Usuário            | POST   | /user/pushname                      |
| ✅       | Meus Grupos do Usuário                 | GET    | /user/my/groups                     |
| ✅       | Meus Boletins Informativos             | GET    | /user/my/newsletters                |
| ✅       | Configuração de Privacidade            | GET    | /user/my/privacy                    |
| ✅       | Meus Contatos                          | GET    | /user/my/contacts                   |
| ✅       | Verificar Usuário                      | GET    | /user/check                         |
| ✅       | Perfil Comercial                       | GET    | /user/business-profile              |
| ✅       | Enviar Mensagem                        | POST   | /send/message                       |
| ✅       | Enviar Imagem                          | POST   | /send/image                         |
| ✅       | Enviar Áudio                           | POST   | /send/audio                         |
| ✅       | Enviar Arquivo                         | POST   | /send/file                          |
| ✅       | Enviar Vídeo                           | POST   | /send/video                         |
| ✅       | Enviar Sticker                         | POST   | /send/sticker                       |
| ✅       | Enviar Contato                         | POST   | /send/contact                       |
| ✅       | Enviar Link                            | POST   | /send/link                          |
| ✅       | Enviar Localização                     | POST   | /send/location                      |
| ✅       | Enviar Enquete / Votação               | POST   | /send/poll                          |
| ✅       | Enviar Presença                        | POST   | /send/presence                      |
| ✅       | Enviar Presença no Chat (Indicador de Digitação)| POST| /send/chat-presence           |
| ✅       | Revogar Mensagem                       | POST   | /message/:message_id/revoke         |
| ✅       | Reagir à Mensagem                      | POST   | /message/:message_id/reaction       |
| ✅       | Deletar Mensagem                       | POST   | /message/:message_id/delete         |
| ✅       | Editar Mensagem                        | POST   | /message/:message_id/update         |
| ✅       | Ler Mensagem (DM)                      | POST   | /message/:message_id/read           |
| ✅       | Marcar Mensagem como Favorita          | POST   | /message/:message_id/star           |
| ✅       | Desmarcar Mensagem como Favorita       | POST   | /message/:message_id/unstar         |
| ✅       | Entrar no Grupo com Link               | POST   | /group/join-with-link               |
| ✅       | Informações do Grupo pelo Link         | GET    | /group/info-from-link               |
| ✅       | Informações do Grupo                   | GET    | /group/info                         |
| ✅       | Sair do Grupo                          | POST   | /group/leave                        |
| ✅       | Criar Grupo                            | POST   | /group                              |
| ✅       | Listar Participantes no Grupo          | GET    | /group/participants                 |
| ✅       | Adicionar Participantes ao Grupo       | POST   | /group/participants                 |
| ✅       | Remover Participante do Grupo          | POST   | /group/participants/remove          |
| ✅       | Promover Participante no Grupo         | POST   | /group/participants/promote         |
| ✅       | Rebaixar Participante no Grupo         | POST   | /group/participants/demote          |
| ✅       | Exportar Participantes do Grupo (CSV)  | GET    | /group/participants/export          |
| ✅       | Listar Participantes Solicitados       | GET    | /group/participant-requests         |
| ✅       | Aprovar Participante Solicitado        | POST   | /group/participant-requests/approve |
| ✅       | Rejeitar Participante Solicitado       | POST   | /group/participant-requests/reject  |
| ✅       | Definir Foto do Grupo                  | POST   | /group/photo                        |
| ✅       | Definir Nome do Grupo                  | POST   | /group/name                         |
| ✅       | Definir Grupo Bloqueado                | POST   | /group/locked                       |
| ✅       | Definir Grupo como Anúncio             | POST   | /group/announce                     |
| ✅       | Definir Tópico do Grupo                | POST   | /group/topic                        |
| ✅       | Obter Link de Convite do Grupo         | GET    | /group/invite-link                  |
| ✅       | Deixar de Seguir Boletim Informativo   | POST   | /newsletter/unfollow                |
| ✅       | Obter Lista de Chats                   | GET    | /chats                              |
| ✅       | Obter Mensagens do Chat                | GET    | /chat/:chat_jid/messages            |
| ✅       | Rotular Chat                           | POST   | /chat/:chat_jid/label               |
| ✅       | Fixar Chat                             | POST   | /chat/:chat_jid/pin                 |
| ✅       | Definir Mensagens Temporárias          | POST   | /chat/:chat_jid/disappearing        |

```txt
✅ = Disponível
❌ = Ainda Não Disponível
```

## Interface do Usuário

### UI do MCP

- Configurar MCP (testado no cursor)
  ![Configurar MCP](https://i.ibb.co/vCg4zNWt/mcpsetup.png)
- Testar MCP
  ![Testar MCP](https://i.ibb.co/B2LX38DW/mcptest.png)
- Configuração MCP bem-sucedida
  ![Sucesso MCP](https://i.ibb.co/1fCx0Myc/mcpsuccess.png)

### UI da API REST HTTP

| Descrição                    | Imagem                                                         |
|------------------------------|---------------------------------------------------------------|
| Página Inicial               | ![Página Inicial](./gallery/homepage.png)                     |
| Login                        | ![Login](./gallery/login.png)                                 |
| Login com Código             | ![Login com Código](./gallery/login-with-code.png)            |
| Enviar Mensagem              | ![Enviar Mensagem](./gallery/send-message.png)                |
| Enviar Imagem                | ![Enviar Imagem](./gallery/send-image.png)                    |
| Enviar Arquivo               | ![Enviar Arquivo](./gallery/send-file.png)                    |
| Enviar Vídeo                 | ![Enviar Vídeo](./gallery/send-video.png)                     |
| Enviar Sticker               | ![Enviar Sticker](./gallery/send-sticker.png)                 |
| Enviar Contato               | ![Enviar Contato](./gallery/send-contact.png)                 |
| Enviar Localização           | ![Enviar Localização](./gallery/send-location.png)            |
| Enviar Áudio                 | ![Enviar Áudio](./gallery/send-audio.png)                     |
| Enviar Enquete               | ![Enviar Enquete](./gallery/send-poll.png)                    |
| Enviar Presença              | ![Enviar Presença](./gallery/send-presence.png)               |
| Enviar Link                  | ![Enviar Link](./gallery/send-link.png)                       |
| Meu Grupo                    | ![Meu Grupo](./gallery/group-list.png)                        |
| Informações do Grupo pelo Link| ![Informações do Grupo pelo Link](./gallery/group-info-from-link.png)|
| Criar Grupo                  | ![Criar Grupo](./gallery/group-create.png)                    |
| Entrar no Grupo com Link     | ![Entrar no Grupo com Link](./gallery/group-join-link.png)    |
| Gerenciar Participantes      | ![Gerenciar Participantes](./gallery/group-manage-participant.png)|
| Meus Boletins                | ![Meus Boletins](./gallery/newsletter-list.png)               |
| Meus Contatos                | ![Meus Contatos](./gallery/contact-list.png)                  |
| Perfil Comercial             | ![Perfil Comercial](./gallery/business-profile.png)           |

### Nota para Mac OS

- Por favor, faça isso se você tiver um erro (invalid flag in pkg-config --cflags: -Xpreprocessor)
  `export CGO_CFLAGS_ALLOW="-Xpreprocessor"`

## Importante

- Este projeto não é oficial e não é afiliado ao WhatsApp.
- Por favor, use a API oficial do WhatsApp para evitar qualquer problema.
- Só podemos executar MCP ou API REST, esta é uma limitação da biblioteca whatsmeow. O MCP independente estará disponível no
  futuro.
