# 06 — O checkpoint humano: PR em draft com evidência

```yaml
# .github/workflows/agent-loop.yml
- name: Screenshot ANTES
  run: npx tsx agent/screenshot.ts --out antes.png
# ... agente edita, verificação passa ...
- name: Screenshot DEPOIS
  run: npx tsx agent/screenshot.ts --out depois.png
- name: Abrir PR draft
  run: gh pr create --draft --base main --head "$B" --body "..."
```

Corpo do PR (gerado de verdade pela run):

> **Plano do agente** — o resumo que o modelo escreveu ·
> **Verificação** — ✅ lint e testes passaram (tentativa 1 de 3, 11k tokens) ·
> **Arquivos alterados** — lista exata ·
> **Antes / Depois** — os dois prints lado a lado

## O que o aluno deve perceber

É aqui que mora o produto vendável. O agente nunca commita na `main`:
ele abre um PR **draft** com tudo que um revisor precisa decidir em 30
segundos — o que mudou, prova de que os testes passam e os prints de
antes/depois.

O humano tem três caminhos: marca "Ready for review" e faz merge, pede
ajuste (comentário), ou fecha. A autonomia do agente termina exatamente
onde começa a responsabilidade de alguém.

Detalhe de implementação que rende pergunta boa: como a imagem entra no
PR? O print é commitado na própria branch (`agent-runs/issue-N/`) e o
corpo do PR referencia a URL `raw.githubusercontent.com`. Sem serviço
de upload, sem conta em banco de imagem.

Variante para discutir: dois checkpoints. O agente comenta o **plano**
na issue e só implementa quando alguém aplica `plan:approved`. Mais
lento, mais seguro. Depende de quanto vale um erro no seu contexto.
