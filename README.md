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

### Deploy & Infraestrutura 100% em Nuvem
| Componente | Plataforma |
|-----------|-----------|
| **Frontend** | Vercel (`swiftdesk.vercel.app`) |
| **Backend API** | Render (`swiftdesk-nvsl.onrender.com`) |
| **Banco de Dados** | Railway (MySQL) |
| **E-mails** | SMTP (Gmail/configurável via `.env`) |

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

## 5. Estrutura do Projeto

```
SwiftDesk/
├── frontend/                          # Aplicação Next.js (frontend)
│   ├── app/
│   │   ├── page.tsx                   # Tela de autenticação (login/cadastro/recuperação)
│   │   ├── layout.tsx                 # Root layout com ThemeProvider
│   │   ├── globals.css                # Tema Tailwind custom (cores #1a3a5c / #3ba5d8)
│   │   └── dashboard/page.tsx         # Roteamento do dashboard por perfil
│   ├── components/
│   │   ├── pages/                     # Páginas de feature
│   │   │   ├── dashboard-suporte-page.tsx
│   │   │   ├── dashboard-empresa-page.tsx
│   │   │   ├── chamados-page.tsx
│   │   │   ├── equipamentos-page.tsx
│   │   │   ├── escalonados-page.tsx
│   │   │   ├── relatorios-page.tsx
│   │   │   └── empresa/               # Páginas específicas do perfil empresa
│   │   │       ├── auditoria-page.tsx
│   │   │       ├── backup-page.tsx
│   │   │       ├── gestao-funcionarios-page.tsx
│   │   │       ├── meus-chamados-page.tsx
│   │   │       ├── meus-equipamentos-page.tsx
│   │   │       └── novo-chamado-page.tsx
│   │   └── ui/                        # Componentes Shadcn/UI reutilizáveis
│   ├── lib/utils.ts                   # Helpers (formatDate, safeJson, cn)
│   ├── hooks/                         # Hooks customizados (use-mobile, use-toast)
│   ├── next.config.mjs                # Rewrites API → backend (dev: localhost:8001, prod: Render)
│   └── package.json
│
├── backend/                           # API FastAPI (backend)
│   ├── main.py                        # Rotas REST, lógica de negócio, middlewares
│   ├── database.py                    # Modelos SQLAlchemy + engine + init_db()
│   ├── requirements.txt
│   ├── Procfile                       # Deploy no Render (gunicorn)
│   ├── seed_db.py                     # Script de população inicial
│   ├── migrate_db.py                  # Migrations manuais SQLite → MySQL
│   └── add_column.py / fix_schema.py  # Scripts de correção de schema
│
├── package.json                       # Scripts root (dev paralelo front+back)
└── README.md
```

---

## 6. Como Executar Localmente

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

## 7. Deploy em Produção

| Componente | Passos |
|-----------|--------|
| **Backend → Render** | 1. Conectar repositório; 2. Build Command: `pip install -r requirements.txt`; 3. Start Command: `gunicorn main:app -k uvicorn.workers.UvicornWorker --bind 0.0.0.0:$PORT`; 4. Definir variáveis de ambiente (`DATABASE_URL`, credenciais SMTP, `BACKEND_CORS_ORIGINS`) |
| **Frontend → Vercel** | 1. Importar repositório (pasta `frontend` como root); 2. Framework: Next.js; 3. Variável de ambiente: `BACKEND_URL=https://swiftdesk-nvsl.onrender.com`; 4. Deploy |
| **Banco → Railway** | Provisionar instância MySQL; copiar `DATABASE_URL` para as variáveis do backend (Render) e rodar `python database.py` uma vez para criar as tabelas |

---

## 8. Aprendizados Consolidados

### 🔧 Engenharia & Arquitetura
1. **Multi-tenant por design:** Isolamento de dados via `empresa_id` em todas as tabelas evita cross-contamination entre clientes.
2. **Fallback de banco:** Engine SQLAlchemy com fallback automático SQLite → MySQL evita bloqueios no desenvolvimento local sem credenciais de nuvem.
3. **Pool de conexões otimizado:** Configurações `pool_pre_ping=True`, `pool_recycle=3600` e `max_overflow` eliminam timeouts "MySQL server has gone away" em ambientes PaaS (Railway/Render).
4. **Middleware de prefixo API:** Rota transparente `/api/*` no Next.js + middleware FastAPI que remove o prefixo evita configuração duplicada de CORS.
5. **Componente AlertDialog para ações críticas:** Confirmações de Assumir, Reabrir e Escalonar usam Shadcn `AlertDialog` com cor padrão `#1a3a5c` (decisão confirmada em projeto).

### 🎯 UX & Consistência Visual
6. **Design System via Shadcn/UI:** Componentização de UI garante botões, tabelas e dialogs consistentes em toda a aplicação (princípio DRY).
7. **Tema de cores bem definido:** Paleta `--primary: #1a3a5c` (azul escuro) + `--secondary: #3ba5d8` (azul claro) como variáveis CSS globais evita inconsistências de cor.
8. **Modo escuro nativo:** Suporte a tema claro/escuro via `next-themes` com variáveis CSS customizadas em `globals.css`.
9. **Status visualmente distintos:** Badges coloridos por status/prioridade no frontend espelham exatamente os Enums do backend (StatusChamado / Prioridade).

### 📦 Deploy & Infraestrutura
10. **Deploy 100% em nuvem sem backend local:** Arquitetura Vercel + Render + Railway atende à restrição de não depender de execução local (confirmada nas preferências do projeto).
11. **Proxy de API via Next.js rewrites:** Frontend chama `/api/*` sem precisar saber a URL do backend; o `next.config.mjs` decide por ambiente (dev vs prod).
12. **Procfile correto para Render:** Uso de `gunicorn` com worker `uvicorn.workers.UvicornWorker` é essencial para produção ASGI em plataformas PaaS.

### 🚀 Lógica de Negócio
13. **Atribuição automática baseada em prioridade-nível:** Mapeamento Baixa→N1, Média→N2, Alta→N3 reduz fila de espera e alinha complexidade com capacidade do técnico.
14. **Desatribuir ao escalonar:** Quando um chamado é escalonado, `atribuido_a_id = None` para que ele volte ao pool do próximo nível — evita "chamado perdido".
15. **Merge inteligente de especificações:** PATCH `/equipamentos` mescla (`{**current, **new}`) em vez de substituir o JSON `especificacoes`, evitando perda de dados.
16. **Cadastro em cascata seguro:** Criação de empresa usa `db.flush()` para obter `empresa_id` antes de criar o funcionário admin, tudo numa única transação com rollback em caso de erro.

### 🛡️ Segurança & Operações
17. **Tratamento de erros de validação:** Handler `RequestValidationError` global loga erros e retorna 422 estruturado — debug mais fácil em produção.
18. **Deleção segura de funcionários:** Antes de excluir, desatribui chamados, deleta notificações e desvincula histórico — evita `IntegrityError` 1451 por FK.
19. **Endpoints de diagnóstico SMTP:** Rota `/debug-email` direto no backend acelera troubleshooting de envio de e-mails sem precisar tocar no código.
20. **Logs de auditoria obrigatórios:** Toda ação sensível (login, criação, atualização, exclusão) registra `usuario_id`, `empresa_id` e `ip` — requisito de compliance e troubleshooting.

### 📊 Dados & Relatórios
21. **Relatórios consomem endpoint real:** `/api/stats/relatorios` entrega métricas de SLA, níveis e técnicos — o frontend não calcula nada, apenas exibe e exporta.
22. **Exportação PDF client-side:** jsPDF + AutoTable no frontend tira carga do backend e permite customização rápida do layout.
23. **Especificações JSON flexíveis:** Campo `especificacoes` do equipamento evita migrations excessivas; SO, RAM e HD são adicionados sem alterar schema.

### ⚠️ Armadilhas Evitadas (Lições Aprendidas)
24. **Colunas ausentes no SQLite causam 500:** Erro 500 no cadastro de equipamentos por falta de coluna `especificacoes` exigiu migration manual (`add_column.py`); sempre validar schema após alterações no ORM.
25. **Enums vs strings em migrações:** Ao trocar status de string para Enum, usar `migrate_enum.py` evita perda de dados existentes em produção.
26. **Turbopack/Build indicators desativados:** Decisão confirmada em projeto (`devIndicators.buildActivity = false` em `next.config.mjs`) evita distrações visuais desnecessárias.
27. **Senha truncada em 72 bytes:** Limitação bcrypt/banco exige truncamento antes do INSERT para evitar erro de tamanho de coluna.
28. **Prioridade "Crítica" foi removida:** Restrição de projeto — não incluir essa opção no cadastro/edição de chamados.

---

## 9. Links Úteis

| Recurso | URL |
|---------|-----|
| 🌐 Aplicação (Vercel) | https://swiftdesk.vercel.app |
| ⚙️ Backend API (Render) | https://swiftdesk-nvsl.onrender.com |
| 📖 Swagger UI (dev) | http://localhost:8000/docs |
| 🗃️ Diagramas de entidade-relacionamento | Ver modelos em [database.py](file:///c:/Users/User/Downloads/SwiftDesk/backend/database.py#L8-L261) |

---

*Projeto desenvolvido como solução de Help Desk empresarial com foco em simplicidade, rastreabilidade e performance de equipe.*
