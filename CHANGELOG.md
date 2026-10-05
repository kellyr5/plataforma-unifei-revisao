# Changelog

Formato baseado em [Keep a Changelog](https://keepachangelog.com/pt-BR/1.1.0/);
versionamento [SemVer](https://semver.org/lang/pt-BR/).

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
