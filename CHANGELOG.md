# Changelog

Formato baseado em [Keep a Changelog](https://keepachangelog.com/pt-BR/1.1.0/);
versionamento [SemVer](https://semver.org/lang/pt-BR/).

## [Não lançado] - 2026-10-24

Somente documentação. Nenhuma linha do código da aplicação mudou.

### Alterado
- Requisitos revisados contra o código: **97 RF e 59 RNF** (eram 86 e 51). Entram 11 RF
  (canal da monitoria e seu arquivamento, presença na conversa, participantes da conversa,
  uma monitoria por disciplina, `sanear_vinculos`, `indexar_forum`, validação pública do
  certificado, assinatura do responsável, consulta da auditoria, `criar_perfis_demo`) e
  8 RNF (política de senha, proteções do navegador, origem do WebSocket, tentativas na
  ativação, hash do código, CPF mascarado na validação, idioma e fuso, log de conexões).
  Corrigida a descrição de RF-AUD-001, que dizia que a consulta era restrita à administração.
- Divergências em relação ao TCC1 ampliadas de 9 para 21 itens, e nova aba
  `Lacunas conhecidas` nos requisitos não funcionais.
- README reorganizado: endereço de implantação e URL de clone deste repositório, rota da
  documentação da API (`/api/docs/`), papel do Redis, contagens, tabela de comandos,
  limitações conhecidas, e removido o link para `docs/estado-do-projeto.md`, que não existe.
- `schema.yml` regenerado: passa a incluir o acervo de arquivos e a consulta de
  participantes da conversa.

## [1.1.2] - 2026-10-05

### Corrigido
- Busca textual (usada quando o modelo semântico está desligado, como no Render) procurava a
  frase inteira digitada, então um título com várias palavras quase nunca encontrava algo e
  as "dúvidas parecidas" ficavam vazias. Agora procura as palavras (sem as muito comuns,
  reduzidas ao radical) e ordena por quantas aparecem. Suíte: 230 testes.

## [1.1.1] - 2026-10-05

### Corrigido
- `render.yaml` usava o nome `plataforma-unifei`, que fazia o Render associar e alterar o
  serviço já existente no workspace. Agora cria serviço (`plataforma-unifei-revisao`) e
  banco Postgres próprios, com `ALLOWED_HOSTS`/`CSRF_TRUSTED_ORIGINS` para `*.onrender.com`.

## [1.1.0] - 2026-10-05

### Adicionado
- Botão **Denunciar** no tópico e nas respostas do fórum, ligado à fila de moderação.
- Aviso de **dúvidas parecidas** na criação de uma dúvida, antes de publicar.
- Requisitos atualizados: RF-FOR-023 (dúvidas parecidas) e RNF-DES-008 (busca sem sobrecarga) novos; RF-FOR-012 e RNF-CON-004 revisados. Planilhas regeneradas por `docs/gerar-requisitos.py` (86 RF e 51 RNF).
- `render.yaml` (blueprint de implantação) e este changelog.
- Variável opcional `SERVIR_FRONTEND` para testar o build do React só com o Django.

### Corrigido
- `PermissionDenied` não importado em `colaboracao/api/views.py`: criar trabalho ou
  grupo sem ser professor retornava erro 500 em vez de 403.
- Testes do canal da monitoria (`CanalDeMonitoriaTests`) falhavam porque o callback
  `on_commit` nunca executava dentro da transação do teste. Suíte: 227 testes passando.

### Alterado
- README reescrito para refletir a estrutura real do repositório.
- Limpeza de imports não usados e ajustes de lint (`ruff`) no backend.

### Removido
- Logos, ícones e CSS sem uso do frontend (resto do template do Vite).

## [1.0.0] - 2026-09-09

Versão base estável: fórum por disciplina, trabalhos em grupo com chat e pedido de ajuda,
canal da monitoria, voluntariado com certificado e busca.
