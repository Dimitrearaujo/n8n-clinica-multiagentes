# 🏥 Secretária Virtual Clínica — Sistema Multi-Agentes

> Ecossistema de atendimento médico com **múltiplos agentes IA especializados por médico** — cada agente gerencia seu próprio CRM (Airtable), agenda (Google Calendar) e atende via WhatsApp com voz e texto.

[![n8n](https://img.shields.io/badge/n8n-workflow-orange?logo=n8n)](https://n8n.io)
[![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4o-412991?logo=openai)](https://openai.com)
[![Airtable](https://img.shields.io/badge/Airtable-CRM-18BFFF?logo=airtable)](https://airtable.com)
[![Google Calendar](https://img.shields.io/badge/Google_Calendar-API-4285F4?logo=googlecalendar)](https://calendar.google.com)

---

## 📌 O que este workflow faz

Um **orquestrador central** recebe mensagens de WhatsApp e roteia automaticamente para o agente do médico correto. Cada agente é uma secretária virtual independente com:

- Atendimento humanizado em texto **e áudio** (voz sintetizada)
- Consulta e criação de cadastros no CRM (Airtable)
- Verificação de disponibilidade e agendamento no Google Calendar
- Memória de sessão por paciente
- Busca na web para informações complementares

### Médicos configurados no template
| Agente | Especialidade | Ferramentas |
|---|---|---|
| Secretária do Dr. Francisco | Cirurgia Plástica | CRM + Agenda + Web Search |
| Secretária do Dr. Roberto | (configurável) | CRM + Agenda |
| Secretária da Dra. Janaina | (configurável) | CRM + Agenda |

### Caso de uso
Clínicas com múltiplos profissionais que precisam de atendimento automatizado 24/7 pelo WhatsApp, sem um call center.

---

## 🏗️ Arquitetura

```
WhatsApp / Webhook
        │
        ▼
  Orquestrador IA (GPT-4o)
  ├── Identifica o médico solicitado
  ├── Processa imagens do paciente (visão)
  └── Roteia para o agente correto
        │
   ┌────┼────┐
   ▼    ▼    ▼
 Ag.   Ag.   Ag.
 Fco  Rob   Jan
   │    │    │
   ▼    ▼    ▼
 ┌─────────────────────┐
 │  Ferramentas comuns │
 │  ├── CRM Airtable   │
 │  │   ├── buscarPaciente   │
 │  │   ├── criarPaciente    │
 │  │   └── atualizarDados   │
 │  └── Google Calendar│
 │      ├── verEventos        │
 │      ├── agendarEvento     │
 │      ├── atualizarEvento   │
 │      └── cancelarEvento    │
 └─────────────────────┘
        │
        ▼
  Resposta via WhatsApp
  (texto + áudio sintetizado)
```

---

## 🔧 Integrações

| Serviço | Uso |
|---|---|
| **WhatsApp / Webhook** | Canal de entrada de mensagens |
| **OpenAI GPT-4o** | Orquestrador + 3 agentes de atendimento |
| **OpenAI TTS** | Geração de respostas em áudio |
| **OpenAI Vision** | Leitura de imagens enviadas pelos pacientes |
| **Airtable** | CRM — cadastro e consulta de pacientes |
| **Google Calendar** | Gestão de agenda e agendamentos |
| **SerpAPI** | Busca web para informações complementares |
| **Memory Buffer** | Contexto de conversa por paciente (sessão) |

---

## ✅ Pré-requisitos

- [ ] n8n v1.0+ instalado
- [ ] Chave de API da OpenAI (com acesso a GPT-4o e TTS)
- [ ] Base no Airtable configurada com tabela de pacientes
- [ ] Calendários no Google Calendar (um por médico)
- [ ] Chave SerpAPI (para busca web — opcional)
- [ ] Integração com WhatsApp (Evolution API, Zapi ou similar)

---

## ⚙️ Configuração

### 1. Credenciais necessárias

| Nó | Credencial | Como obter |
|---|---|---|
| Agentes IA | OpenAI API Key | [platform.openai.com/api-keys](https://platform.openai.com/api-keys) |
| CRM_* (Airtable) | Airtable Personal Token | [airtable.com/create/tokens](https://airtable.com/create/tokens) |
| Agendamentos_* | Google Calendar OAuth2 | [Google Cloud Console](https://console.cloud.google.com) |
| buscarGoogle | SerpAPI Key | [serpapi.com](https://serpapi.com) |

### 2. Configurar o Airtable

Crie uma base com a tabela `Pacientes` contendo os campos:
```
Nome | CPF | Telefone | Endereço | Médico | Histórico de Agendamentos
```

No nó CRM de cada agente, configure o `Base ID` e `Table ID` da sua base.

### 3. Configurar os Calendários

Para cada médico, crie um calendário no Google Calendar e configure o `Calendar ID` nos nós `agendarEvento`, `verEventos`, etc.

### 4. Personalizar os agentes

Cada agente tem um `System Message` editável com:
- Nome e especialidade do médico
- Procedimentos oferecidos
- Horários de atendimento
- Tom de voz e personalidade

### 5. Conectar o WhatsApp

Configure o webhook de entrada com a URL do seu n8n:
```
https://seu-n8n.com/webhook/your-webhook-path
```

Configure sua integração WhatsApp para enviar mensagens para esse endpoint.

---

## 📊 Métricas do workflow

| Métrica | Valor |
|---|---|
| Total de nós | 63 |
| Agentes IA | 4 (1 orquestrador + 3 secretárias) |
| Médicos suportados | 3 (expansível) |
| Canais de resposta | Texto + Áudio |
| Modelo LLM | GPT-4o |
| Memória | Buffer por sessão de paciente |

---

## 💡 Dicas de personalização

- **Adicionar médico**: duplique um bloco de agente + ferramentas e atualize o orquestrador para incluir o novo nome
- **Mudar especialidade**: edite o `System Message` do agente com os procedimentos e protocolos específicos
- **Integrar prontuário**: substitua o Airtable por uma API do seu sistema de prontuário eletrônico
- **Relatórios**: adicione um workflow paralelo que consolida os agendamentos do dia no Sheets

---

[← Voltar ao índice](../README.md)
