---
description: >
  Abre uma frente de trabalho - branch no padrao modulo/tipo/descricao.
  DISPARAR AUTOMATICAMENTE, sem esperar o comando, sempre que o usuario pedir
  qualquer alteracao de codigo (nova funcionalidade, correcao de bug,
  refactor, "vamos fazer", "implementa", "corrige", "ajusta") e a sessao
  ainda estiver na main ou sem branch de frente aberto. Nunca comecar a
  construir sem este ritual: primeiro o canteiro, depois a obra.
allowed-tools: Bash(git:*), Read, Grep, Glob
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

## Passo 2 — Verificar o terreno

Executar e conferir:

1. `git status` — se houver alteracoes nao commitadas, avisar o usuario e perguntar se deseja guardar em stash antes de trocar de branch. Nunca descartar trabalho sem confirmacao.
2. `git checkout main && git pull` — sempre partir da main atualizada.

## Passo 3 — Abrir o branch

Criar o branch no padrao **`modulo/tipo/descricao-curta`**, com a descricao em kebab-case, sem acentos, no maximo 5 palavras.

Exemplo: pedido "revisor fix timeout em PDFs grandes" vira:

```
git checkout -b revisor/fix/timeout-pdfs-grandes
```

Nunca incluir numero de versao no nome do branch — versao e definida apenas no fechamento, via tag.

## Passo 4 — Plano antes de executar

Antes de alterar qualquer arquivo, apresentar ao usuario um plano de ataque resumido:
- Arquivos que provavelmente serao tocados
- Estrategia da mudanca
- Riscos ou duvidas

Aguardar aprovacao explicita do usuario antes de comecar a implementar. Este e o portao de aprovacao da equipe: branch aberto, plano aprovado, so entao a obra comeca.
