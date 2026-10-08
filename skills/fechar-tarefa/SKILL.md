---
description: >
  Fecha a frente de trabalho - fragmento de changelog, push do branch e nota
  de entrega (a integracao e do compatibilizador). DISPARAR AUTOMATICAMENTE,
  sem esperar o comando, quando a obra estiver aprovada (fiscal ok, testes
  verdes) ou quando o usuario sinalizar encerramento: "terminei", "pode
  entregar", "fecha a tarefa", "sobe isso", "finaliza". Nunca encerrar uma
  frente com commits entregues sem este ritual.
allowed-tools: Bash(git:*), Read, Edit, Write, Grep, Glob
---

# Fechar tarefa

Ritual de fechamento de frente de trabalho da equipe MCAA. Executar as etapas na ordem, como um checklist de medicao de obra.

**Divisao de responsabilidade**: esta skill fecha a FRENTE (o branch). Quem
integra na `main` — merges, resolucao de clashes, consolidacao do changelog e
tag — e o chat compatibilizador, via `/compatibilizar`. Este fluxo NUNCA
mergeia nem da push na `main`.

## Passo 1 — Conferir o estado do branch

1. `git branch --show-current` — confirmar que NAO estamos na main. Se estivermos, interromper e avisar o usuario.
2. `git status` — listar alteracoes pendentes. Se houver arquivos nao commitados, commitar com mensagem descritiva antes de prosseguir (confirmar a mensagem com o usuario).
3. `git log main..HEAD --oneline` — listar todos os commits desta frente de trabalho.

## Passo 2 — Gerar o fragmento de changelog

A frente NAO edita o `CHANGELOG.md` (e a area da prancha onde todo mundo
desenha — editar direto gera clash certo entre frentes paralelas). Em vez
disso, deixar um fragmento proprio:

1. Garantir que a pasta `changelog.d/` existe na raiz do modulo (criar com um
   `.gitkeep` se for a primeira vez).
2. Criar o arquivo `changelog.d/<nome-do-branch-em-kebab>.md`
   (ex: branch `revisor/fix/timeout-pdfs-grandes` -> `changelog.d/revisor-fix-timeout-pdfs-grandes.md`).
   Como cada frente cria um arquivo DIFERENTE, o Git nunca ve conflito.
3. Com base nos commits do Passo 1, redigir o conteudo no formato
   **Keep a Changelog**, usando apenas as categorias que se aplicam:

   ```markdown
   ### Corrigido
   - Timeout no processamento de PDFs grandes no Revisor
   ```

   Categorias: `### Adicionado` (funcionalidades novas), `### Corrigido`
   (correcoes de bug), `### Alterado` (mudancas em comportamento existente),
   `### Removido` (remocoes).

Escrever em portugues, frases curtas, focadas no efeito para quem usa o modulo (nao em detalhes internos de implementacao).

**LGPD**: nunca incluir no fragmento dados pessoais, nomes de clientes, caminhos de servidores internos ou qualquer informacao sensivel.

Mostrar o fragmento proposto ao usuario e aguardar aprovacao antes de gravar. Depois, commitar o fragmento junto da frente.

**Versao e tag NAO sao desta skill**: numero SemVer se decide olhando o
conjunto da rodada, no fechamento do ciclo do modulo — responsabilidade
exclusiva do `/compatibilizar`. Esta frente nao sugere versao nem cria tag.

## Passo 3 — Entregar a frente

1. `git push -u origin <branch-atual>` — e PARAR aqui. Nada de merge.
2. Redigir a nota de entrega da frente (para o PR, se a equipe quiser revisao
   humana, ou para o quadro de obra do compatibilizador):
   - **O que** foi feito (resumo em 1-2 frases)
   - **Por que** (o problema que motivou)
   - **Como testar** (passos objetivos)
   - O fragmento de changelog criado
3. PR e opcional (util quando a frente pede revisao de outra pessoa). Se o
   GitHub CLI (`gh`) estiver disponivel e o usuario quiser, criar com
   `gh pr create`. Quem mergeia, com ou sem PR, e o compatibilizador.

Avisar o usuario: a frente esta entregue e aguarda a proxima rodada de
`/compatibilizar`. Apos a integracao, o compatibilizador desmobiliza o branch.
