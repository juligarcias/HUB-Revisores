---
name: convencoes-git
description: >
  This skill should be used when the user asks about "convencoes de git",
  "padrao de branch", "como nomear branch", "como versionar", "criar tag",
  "fechar versao", "atualizar changelog", or performs any git operation
  (branch, commit, merge, tag) in MCAA team repositories, to ensure the
  team's branching, versioning and changelog conventions are followed.
metadata:
  version: "0.1.0"
  author: "MCAA Arquitetos"
---

# Convencoes de Git da equipe MCAA

Aplicar estas convencoes em toda operacao de Git nos repositorios da equipe.

## Branches

- Padrao de nome: **`modulo/tipo/descricao-curta`**
  - `modulo`: modulo do HUB afetado (revisor, compressor-pdf, visualizador-glb, hub-core, etc.)
  - `tipo`: `feature` | `fix` | `refactor`
  - `descricao-curta`: kebab-case, sem acentos, ate 5 palavras
  - Exemplo: `revisor/fix/timeout-pdfs-grandes`
- Sempre criar branches a partir da `main` atualizada (`git checkout main && git pull`).
- Nunca commitar diretamente na `main`.
- Branches sao de vida curta (1-3 dias): abrir, entregar, mergear via PR, deletar.
- Nunca colocar numero de versao no nome do branch.
- Em tarefas longas, incorporar a `main` periodicamente (`git merge main`) para reduzir divergencia.

## Versionamento (SemVer por modulo)

- Cada modulo tem linha de versao independente, registrada por **tags** com prefixo do modulo: `compressor-pdf/v1.3.0`.
- A versao e definida apenas no fechamento, olhando o que realmente entrou:
  - correcoes -> patch | funcionalidade nova -> minor | quebra de compatibilidade -> major
- Tags sao criadas na `main`, apos a compatibilizacao, pelo chat compatibilizador (`/compatibilizar`).
- Listar versoes de um modulo: `git tag --list 'modulo/*' --sort=-v:refname`

## Changelog (Keep a Changelog, com fragmentos)

- Frentes NAO editam o `CHANGELOG.md` diretamente (evita conflito entre chats paralelos).
- Cada frente registra suas mudancas num fragmento proprio em `changelog.d/`, criado pelo `/fechar-tarefa` (nome derivado do branch, formato Keep a Changelog).
- Categorias: Adicionado, Corrigido, Alterado, Removido.
- Redigir em portugues, focado no efeito para o usuario do modulo.
- A consolidacao dos fragmentos na secao `[Nao lancado]` do `CHANGELOG.md` e feita pelo chat compatibilizador, via `/compatibilizar`.
- Ao fechar uma versao, mover o conteudo de `[Nao lancado]` para a nova secao versionada com data (`## [1.3.0] - AAAA-MM-DD`).
- Intervalo de commits entre versoes: `git log modulo/v1.2.0..modulo/v1.3.0 --oneline`

## LGPD e seguranca

- Nunca incluir em commits, changelogs ou descricoes de PR: dados pessoais, nomes de clientes, credenciais, tokens, caminhos de servidores internos.
- Segredos vivem em variaveis de ambiente ou cofre, nunca no repositorio.

## Fluxo resumido

1. `/iniciar-tarefa modulo tipo descricao` -> intent aprovado + branch criado + intent commitado em `intents/` + plano aprovado
2. Trabalho com commits pequenos e frequentes
3. `/fechar-tarefa` -> fragmento de changelog em `changelog.d/` + push do branch + nota de entrega (PR opcional)
4. `/compatibilizar` (so no chat compatibilizador) -> merges na main + conflitos resolvidos + changelog consolidado + branches deletados + tag quando o ciclo do modulo fechar
