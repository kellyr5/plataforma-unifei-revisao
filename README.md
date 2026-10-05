# Plataforma UNIFEI

> Plataforma web que integra três frentes da vida acadêmica: fórum disciplinar, trabalhos em grupo colaborativos e voluntariado com certificação.

**Status:** Em desenvolvimento | **Deploy:** [plataforma-unifei.onrender.com](https://plataforma-unifei.onrender.com)

---

## 📋 Visão Geral

A Plataforma UNIFEI centraliza as atividades acadêmicas dispersas entre SIGAA, aplicativos de mensagem e arquivos pessoais, oferecendo:

- **Fórum Acadêmico** — Dúvidas organizadas por disciplina/período com votação, marcação de melhor resposta, moderação e busca semântica
- **Trabalhos em Grupo** — Formação flexível, conversa em tempo real com áudio/anexos, material de apoio integrado e fila de dúvidas para monitoria
- **Voluntariado com Certificado** — Ações presenciais e campanhas de doação com emissão de PDF verificável como atividade complementar

**Interface adaptativa:** Coordenação, Professor, Monitor, Estudante e Organização Parceira têm visualizações e permissões distintas.

---

## 🛠️ Stack Tecnológico

### Backend
- **Python 3.10** | **Django 5.2** | **Django REST Framework**
- **Django Channels + Daphne** — WebSocket para comunicação em tempo real
- **PostgreSQL + pgvector** — Busca semântica e embedding
- **Redis** — Cache e fila de tarefas
- **SimpleJWT** — Autenticação com lista de negação
- **WeasyPrint** — Geração de PDFs
- **sentence-transformers** — NLP para busca semântica

### Frontend
- **React + TypeScript** — Componentes tipados
- **Vite** — Build otimizado
- **TailwindCSS v4** — Estilização
- **Axios** — Cliente HTTP

### Infraestrutura
- **Docker** multiestágio
- **WhiteNoise** — Servir assets estáticos
- **django-storages** — Armazenamento em nuvem
- **Render** — Hospedagem

---

## 📁 Estrutura de Diretórios

```
plataforma-unifei/
├── config/            # Configurações Django, ASGI, permissões e fábricas de teste
├── autenticacao/      # Usuário, primeiro acesso, perfis e comandos de carga
├── forum/             # Disciplina, publicação, resposta, voto, denúncia e moderação
├── colaboracao/       # Trabalhos, grupos, conversa, pedido de ajuda, canal da monitoria, acervo
├── voluntariado/      # Oportunidades, inscrições e certificados
├── notificacoes/      # Notificações e entrega por WebSocket
├── busca/             # Índice vetorial e busca de dúvidas parecidas
├── auditoria/         # Registro de ações sensíveis
├── frontend/          # Aplicação React + TypeScript (Vite)
├── deploy/            # Script de inicialização e guia de implantação
├── docs/              # Estado do projeto, matriz curricular e roteiro de avaliação
├── artigo/            # Texto do TCC em LaTeX
├── Dockerfile         # Imagem única (build do React + Django/Daphne)
└── render.yaml        # Blueprint de implantação no Render
```

---

## 🚀 Como Executar

### Pré-requisitos
- Python 3.12, Node.js 22
- PostgreSQL com a extensão `pgvector` (busca semântica opcional)
- Redis (camada de canais do WebSocket; opcional em desenvolvimento)

### Desenvolvimento local

```bash
git clone https://github.com/kellyr5/plataforma-unifei.git
cd plataforma-unifei

# Backend
python -m venv venv
source venv/bin/activate        # No Windows: venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env            # preencha SECRET_KEY e as credenciais do banco
python manage.py migrate
python manage.py importar_matriz_ppc docs/ppc-cco.txt --curso CCO --nome "Ciencia da Computacao" --ano 2026
python manage.py criar_perfis_demo --reset
python manage.py popular_demonstracao
python manage.py runserver

# Frontend (outro terminal)
cd frontend
npm ci
npm run dev
```

Para servir o build do React pelo próprio Django (sem o Vite), gere `npm run build`
e inicie o servidor com `SERVIR_FRONTEND=true`.

### Com Docker

```bash
docker build -t plataforma-unifei .
docker run --env-file .env -p 8000:8000 plataforma-unifei
```

### Implantação

Veja [`deploy/LEIA-ME.md`](deploy/LEIA-ME.md) e o blueprint [`render.yaml`](render.yaml).

---

## ✅ Funcionalidades

### Fórum Acadêmico
- ✓ Perguntas por disciplina/período
- ✓ Votação de respostas
- ✓ Marcação de melhor resposta
- ✓ Busca de dúvidas parecidas (semântica opcional) ao criar uma dúvida
- ✓ Denúncia de conteúdo e fila de moderação

### Trabalhos em Grupo
- ✓ Formação por escolha ou sorteio
- ✓ Chat privado em tempo real (WebSocket)
- ✓ Compartilhamento de anexos
- ✓ Material de apoio integrado
- ✓ Fila de dúvidas para monitoria/professores

### Voluntariado
- ✓ Publicação de ações presenciais
- ✓ Campanhas de doação
- ✓ Certificado PDF com código de verificação
- ✓ Integração com atividades complementares

---

## 🧪 Testes

```bash
python manage.py test            # 230 testes
python -m ruff check .           # lint do backend
python docs/gerar-requisitos.py  # regenera as planilhas de RF/RNF (requer openpyxl)
cd frontend && npx tsc -b        # tipos do frontend
```

---

## 📊 Estado Atual

| Módulo | Status | Detalhes |
|--------|--------|----------|
| Fórum | ✓ Pronto | Implementado com busca semântica |
| Trabalhos em Grupo | ✓ Pronto | WebSocket + chat em tempo real |
| Voluntariado | ✓ Pronto | Certificação automatizada |
| Testes | ✓ Pronto | Cobertura completa |
| **Pendências** | | |
| Validação UX | ⏳ Etapa final TCC | Testes com usuários reais (Seção 4.3) |
| Documentação | ⏳ Em andamento | ER atualizado (26 tabelas), artigo em conclusão |

---

## 📚 Documentação

- [`docs/estado-do-projeto.md`](docs/estado-do-projeto.md) — decisões de projeto e estado atual
- [`docs/Requisitos_Funcionais_Plataforma_UNIFEI.xlsx`](docs/Requisitos_Funcionais_Plataforma_UNIFEI.xlsx) e [`docs/Requisitos_Não_Funcionais_Plataforma_UNIFEI.xlsx`](docs/Requisitos_N%C3%A3o_Funcionais_Plataforma_UNIFEI.xlsx) — 86 requisitos funcionais e 51 não funcionais (gerados por `docs/gerar-requisitos.py`)
- [`docs/avaliacao-usabilidade.md`](docs/avaliacao-usabilidade.md) — roteiro de avaliação com usuários
- [`deploy/LEIA-ME.md`](deploy/LEIA-ME.md) — implantação gratuita (Render + Neon + R2)
- [`CHANGELOG.md`](CHANGELOG.md) — histórico de versões
- Swagger da API em `/api/schema/swagger-ui/` (com o servidor rodando)

---

## 🤝 Contribuindo

Fluxo de branches:

| Branch | Uso |
|--------|-----|
| `main` | Código estável; cada versão publicada recebe uma tag `vX.Y.Z` |
| `develop` | Integração das próximas mudanças |
| `feature/*`, `fix/*`, `chore/*` | Trabalho isolado, criado a partir de `develop` e integrado com PR |

Mensagens de commit seguem [Conventional Commits](https://www.conventionalcommits.org/pt-br/)
(`feat:`, `fix:`, `chore:`, `docs:`, `test:`). Antes de abrir o PR, rode
`python manage.py test` e `python -m ruff check .`.

---

## 📝 Licença

Projeto acadêmico (TCC) — Universidade Federal de Itajubá (UNIFEI)

---

## 👤 Autor

**Kelly Reis**  
Desenvolvedor Backend | Análise de Dados | Python | Django  
[LinkedIn](https://linkedin.com/in/kelly-reis-a4b0ab194) | [GitHub](https://github.com/kellyr5)
