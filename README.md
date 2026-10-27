# Plataforma UNIFEI

Plataforma web que integra três frentes da vida acadêmica: fórum por disciplina, trabalhos em grupo colaborativos e voluntariado com certificação. Trabalho de conclusão de curso de Ciência da Computação da Universidade Federal de Itajubá.

**Versão:** 1.1.2 | **Implantação:** [plataforma-unifei-revisao.onrender.com](https://plataforma-unifei-revisao.onrender.com) (plano gratuito: a primeira abertura depois de um período parado pode levar de 1 a 2 minutos)

---

## Visão geral

A plataforma reúne atividades que hoje ficam espalhadas entre o SIGAA, aplicativos de mensagem e arquivos pessoais:

- **Fórum acadêmico**: dúvidas organizadas por disciplina, com votação, marcação da melhor resposta, denúncia, moderação e busca por dúvidas parecidas.
- **Trabalhos em grupo**: formação por escolha ou sorteio, conversa privada em tempo real com anexos e áudio, material de apoio do professor e encaminhamento seletivo de dúvidas à monitoria.
- **Voluntariado com certificado**: ações presenciais e campanhas de doação, inscrição, aprovação pela organização e certificado em PDF com código de validação pública.

A interface muda conforme o perfil: coordenação, professor, monitor, estudante e organização parceira veem telas e têm permissões diferentes.

---

## Tecnologias

**Backend**
- Python 3.12, Django 5.2 e Django REST Framework
- Django Channels com Daphne, para WebSocket
- PostgreSQL com pgvector (busca semântica)
- Redis, como camada de canais do WebSocket e cache de tokens invalidados. É opcional em instância única (`USAR_REDIS=false`)
- SimpleJWT, com lista de tokens invalidados no logout
- WeasyPrint, para os certificados em PDF
- sentence-transformers, para a busca semântica (opcional, em `requirements-ia.txt`)

**Frontend**
- React e TypeScript, com Vite
- TailwindCSS v4
- Axios

**Infraestrutura**
- Docker em dois estágios (build do React e execução do Django)
- WhiteNoise, para servir os arquivos estáticos
- django-storages, para armazenamento de arquivos em serviço externo
- Render, para hospedagem

---

## Estrutura do repositório

```
plataforma-unifei-revisao/
├── config/            Configurações Django, ASGI, permissões e dados de teste
├── autenticacao/      Usuário, primeiro acesso, perfis e comandos de carga
├── forum/             Disciplina, publicação, resposta, voto, denúncia e moderação
├── colaboracao/       Trabalhos, grupos, conversa, pedido de ajuda, canal da monitoria e acervo
├── voluntariado/      Oportunidades, inscrições e certificados
├── notificacoes/      Notificações e entrega por WebSocket
├── busca/             Índice vetorial e busca de dúvidas parecidas
├── auditoria/         Registro de ações sensíveis
├── frontend/          Aplicação React + TypeScript (Vite)
├── deploy/            Script de inicialização e guia de implantação
├── docs/              Requisitos, matriz curricular (PPC) e roteiro de avaliação
├── Dockerfile         Imagem única (build do React + Django/Daphne)
└── render.yaml        Plano de implantação no Render
```

---

## Como executar

### Pré-requisitos
- Python 3.12 e Node.js 22
- PostgreSQL, com a extensão `pgvector` se for usar a busca semântica
- Redis, só quando houver mais de uma instância (em desenvolvimento é opcional)

### Desenvolvimento local

```bash
git clone https://github.com/kellyr5/plataforma-unifei-revisao.git
cd plataforma-unifei-revisao

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

# Frontend (em outro terminal)
cd frontend
npm ci
npm run dev
```

Para servir o build do React pelo próprio Django, sem o Vite, rode `npm run build` e inicie o servidor com `SERVIR_FRONTEND=true`.

### Com Docker

```bash
docker build -t plataforma-unifei .
docker run --env-file .env -p 8000:8000 plataforma-unifei
```

### Implantação

Veja [`deploy/LEIA-ME.md`](deploy/LEIA-ME.md) e o plano [`render.yaml`](render.yaml).

---

## Comandos de gerenciamento

| Comando | Para que serve |
|---------|----------------|
| `importar_matriz_ppc` | Importa as disciplinas do curso a partir do ementário do PPC |
| `cadastrar_participantes` | Cadastra pessoas em lote a partir de um CSV (modelo em `docs/participantes-exemplo.csv`) |
| `popular_demonstracao` | Povoa a base com um semestre de demonstração, sempre com o mesmo conteúdo |
| `criar_perfis_demo` | Cria um usuário de cada perfil. **Não use em produção**: as senhas são conhecidas |
| `indexar_forum` | Gera os vetores da busca semântica para as publicações existentes (`--tudo` refaz todos) |
| `sanear_vinculos` | Relata inconsistências nos vínculos com disciplinas, como mais de um monitor. Com `--aplicar` corrige o que é seguro |

---

## Funcionalidades

**Fórum**
- Perguntas por disciplina, com filtro por situação e ordenação
- Votação de respostas e marcação da melhor resposta
- Aviso de dúvidas parecidas ao escrever uma nova dúvida
- Denúncia de conteúdo, fila de moderação e restrição de publicação por decisão pedagógica
- Anexos com verificação do tipo real do arquivo

**Trabalhos em grupo e monitoria**
- Formação de grupos por escolha ou sorteio
- Conversa privada por grupo em tempo real, com anexos, áudio e indicação de presença
- Encaminhamento de uma mensagem marcada à monitoria, sem expor o resto da conversa
- Canal reservado entre professor e monitor de cada disciplina
- Acervo único de arquivos por disciplina, sem ampliar quem pode ver cada um

**Voluntariado**
- Publicação de ações presenciais e de campanhas de doação
- Inscrição, aprovação e conclusão pela organização
- Certificado em PDF com assinatura do responsável e código de validação pública

**Para toda a plataforma**
- Notificações em tempo real
- Painéis por perfil e painel de acompanhamento da coordenação
- Tema claro e escuro, adaptação ao celular e recursos de acessibilidade (WCAG 2.1)

---

## Testes e qualidade

```bash
python manage.py test            # 230 testes
python -m ruff check .           # lint do backend
python docs/gerar-requisitos.py  # regenera as planilhas de requisitos (requer openpyxl)
cd frontend && npx tsc -b        # verificação de tipos do frontend
```

---

## Estado atual

| Módulo | Situação | Observação |
|--------|----------|------------|
| Fórum | Pronto | Na implantação gratuita a busca semântica fica desligada e a busca opera em modo textual |
| Trabalhos em grupo e monitoria | Pronto | WebSocket com conversa em tempo real |
| Voluntariado | Pronto | Certificação automática |
| Testes automatizados | 230 testes | Cobrem permissões, privacidade e regras de negócio |
| Avaliação com usuários | Concluída | Pesquisa exploratória com 14 alunos e 6 monitores |
| Artigo | Concluído | Mantido fora do repositório, no modelo LaTeX do IMC/UNIFEI |

**Limitações conhecidas:** não há redefinição de senha por e-mail, nem limitação de taxa de requisições na API, e as mensagens do chat ficam em texto no banco (a proteção é por controle de acesso). A lista completa está na aba `Lacunas conhecidas` da planilha de requisitos não funcionais.

---

## Documentação

- [`docs/Requisitos_Funcionais_Plataforma_UNIFEI.xlsx`](docs/Requisitos_Funcionais_Plataforma_UNIFEI.xlsx): 97 requisitos funcionais e as divergências em relação ao planejamento do TCC1
- [`docs/Requisitos_Não_Funcionais_Plataforma_UNIFEI.xlsx`](docs/Requisitos_N%C3%A3o_Funcionais_Plataforma_UNIFEI.xlsx): 59 requisitos não funcionais e as lacunas conhecidas
- [`docs/avaliacao-usabilidade.md`](docs/avaliacao-usabilidade.md): roteiro original de avaliação com usuários
- [`docs/formulario-participante.md`](docs/formulario-participante.md): formulário do participante
- [`docs/ppc-cco.txt`](docs/ppc-cco.txt): ementário do curso usado para importar a matriz curricular
- [`deploy/LEIA-ME.md`](deploy/LEIA-ME.md): implantação gratuita (Render, Neon e R2)
- [`CHANGELOG.md`](CHANGELOG.md): histórico de versões
- Documentação da API em `/api/docs/` (Swagger) e `/api/redoc/`, com o servidor rodando

As planilhas de requisitos são geradas por [`docs/gerar-requisitos.py`](docs/gerar-requisitos.py). Para alterar um requisito, edite o script e rode-o de novo, em vez de editar a planilha à mão.

---

## Contribuindo

| Branch | Uso |
|--------|-----|
| `main` | Código estável. Cada versão publicada recebe uma tag `vX.Y.Z` |
| `develop` | Integração das próximas mudanças |
| `feature/*`, `fix/*`, `chore/*`, `docs/*` | Trabalho isolado, criado a partir de `develop` |

As mensagens de commit são escritas em português, de forma direta, dizendo o que mudou e por quê. Antes de integrar, rode `python manage.py test` e `python -m ruff check .`.

---

## Licença

Projeto acadêmico (TCC), Universidade Federal de Itajubá (UNIFEI).

---

## Autor

**Kelly dos Reis Leite**  
Desenvolvedor Backend | Análise de Dados | Python | Django  
[LinkedIn](https://linkedin.com/in/kelly-reis-a4b0ab194) | [GitHub](https://github.com/kellyr5)
