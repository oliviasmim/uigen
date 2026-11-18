# 01 — O gatilho: uma label

```yaml
# .github/workflows/agent-loop.yml
on:
  issues:
    types: [labeled]

jobs:
  agent:
    if: github.event_name == 'workflow_dispatch' || github.event.label.name == 'agent:ready'
```

## O que o aluno deve perceber

O ciclo não começa no código. Começa numa decisão humana: alguém com
permissão de escrita no repo aplica a label `agent:ready` na issue.

Label nada mais é do que um botão de "pode ir" disfarçado. E aqui está o
detalhe de segurança: num repo público qualquer pessoa abre issue, mas
**só quem tem permissão de escrita coloca label**. O gatilho já vem com
o porteiro embutido, de graça.

`workflow_dispatch` é o botão manual para a demo ao vivo: se a live
travar no webhook, dá para disparar na mão informando o número da issue.

Ponto extra para discutir com a turma: por que não `on: issues: types:
[opened]`? Porque aí toda issue aberta ligaria o agente — inclusive
issue escrita por estranho com prompt injection dentro. O texto da issue
é input não confiável (volta no snippet 05).
