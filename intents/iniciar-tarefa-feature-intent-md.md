# Intent — iniciar-tarefa feature: intent.md antes do branch

**Problema:** o pedido de uma frente vai direto da conversa ao branch e ao plano; nada registra o que doia, o que fica fora e como se prova que ficou pronto. O fiscal confere contra um plano que nasceu do zero, e o compatibilizador integra sem saber o que a frente prometeu.
**Resultado esperado:** toda frente nasce com um intent de uma tela, aprovado pela usuaria, commitado em `intents/<branch>.md`; o plano deriva dele e a nota de entrega confere o criterio de pronto item a item.
**Fora de escopo:** o `/compatibilizar` (ler intents na rodada fica para outra frente); hooks que exijam o intent por forca; o formato `spec.md` e `plan.md` do artigo da Anthropic.
**Criterio de pronto:** (1) o SKILL.md do `iniciar-tarefa` tem o passo do intent entre entender a tarefa e abrir o branch, com modelo de cinco campos e limite de 20 linhas; (2) o `fechar-tarefa` cita o intent na nota de entrega; (3) o frontmatter YAML das duas skills continua valido; (4) esta propria frente tem o seu intent commitado.
**Riscos e duvidas:** o plugin so passa a valer depois do upload ao catalogo da organizacao, que e manual; a convencao `intents/` nasce agora e os repositorios existentes nao a tem.
