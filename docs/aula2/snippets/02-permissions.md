# 02 — Permissions: o mínimo que o token precisa

```yaml
# .github/workflows/agent-loop.yml
permissions:
  contents: write      # criar branch e commitar a correção + prints
  pull-requests: write # abrir o PR draft
  issues: write        # comentar o resultado na issue
```

## O que o aluno deve perceber

Todo workflow roda com um token (`GITHUB_TOKEN`). Sem esse bloco, o repo
pode estar configurado com permissões amplas por padrão — ou seja, o
agente herda poder que não precisa.

A regra é listar só o que o ciclo usa: escrever branch, abrir PR,
comentar na issue. Nada de `actions: write` (o agente não pode mudar o
próprio workflow — senão uma issue maliciosa convenceria o agente a se
auto-promover).

Duas configurações do repo completam o quadro (estão no ROTEIRO, passo
de setup):

1. **Settings → Actions → General → "Allow GitHub Actions to create
   pull requests"** precisa estar ligado, senão o `gh pr create` falha;
2. PR criado com `GITHUB_TOKEN` **não dispara outros workflows** (é a
   proteção anti-recursão do GitHub). Se o projeto tiver CI de PR, a
   saída é usar um PAT ou um GitHub App. Para a aula, o GITHUB_TOKEN
   basta — e a limitação vira assunto de discussão.
