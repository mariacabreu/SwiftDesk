# SwiftDesk — Agile Support Solutions

> Sistema completo de Help Desk para gerenciamento de chamados, equipamentos e equipes de suporte técnico multinível.

---

## 1. O Problema Atacado

Pequenas e médias empresas enfrentam dificuldades com o gerenciamento informal de solicitações de suporte técnico:
- Chamados abertos por e-mail, WhatsApp ou telefone sem rastreabilidade
- Falta de priorização e distribuição automática entre técnicos
- Sem histórico consolidado de atendimentos por equipamento ou colaborador
- Sem visibilidade de SLA, performance da equipe e custos de manutenção
- Controle de patrimônio de TI (notebooks, impressoras, etc.) disperso em planilhas

O SwiftDesk centraliza todo o ciclo de vida do atendimento em uma única plataforma, do registro do chamado ao fechamento, passando por atribuição automática, escalonamento multinível e auditoria completa.

---

## 2. Objetivos da Aplicação

| Objetivo | Descrição |
|----------|-----------|
| **Centralização** | Unificar abertura, acompanhamento e fechamento de chamados em um só lugar |
| **Distribuição Inteligente** | Atribuir chamados automaticamente por nível de atendimento (N1/N2/N3) e prioridade |
| **Escalonamento** | Permitir que técnicos de nível inferior encaminhem casos complexos para níveis superiores |
| **Gestão de Patrimônio** | Cadastrar e monitorar equipamentos vinculando-os a chamados e rotinas de backup |
| **Rastreabilidade** | Histórico completo de ações, logs de auditoria e notificações em tempo real |
| **Tomada de Decisão** | Relatórios analíticos com métricas de SLA, performance por técnico e volume de chamados |
| **Multi-empresa** | Arquitetura multi-tenant onde cada empresa gerencia seus próprios dados e equipe |

---

## 3. Stack Tecnológica

### Frontend (Next.js 16 + React 19)
| Tecnologia | Versão | Propósito |
|-----------|--------|-----------|
| **Next.js** | 16.1.6 | Framework React com SSR, rotas App Router e proxy de API via `rewrites` |
| **React** | 19.2.4 | Biblioteca de interface |
| **TypeScript** | 5.7.3 | Tipagem estática |
| **Tailwind CSS** | 4.2.0 | Estilização utilitária com tema custom (`#1a3a5c` / `#3ba5d8`) |
| **Shadcn/UI** | — | Biblioteca de componentes acessíveis (Dialog, Table, AlertDialog, etc.) |
| **Radix UI** | 1.2.x+ | Primitivos acessíveis que alimentam o Shadcn |
| **React Hook Form** | 7.54.1 | Gerenciamento de formulários |
| **Zod** | 3.24.1 | Validação de schemas |
| **Recharts** | 2.15.0 | Gráficos nos dashboards |
| **Lucide React** | 0.564.0 | Ícones |
| **jsPDF + AutoTable** | 4.2.x / 5.0.x | Exportação de relatórios em PDF |
| **Sonner** | 1.7.1 | Sistema de toasts/notificações |
| **next-themes** | 0.4.6 | Suporte a modo claro/escuro |

### Backend (FastAPI + Python)
| Tecnologia | Versão | Propósito |
|-----------|--------|-----------|
| **FastAPI** | última | Framework web assíncrono e API REST |
| **Uvicorn** | — | Servidor ASGI (dev) |
| **Gunicorn** | — | Servidor WSGI (produção - Render) |
| **SQLAlchemy** | última | ORM para mapeamento objeto-relacional |
| **Pydantic** | última | Validação de schemas de entrada/saída |
| **PyMySQL** | última | Driver MySQL para deploy em nuvem |
| **python-dotenv** | última | Carregamento de variáveis de ambiente |
| **python-multipart** | última | Upload de anexos nos chamados |
| **cryptography** | última | Utilitários de criptografia |
| **requests** | última | Integração com API ViaCEP (consulta de endereço por CEP) |
| **SMTP (smtplib)** | nativo | Envio de e-mails transacionais (boas-vindas, recuperação de senha) |

### Banco de Dados
| Ambiente | Tecnologia | Observação |
|----------|-----------|------------|
| **Desenvolvimento** | SQLite | Arquivo local `swiftdesk.db` (fallback automático) |
| **Produção** | MySQL (Railway) | Conexão via `DATABASE_URL` com pool de conexões otimizado |

Modelos principais: `Empresa`, `Funcionario`, `Equipamento`, `Chamado`, `HistoricoChamado`, `AnexoChamado`, `Notificacao`, `LogSistema`, `BackupEquipamento`, `BackupSistema`, `PasswordRecovery`.

---

## 4. Funcionalidades Principais

### 🔐 Autenticação & Perfis de Usuário
- Login com credenciais (login + senha)
- Cadastro de nova empresa (cria automaticamente o usuário administrador)
- Recuperação de senha via e-mail com código de 5 dígitos (válido 30 min)
- **Dois perfis principais:**
  - **Empresa/Gestor:** Visualiza todos os chamados, relatórios, gestão de funcionários e equipamentos
  - **Suporte Técnico:** Atende chamados, escalona para níveis superiores e visualiza seu painel

### 📋 Gestão de Chamados (Ciclo Completo)
- Abertura por funcionário vinculada a equipamento (opcional)
- **Atribuição automática** por prioridade e nível:
  - Prioridade Baixa → N1
  - Prioridade Média → N2
  - Prioridade Alta → N3
- Atribuição manual direta também suportada
- **Workflow de status:** Aberto → Em Atendimento → Aguardando Solicitante/Terceiro → Pendente → Resolvido/Fechado/Cancelado
- **Escalonamento multinível (N1 → N2 → N3):** Técnico encaminha, chamado volta ao pool do próximo nível sem técnico atrelado
- Histórico de ações por chamado com autor e data/hora
- Upload de anexos (prints, logs, documentos)
- Notificações internas (abertura, atribuição, mudança de status, escalonamento)

### 🖥️ Gestão de Equipamentos
- Cadastro com patrimônio único (Notebook, Desktop, Impressora, etc.)
- Campo JSON flexível `especificacoes` para SO, RAM, HD, processador, etc.
- Sistema Operacional restrito a: Windows, Linux, Mac
- Relacionamento com chamados (histórico de atendimentos por equipamento)
- Rotina de **backup por equipamento** e **backup completo do sistema**
- Status: Ativo, Inativo, Manutenção

### 👥 Gestão de Funcionários
- Cadastro com geração automática de login (8 dígitos únicos)
- Senha inicial = 6 primeiros dígitos do CPF
- Envio automático de e-mail de boas-vindas com credenciais
- Níveis de atendimento: N1, N2, N3 (para distribuição automática)
- Status: Ativo / Inativo

### 📊 Dashboards & Relatórios
- **Dashboard Suporte:** Chamados abertos, em andamento, resolvidos, % escalonados, SLA cumprido, chamados por técnico
- **Dashboard Empresa:** Visão consolidada da empresa
- **Relatórios Analíticos (exportáveis em PDF):**
  - Resumo de chamados por status
  - Distribuição por prioridade
  - Performance por técnico (atendidos, SLA, tempo médio)
  - Volume por tipo de chamado e nível de atendimento
- Integração com dados reais via `/api/stats/relatorios`

### 🔍 Auditoria & Logs
- Tabela `LogSistema` registra: autenticações (sucesso/falha), criação de chamados/equipamentos, atualizações, exclusões
- Visível apenas para perfis Administrador e Suporte Técnico
- Campos: timestamp, tipo (info/success/warning/error), módulo, ação, usuário, IP, empresa

### 📧 Notificações & Integrações
- Notificações internas por usuário
- E-mail transacional SMTP:
  - Boas-vindas ao cadastrar funcionário/empresa
  - Código de recuperação de senha
- Consulta automática de endereço via API ViaCEP no cadastro de empresa

---

## 5. Como Executar Localmente

### Pré-requisitos
- Node.js 18+ (para o frontend)
- Python 3.10+ (para o backend)
- Pip (gerenciador de pacotes Python)

### Passo a Passo

**1. Clonar o repositório**
```bash
git clone <url-do-repositorio>
cd SwiftDesk
```

**2. Backend — Instalar dependências e rodar**
```bash
cd backend
pip install -r requirements.txt
```

Crie um arquivo `.env` na pasta `backend/` (opcional para SQLite local):
```
DATABASE_URL=mysql://user:pass@host:port/db   # Opcional; sem ela usa SQLite local
SMTP_SERVER=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=seu_email@gmail.com
SMTP_PASSWORD=sua_senha_app
SMTP_FROM_NAME=SwiftDesk Support
```

Inicie o servidor:
```bash
# Opção 1: porta 8000 (padrão)
py -m uvicorn main:app --reload --port 8000

# Opção 2: porta 8001 (compatível com script root)
py -m uvicorn main:app --reload --port 8001
```
A API estará disponível em `http://localhost:8000` (ou 8001) com documentação Swagger em `/docs`.

**3. Frontend — Instalar dependências e rodar**
```bash
cd ../frontend
npm install
npm run dev
```
A aplicação estará em `http://localhost:3000`. Os requests `/api/*` são automaticamente roteados para `http://localhost:8000` (dev) via `next.config.mjs`.

**4. Modo root (executa front e backend em paralelo)**
```bash
cd ..
npm install         # instala npm-run-all na raiz
npm run dev         # abre frontend e backend simultaneamente
```

### Acesso Inicial
- Ao cadastrar uma nova empresa, o usuário administrador é criado automaticamente.
- Para novos funcionários: login = 8 dígitos aleatórios + senha = 6 primeiros dígitos do CPF.
- Consulte `/api/debug-email?email=seu@email.com` para diagnosticar envio SMTP.

---

## 6. Links Úteis

| Recurso | URL |
|---------|-----|
| 🌐 Aplicação (Vercel) | https://swiftdesk.vercel.app |
| ⚙️ Backend API (Render) | https://swiftdesk-nvsl.onrender.com |
| 📖 Swagger UI (dev) | http://localhost:8000/docs |
| 🗃️ Diagramas de entidade-relacionamento | Ver modelos em [database.py](file:///c:/Users/User/Downloads/SwiftDesk/backend/database.py#L8-L261) |

---

*Projeto desenvolvido como solução de Help Desk empresarial com foco em simplicidade, rastreabilidade e performance de equipe.*
