# ⚡ N8N Workflows — Automação Pronta para Produção

Coleção de workflows N8N prontos para importar e usar, focados nas necessidades reais de pequenas e médias empresas brasileiras.

![N8N](https://img.shields.io/badge/N8N-1.x-0D2137?style=for-the-badge&logo=n8n&logoColor=34D399)
![License](https://img.shields.io/badge/License-MIT-0D2137?style=for-the-badge&logoColor=34D399)
![Maintained](https://img.shields.io/badge/Maintained-Yes-34D399?style=for-the-badge)

---

## 📦 Workflows disponíveis

| Arquivo | Descrição | Triggers |
|---------|-----------|----------|
| [`cobranca-automatica.json`](#-cobrança-automática) | Lembra clientes sobre faturas vencidas via WhatsApp | Cron diário |
| [`alerta-vps.json`](#-monitoramento-de-vps) | Monitora CPU, RAM e disco da VPS e alerta via ntfy | Cron 5min |
| [`lead-whatsapp.json`](#-captura-de-lead-via-whatsapp) | Captura leads do WhatsApp e cria no CRM automaticamente | Webhook |
| [`backup-notificacao.json`](#-notificação-de-backup) | Notifica equipe após backup concluído com status e tamanho | Webhook |
| [`relatorio-diario.json`](#-relatório-diário) | Envia resumo diário de vendas e atendimentos por WhatsApp | Cron diário |

---

## 💰 Cobrança Automática

Verifica faturas vencidas no banco de dados e envia lembrete personalizado via WhatsApp usando Evolution API.

**Fluxo:**
```
Cron (08h diário)
    │
    ▼
Consulta DB → Filtra vencidas há 1, 3, 7 dias
    │
    ▼
Para cada fatura:
    ├── Monta mensagem personalizada
    ├── Envia via Evolution API (WhatsApp)
    └── Registra log no banco
```

**Variáveis necessárias:**
```
DB_HOST, DB_USER, DB_PASS, DB_NAME
EVOLUTION_URL, EVOLUTION_API_KEY, EVOLUTION_INSTANCE
```

---

## 🖥️ Monitoramento de VPS

Verifica a saúde da VPS a cada 5 minutos e envia alerta via ntfy quando CPU > 80%, RAM > 85% ou disco > 90%.

**Fluxo:**
```
Cron (a cada 5 min)
    │
    ▼
SSH → Coleta métricas (CPU / RAM / Disco)
    │
    ▼
Verifica limites
    ├── Dentro do normal → Sem ação
    └── Limite atingido → Publica alerta no ntfy
```

**Variáveis necessárias:**
```
VPS_HOST, VPS_USER, SSH_KEY
NTFY_URL, NTFY_TOPIC
LIMITE_CPU, LIMITE_RAM, LIMITE_DISCO
```

---

## 📥 Captura de Lead via WhatsApp

Recebe webhook da Evolution API quando uma nova conversa começa e cria o lead automaticamente no CRM com nome, telefone e origem.

**Fluxo:**
```
Webhook (Evolution API)
    │
    ▼
Extrai dados: nome, telefone, mensagem
    │
    ▼
Verifica se lead já existe no CRM
    ├── Existe → Atualiza última interação
    └── Novo → Cria lead + envia mensagem de boas-vindas
```

**Variáveis necessárias:**
```
CRM_URL, CRM_API_KEY
EVOLUTION_URL, EVOLUTION_API_KEY, EVOLUTION_INSTANCE
WEBHOOK_SECRET
```

---

## 💾 Notificação de Backup

Recebe webhook após conclusão de backup (Restic, rsync ou script customizado) e notifica a equipe com status, tamanho e duração.

**Fluxo:**
```
Webhook (script de backup)
    │
    ▼
Extrai: status, tamanho, duração, servidor
    │
    ▼
Formata mensagem
    ├── Sucesso → Notifica via WhatsApp ✅
    └── Falha → Alerta urgente via WhatsApp + ntfy 🚨
```

**Variáveis necessárias:**
```
EVOLUTION_URL, EVOLUTION_API_KEY, EVOLUTION_INSTANCE
NTFY_URL, NTFY_TOPIC
WHATSAPP_NUMERO_DESTINO
```

---

## 📊 Relatório Diário

Gera e envia um resumo diário com total de vendas, novos leads, atendimentos abertos e fechados — direto no WhatsApp.

**Fluxo:**
```
Cron (18h diário)
    │
    ▼
Consultas paralelas:
    ├── Total vendas do dia (DB)
    ├── Novos leads (CRM API)
    └── Atendimentos (Chatwoot API)
    │
    ▼
Monta relatório formatado
    │
    ▼
Envia via WhatsApp (Evolution API)
```

**Variáveis necessárias:**
```
DB_HOST, DB_USER, DB_PASS, DB_NAME
CRM_URL, CRM_API_KEY
CHATWOOT_URL, CHATWOOT_TOKEN
EVOLUTION_URL, EVOLUTION_API_KEY, EVOLUTION_INSTANCE
WHATSAPP_NUMERO_DESTINO
```

---

## 🚀 Como importar

### Via interface do N8N

1. Abra seu N8N
2. Clique em **"+"** → **"Import from file"**
3. Selecione o arquivo `.json` desejado
4. Configure as credenciais necessárias
5. Ative o workflow

### Via API do N8N

```bash
curl -X POST https://seu-n8n.com.br/api/v1/workflows \
  -H "X-N8N-API-KEY: sua_api_key" \
  -H "Content-Type: application/json" \
  -d @cobranca-automatica.json
```

---

## ⚙️ Configuração das credenciais

Todos os workflows usam **credenciais nomeadas** — configure uma vez e reutilize em todos:

| Credencial | Tipo | Usado em |
|-----------|------|----------|
| `Evolution API` | HTTP Header Auth | Todos com WhatsApp |
| `PostgreSQL` | Database | Cobrança, Relatório |
| `Chatwoot API` | HTTP Header Auth | Relatório |
| `SSH VPS` | SSH Key | Monitoramento |
| `ntfy` | HTTP Request | Monitoramento, Backup |

---

## 📁 Estrutura do projeto

```
n8n-workflows/
├── cobranca-automatica.json
├── alerta-vps.json
├── lead-whatsapp.json
├── backup-notificacao.json
├── relatorio-diario.json
└── README.md
```

---

## 📞 Suporte

Este repositório é mantido pela **GP2Tech** como referência pública.

Precisa de implementação, customização ou integração completa com seu sistema?

[![GP2Tech](https://img.shields.io/badge/GP2Tech-Consultoria_em_Tecnologia-0D2137?style=for-the-badge&logo=google-chrome&logoColor=34D399)](https://gp2tech.com.br)
[![WhatsApp](https://img.shields.io/badge/Falar_no_WhatsApp-25D366?style=for-the-badge&logo=whatsapp&logoColor=white)](https://wa.me/5511994056667?text=Olá!%20Vim%20pelo%20GitHub%20e%20tenho%20interesse%20nos%20workflows%20N8N.)

---

## 📄 Licença

MIT © [Fabio Cestini](https://github.com/fcestini) — GP2Tech Soluções em Tecnologia
