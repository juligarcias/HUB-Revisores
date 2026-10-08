---
description: >
  Integra os branches prontos na main - merges, conflitos, changelog
  consolidado e tag. DISPARAR AUTOMATICAMENTE apenas na sessao designada como
  compatibilizadora, quando o usuario pedir integracao ("integra",
  "compatibiliza", "roda a rodada", "junta os branches", "fecha o ciclo") ou
  mencionar branches entregues aguardando. NUNCA disparar em sessao de
  frente/obra: se houver duvida sobre o papel da sessao, perguntar antes.
allowed-tools: Bash(git:*), Read, Edit, Write, Grep, Glob
---

# Compatibilizar

Ritual de compatibilizacao da equipe MCAA. Este chat e o **coordenador de
compatibilizacao**: as frentes (outros chats) entregam suas pranchas (branches)
e SOMENTE ESTE FLUXO sobrepoe tudo, resolve os clashes e emite o conjunto
compatibilizado (a main). Executar as etapas na ordem.

## Regra de ouro

- Este e o UNICO fluxo autorizado a mergear e dar push na `main`.
- Chats de frente nunca rodam `/compatibilizar`; se houver duvida se esta
  sessao e o compatibilizador designado, perguntar ao usuario antes de comecar.
- Nunca compatibilizar com trabalho proprio nao commitado no diretorio:
  `git status` limpo e pre-requisito.

## Passo 1 — Levantamento das frentes

1. `git checkout main && git pull && git fetch --all --prune`
2. Listar os branches ainda nao integrados:
   `git branch -r --no-merged origin/main`
3. Para cada branch, coletar: ultimo commit (`git log -1 --format='%h %an %ar %s' <branch>`)
   e arquivos tocados (`git diff --name-only main...<branch>`).
4. Apresentar ao usuario um quadro de obra: branch, autor da frente, idade,
   arquivos tocados e SOBREPOSICOES (arquivos tocados por mais de um branch —
   esses sao os clashes previsiveis).

## Passo 2 — Ordem de integracao (portao de aprovacao)

Propor a ordem de merge e AGUARDAR aprovacao antes de executar:

- Dependencias primeiro (se a frente B usa codigo da frente A, A entra antes).
- Sem dependencia entre elas: o branch menor/mais antigo entra primeiro,
  para reduzir o tamanho de cada compatibilizacao.
- Branch com a pauta incompleta (sem fragmento de changelog, testes quebrando)
  fica de fora desta rodada: reportar ao usuario e devolver a frente.

## Passo 3 — Integrar um a um

Para cada branch, na ordem aprovada:

1. `git merge --no-ff origin/<branch>` a partir da main local.
2. Se houver conflito: acionar o protocolo completo da skill
   **resolver-conflitos** (baldes, intencao dos dois lados, plano aprovado,
   caca a conflitos semanticos).
3. Apos cada merge, rodar as verificacoes do modulo (build, lint, testes).
   Merge que compila nao e merge pronto: so avancar para o proximo branch
   com as verificacoes verdes.
4. Se um merge travar a fila (conflito grande, contradicao genuina),
   NAO pular para o proximo branch sem avisar: parar, reportar e decidir
   com o usuario se aborta (`git merge --abort`) ou resolve agora.

## Passo 4 — Consolidar o changelog

As frentes NAO editam o `CHANGELOG.md`: cada uma deixa um fragmento em
`changelog.d/` (gerado pelo `/fechar-tarefa`). Consolidar assim:

1. Listar os fragmentos: `ls changelog.d/*.md` (ignorar `.gitkeep`).
2. Ler cada fragmento e mover as entradas para a secao `[Nao lancado]` do
   `CHANGELOG.md` do modulo, agrupando por categoria
   (Adicionado, Corrigido, Alterado, Removido) e sem duplicar itens.
3. Apagar os fragmentos consolidados (`git rm changelog.d/<arquivo>.md`).
4. **LGPD**: conferir que nenhuma entrada contem dados pessoais, nomes de
   clientes, credenciais ou caminhos de servidores internos.
5. Mostrar o `[Nao lancado]` resultante ao usuario e aguardar aprovacao
   antes de commitar (`git commit -m "chore: consolida changelog da rodada"`).

## Passo 5 — Publicar e desmobilizar

1. `git push origin main`
2. Deletar os branches integrados (confirmar a lista com o usuario antes):
   `git push origin --delete <branch>` e `git branch -d <branch>` se existir local.
3. Se o ciclo do modulo fechou, sugerir a versao SemVer (conforme
   **convencoes-git**: correcao -> patch, funcionalidade -> minor,
   quebra -> major) e oferecer:
   `git tag modulo/vX.Y.Z && git push --tags`
   Ao fechar versao, mover o `[Nao lancado]` para a secao versionada com data.

## Passo 6 — Relatorio de compatibilizacao

Emitir o resumo da rodada (para registro interno ou descricao de release):

- Branches integrados, na ordem, e quem era o autor de cada frente
- Clashes encontrados e como cada um foi resolvido (e por que, quando um
  lado foi descartado)
- Verificacoes executadas e resultado
- Versao sugerida/criada e estado final da main

## Freios de emergencia

- Mais de ~10 arquivos em conflito num mesmo merge, ou conflitos em
  multiplos modulos: PARAR e sugerir dividir a rodada.
- Conflito envolvendo dados sensiveis, credenciais ou configuracao de
  producao: nunca resolver autonomamente (LGPD e seguranca).
- Dois branches com contradicao genuina (nao podem coexistir): a decisao
  e do usuario, nunca do agente.
