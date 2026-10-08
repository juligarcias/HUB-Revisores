### Adicionado
- `/iniciar-tarefa` passa a redigir e aprovar um **intent** (problema, resultado esperado, fora de escopo, criterio de pronto, riscos) antes do branch, gravado como primeiro commit da frente em `intents/<branch-em-kebab>.md`; o plano deriva dele
- `/iniciar-tarefa` abre a frente em worktree proprio quando o diretorio principal tem trabalho de outra sessao

### Alterado
- `/fechar-tarefa`: a nota de entrega aponta o intent e confere o criterio de pronto item a item, com ressalva explicita quando algo nao foi atendido
- `convencoes-git`: fluxo resumido passa a citar o intent aprovado e commitado antes do branch
