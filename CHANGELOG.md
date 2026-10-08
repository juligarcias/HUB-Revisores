# Trecho para o CHANGELOG.md do plugin mcaa-workflow

## [0.5.1] - 2026-08-22

### Corrigido
- Frontmatter YAML invalido na skill iniciar-tarefa: o argument-hint com
  colchetes sem aspas era interpretado como lista malformada e reprovava a
  validacao do upload do plugin. Valor agora entre aspas.


## [0.5.0] - 2026-08-22

### Adicionado
- Hook de SessionStart ("porteiro do canteiro"): ao abrir qualquer sessao num
  repositorio da equipe, faz git fetch (timeout 10s) e avisa quantas frentes
  entregues aguardam compatibilizacao, listando os branches. Fora de
  repositorio git ou com tudo integrado, fica em silencio.
- Comando do hook e inline no hooks.json, sem script externo: contorna bug
  conhecido do Claude Code em que CLAUDE_PLUGIN_ROOT nao e preenchido em
  hooks de SessionStart de plugins (anthropics/claude-code#27145).

### Notas
- Se o aviso nao aparecer no inicio da sessao (bug historico de plugins nao
  surfacearem contexto de SessionStart), copiar o bloco de hooks para o
  settings.json do projeto — o formato e identico, sem o wrapper "hooks" externo.


## [0.4.0] - 2026-08-22

### Alterado
- Rituais /iniciar-tarefa, /fechar-tarefa e /compatibilizar passam a ter
  disparo automatico: as descriptions agora incluem os gatilhos de contexto
  para o modelo invocar sozinho (pedido de alteracao de codigo sem branch ->
  iniciar; obra aprovada ou sinal de encerramento -> fechar; pedido de
  integracao na sessao compatibilizadora -> compatibilizar). Os comandos de
  barra continuam funcionando; os portoes de aprovacao dentro dos rituais
  permanecem manuais.
- /compatibilizar: gatilho automatico restrito a sessao designada; em sessao
  de frente nunca dispara.


## [0.3.0] - 2026-08-22

### Adicionado
- Skill `/compatibilizar`: fluxo do chat compatibilizador, unico autorizado a
  integrar branches na main — levantamento das frentes com mapa de
  sobreposicoes, ordem de merge aprovada pelo usuario, resolucao de clashes
  via protocolo `resolver-conflitos`, consolidacao do changelog, push, tag e
  relatorio de compatibilizacao.
- Padrao de fragmentos de changelog em `changelog.d/`: cada frente registra
  suas mudancas em arquivo proprio, eliminando conflitos no `CHANGELOG.md`
  entre chats paralelos.

### Alterado
- Skill `/fechar-tarefa`: passa a gerar fragmento em `changelog.d/` em vez de
  editar o `CHANGELOG.md` diretamente, e encerra no push do branch — merge e
  fechamento de versao migram para o `/compatibilizar`. Sugestao de versao
  SemVer removida (exclusiva do compatibilizador). PR torna-se opcional
  (apenas para revisao humana).
- Skill `convencoes-git`: secao de changelog reescrita para o padrao de
  fragmentos; tags passam a ser criadas pelo chat compatibilizador; fluxo
  resumido atualizado com a rodada de `/compatibilizar`.

### Notas de migracao
- Designar explicitamente qual sessao do Claude Code e o compatibilizador.
- Criar a pasta `changelog.d/` (com `.gitkeep`) na raiz de cada modulo.
- Frentes ja abertas com edicao direta no `CHANGELOG.md` podem fechar no
  fluxo antigo; novas frentes adotam o fragmento.
