---
name: resolver-conflitos
description: >
  This skill should be used when the user asks to "resolver conflitos",
  "resolver merge", "compatibilizar branches", "terminar o rebase", or when
  a git merge, rebase, or cherry-pick results in conflicts (CONFLICT markers,
  "fix conflicts and run git rebase --continue", unmerged paths in git status)
  in MCAA team repositories. Provides a structured protocol to resolve
  conflicts preserving the intent of both sides.
metadata:
  version: "0.1.0"
  author: "MCAA Arquitetos"
---

# Resolver conflitos - Protocolo de compatibilizacao MCAA

Conflito de merge e reuniao de compatibilizacao: duas disciplinas desenharam
sobre a mesma area da prancha. O objetivo NAO e escolher um vencedor - e
integrar as duas intencoes. Seguir o protocolo abaixo na ordem.

## Etapa 1 - Detectar o contexto

Antes de tocar em qualquer arquivo, entender a situacao:

1. `git status` - listar os arquivos em conflito (unmerged paths).
2. Identificar a operacao em andamento: merge, rebase, cherry-pick ou revert
   (verificar a existencia de `.git/MERGE_HEAD`, `.git/rebase-merge/`,
   `.git/CHERRY_PICK_HEAD`). Em rebase, informar o passo atual
   (ex: "passo 3 de 12").
3. Identificar os dois lados: qual e "nosso" (ours) e qual e o que esta
   chegando (theirs) - em rebase os papeis se invertem em relacao ao merge;
   confirmar antes de raciocinar sobre os lados.

## Etapa 2 - Categorizar os arquivos em baldes

Classificar cada arquivo conflitado e aplicar a estrategia do balde:

| Balde | Exemplos | Estrategia |
|---|---|---|
| Lock files / gerados | package-lock.json, poetry.lock, *.min.js | NUNCA resolver na mao: aceitar um lado e REGENERAR com a ferramenta (npm install, poetry lock) |
| Changelog | CHANGELOG.md | Quase sempre manter AS DUAS entradas, ordenadas na secao correta |
| Criticos | migracoes de banco, settings.json, .env.example, configs do HUB, hooks | NUNCA resolver autonomamente: apresentar os dois lados + proposta e AGUARDAR aprovacao do usuario |
| Codigo comum | .py, .js, .ts, etc. | Protocolo completo das Etapas 3-5 |

## Etapa 3 - Entender a intencao dos dois lados

Para cada arquivo de codigo em conflito:

1. `git log -p -n 3 <lado-alvo> -- <arquivo>` - ver as mudancas recentes do
   arquivo no outro branch e entender O QUE o autor tentava fazer.
2. `git log --oneline main..HEAD -- <arquivo>` - relembrar a intencao do
   nosso proprio lado.
3. Resumir em uma frase a intencao de cada lado. Se nao for possivel resumir,
   a intencao nao foi entendida - investigar mais antes de resolver.

## Etapa 4 - Plano antes de resolver (portao de aprovacao)

Apresentar ao usuario um plano de resolucao ANTES de editar qualquer arquivo:

- Lista dos arquivos em conflito, com o balde e a intencao de cada lado
- Para cada um, a resolucao proposta: combinar / lado A / lado B / regenerar
- Destacar os casos ambiguos ou contraditorios

Regras de decisao:

- **Padrao: combinar.** Na maioria dos conflitos reais as duas mudancas sao
  validas e a resolucao correta integra ambas.
- **Um lado supera o outro** apenas quando uma versao claramente substitui a
  outra (ex: o outro lado corrigiu o mesmo bug de forma melhor).
- **Contradicao genuina** (as duas mudancas nao podem coexistir): a decisao e
  do usuario, nunca do agente.
- **Em duvida, parar e perguntar.** Pedir direcao e mais barato que desfazer
  um merge errado.

Aguardar aprovacao explicita antes de executar.

## Etapa 5 - Resolver e verificar

1. Editar os arquivos removendo TODOS os marcadores (<<<<<<<, =======, >>>>>>>).
2. Regenerar lock files quando aplicavel.
3. **Cacar conflitos semanticos**: o Git so acusa conflito de linha; verificar
   se as duas mudancas combinadas fazem sentido juntas - funcao renomeada num
   lado e chamada pelo nome antigo no outro, contrato de dados alterado,
   constante duplicada. Usar Grep nos simbolos alterados pelos dois lados.
4. Rodar as verificacoes do modulo: build, lint e testes disponiveis.
   Merge que compila nao e merge pronto.
5. `git add` dos arquivos resolvidos e continuar a operacao
   (`git merge --continue` / `git rebase --continue` / commit conforme o caso).
   Em rebase com multiplos passos, repetir o protocolo a cada novo conflito.

## Etapa 6 - Relatorio de compatibilizacao

Ao final, produzir um resumo para a descricao do PR:

- Arquivos que estavam em conflito e como cada um foi resolvido
- Decisoes tomadas e por que (especialmente onde um lado foi descartado)
- Verificacoes executadas e resultado

## Freios de emergencia

- Mais de ~10 arquivos em conflito ou conflitos em multiplos modulos:
  PARAR, reportar o tamanho do problema e sugerir avaliar `git merge --abort`
  e dividir a integracao em partes menores.
- Qualquer conflito envolvendo dados sensiveis, credenciais ou configuracao
  de producao: nunca resolver autonomamente (LGPD e seguranca).
- Se `git status` mostrar alteracoes nao commitadas alheias ao conflito,
  nao misturar: alertar o usuario antes de prosseguir.
