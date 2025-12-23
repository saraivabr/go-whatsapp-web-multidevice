# CLAUDE.md

Este arquivo fornece orientação ao Claude Code (claude.ai/code) ao trabalhar com código neste repositório.

## Comandos Comuns de Desenvolvimento

### Compilar e Executar

- **Compilar binário**: `cd src && go build -o whatsapp` (Linux/macOS) ou `go build -o whatsapp.exe` (Windows)
- **Executar modo API REST**: `cd src && go run . rest` ou `./whatsapp rest`
- **Executar modo servidor MCP**: `cd src && go run . mcp` ou `./whatsapp mcp`
- **Executar com Docker**: `docker-compose up -d --build`

### Testes

- **Executar todos os testes**: `cd src && go test ./...`
- **Executar testes de pacote específico**: `cd src && go test ./validations`
- **Executar testes com cobertura**: `cd src && go test -cover ./...`

### Desenvolvimento

- **Formatar código**: `cd src && go fmt ./...`
- **Obter dependências**: `cd src && go mod tidy`
- **Verificar problemas**: `cd src && go vet ./...`

## Arquitetura do Projeto

Este é um servidor API Web WhatsApp baseado em Go que suporta os modos API REST e MCP (Model Context Protocol).

### Padrão de Arquitetura Central

- **Design Orientado a Domínio**: Lógica de negócios separada em pacotes de domínio (`domains/`)
- **Arquitetura Limpa**: Separação clara entre camadas de UI, casos de uso e infraestrutura
- **Cobra CLI**: Padrão de comando com comandos separados para os modos `rest` e `mcp`

### Diretórios Principais

- `src/`: Diretório principal do código-fonte
- `src/cmd/`: Comandos CLI (root, rest, mcp)
- `src/domains/`: Lógica de domínio de negócios (app, chat, group, message, send, user, newsletter)
- `src/infrastructure/`: Integrações externas (WhatsApp, banco de dados)
- `src/ui/`: Camadas de interface do usuário (API REST, servidor MCP, WebSocket)
- `src/usecase/`: Casos de uso da aplicação conectando domínios e UI
- `src/validations/`: Lógica de validação de entrada
- `src/pkg/`: Utilitários e auxiliares compartilhados

### Configuração

- **Variáveis de Ambiente**: Veja `.env.example` para todas as opções disponíveis
- **Flags de Linha de Comando**: Todas as variáveis de ambiente podem ser sobrescritas com flags CLI
- **Prioridade de Configuração**: Flags CLI > Variáveis de ambiente > arquivo `.env`

### Banco de Dados

- **DB Principal**: Dados de conexão do WhatsApp (SQLite por padrão, suporta PostgreSQL)
- **Armazenamento de Chat**: Banco de dados SQLite separado para histórico de chat (`storages/chatstorage.db`)
- **URIs de Banco de Dados**: Configurável via variáveis de ambiente `DB_URI` e `DB_KEYS_URI`

### Arquitetura Específica do Modo

- **Modo REST**: Servidor web Fiber com templates HTML, suporte a WebSocket, pilha de middleware
- **Modo MCP**: Servidor Model Context Protocol com transporte SSE para integração com agentes de IA

### Dependências Principais

- `go.mau.fi/whatsmeow`: Implementação do protocolo WhatsApp Web
- `github.com/gofiber/fiber/v2`: Framework web para API REST
- `github.com/mark3labs/mcp-go`: Implementação do servidor MCP
- `github.com/spf13/cobra`: Framework CLI
- `github.com/spf13/viper`: Gerenciamento de configuração

### Integração com WhatsApp

- Usa biblioteca whatsmeow para protocolo WhatsApp Web
- Suporta contas WhatsApp multi-dispositivo
- Auto-reconexão e monitoramento de conexão
- Compressão de mídia e suporte a webhook

## Notas Importantes

- O aplicativo não pode executar os modos REST e MCP simultaneamente (limitação da biblioteca whatsmeow)
- Todo o código-fonte deve estar no diretório `src/`
- Arquivos de mídia são armazenados em `src/statics/media/` e `src/storages/`
- Templates HTML e assets são incorporados no binário usando o recurso embed do Go
- FFmpeg é necessário para processamento de mídia (a instalação varia por plataforma)
