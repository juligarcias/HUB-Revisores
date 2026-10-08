---
description: >
  Abre uma frente de trabalho - intent aprovado e branch no padrao
  modulo/tipo/descricao. DISPARAR AUTOMATICAMENTE, sem esperar o comando,
  sempre que o usuario pedir qualquer alteracao de codigo (nova
  funcionalidade, correcao de bug, refactor, "vamos fazer", "implementa",
  "corrige", "ajusta") e a sessao ainda estiver na main ou sem branch de
  frente aberto. Nunca comecar a construir sem este ritual: primeiro o
  briefing, depois o canteiro, depois a obra.
allowed-tools: Bash(git:*), Read, Write, Grep, Glob
argument-hint: "[modulo] [tipo] [descricao da tarefa]"
---

# Iniciar tarefa

Ritual de abertura de frente de trabalho da equipe MCAA. Argumentos recebidos: $ARGUMENTS

## Passo 1 — Entender a tarefa

Interpretar os argumentos no formato `[modulo] [tipo] [descricao]`:
- **modulo**: qual modulo do HUB sera alterado (ex: revisor, compressor-pdf, visualizador-glb, hub-core)
- **tipo**: `feature` (funcionalidade nova), `fix` (correcao) ou `refactor` (reorganizacao sem mudanca de comportamento)
- **descricao**: o problema ou objetivo em poucas palavras

Se algum dos tres estiver faltando ou ambiguo, perguntar ao usuario antes de prosseguir. Nao inventar modulo nem tipo.

## Passo 2 — Redigir o intent (o briefing da frente)

Antes de qualquer branch ou plano, escrever o **intent**: o briefing que diz
qual problema a frente resolve e como se sabe que resolveu. E o programa de
necessidades da obra — o plano (Passo 6) deriva dele, nunca nasce do zero.

Propor ao usuario, em markdown, com estes cinco campos e nada mais:

```markdown
# Intent — <modulo> <tipo>: <descricao>

**Problema:** o que doi hoje, em uma ou duas frases.
**Resultado esperado:** o que muda quando a obra terminar, observavel por quem usa.
**Fora de escopo:** o que esta frente NAO vai tocar.
**Criterio de pronto:** como se prova que ficou pronto (teste, saida, comportamento).
**Riscos e duvidas:** o que pode dar errado ou ainda nao se sabe.
```

Regras do intent:
- **Uma tela, no maximo 20 linhas.** Se precisar de mais, a frente e grande
  demais: propor dividir em duas antes de seguir.
- Correcao pequena (um `fix` de poucas linhas) tem intent de tres linhas, mas
  tem. E o que garante que ate o conserto pequeno registre o que estava quebrado.
- Escrever o que o usuario disse, nao o que seria bonito. Se o pedido nao da
  para preencher "Problema" ou "Criterio de pronto", perguntar — e so uma
  pergunta por vez.
- "Fora de escopo" e proposta do Claude; o usuario corta ou acrescenta.

Mostrar o intent e **aguardar aprovacao explicita** antes de abrir o branch.
Este e o primeiro portao da frente: sem intent aprovado, nao ha canteiro.

## Passo 3 — Verificar o terreno

Executar e conferir:

1. `git status` — se houver alteracoes nao commitadas, avisar o usuario e perguntar se deseja guardar em stash antes de trocar de branch. Nunca descartar trabalho sem confirmacao.
2. `git checkout main && git pull` — sempre partir da main atualizada.

Se o diretorio principal tiver trabalho de outra sessao que nao pode ser
movido, abrir a frente num worktree proprio
(`git worktree add .claude/worktrees/<descricao> -b <branch> main`) em vez de
trocar de branch por cima do trabalho alheio.

## Passo 4 — Abrir o branch

Criar o branch no padrao **`modulo/tipo/descricao-curta`**, com a descricao em kebab-case, sem acentos, no maximo 5 palavras.

Exemplo: pedido "revisor fix timeout em PDFs grandes" vira:

```
git checkout -b revisor/fix/timeout-pdfs-grandes
```

Nunca incluir numero de versao no nome do branch — versao e definida apenas no fechamento, via tag.

## Passo 5 — Gravar o intent no branch

O intent aprovado e o **primeiro commit da frente**:

1. Garantir que a pasta `intents/` existe na raiz do repositorio (a mesma
   onde fica o `changelog.d/`; criar com um `.gitkeep` se for a primeira vez).
2. Gravar em `intents/<nome-do-branch-em-kebab>.md`
   (ex: branch `revisor/fix/timeout-pdfs-grandes` -> `intents/revisor-fix-timeout-pdfs-grandes.md`).
   Cada frente grava um arquivo diferente, entao o Git nunca ve conflito.
3. `git add intents/<arquivo>.md && git commit -m "intent: <descricao curta>"`

O arquivo viaja com o branch: o fiscal confere a obra contra o criterio de
pronto dele, e o intent fica disponivel para quem integrar a frente.

**LGPD**: nunca incluir no intent dados pessoais, nomes de clientes, caminhos
de servidores internos ou qualquer informacao sensivel.

## Passo 6 — Plano antes de executar

Antes de alterar qualquer arquivo, apresentar ao usuario um plano de ataque
resumido, **derivado do intent**:
- Arquivos que provavelmente serao tocados
- Estrategia da mudanca
- Como cada item do "Criterio de pronto" sera verificado
- Riscos ou duvidas (os do intent, mais os que a leitura do codigo revelou)

Aguardar aprovacao explicita do usuario antes de comecar a implementar. Este e
o segundo portao da frente: intent aprovado, branch aberto, plano aprovado, so
entao a obra comeca.
