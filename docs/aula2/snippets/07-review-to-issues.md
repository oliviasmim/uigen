# 07 — Fechando o ciclo: o Design Review vira issues

```ts
// agent/review-to-issues.ts
const runDesignReview = await loadReview();                 // src/lib/review/review.ts (Aula 1)
const review = await runDesignReview(model, nodes);         // DesignReview validado por zod
const drafts = reviewToIssues(review);                      // src/lib/review/schema.ts (contrato)

// dedup: cada issue carrega <!-- design-review-id: <slug> --> no corpo
if (id && existing.has(id)) continue;
await createIssue(draft); // gh issue create --label design-review --label severity:high
```

```yaml
# .github/workflows/review-to-issues.yml
on:
  workflow_dispatch:          # demo ao vivo: "rodar review agora"
  schedule:
    - cron: "0 9 * * 1"       # ou toda segunda, como se fosse o cliente
```

## O que o aluno deve perceber

Aqui as duas aulas se encontram. O structured output da Aula 1 (o
`DesignReview` com score, issues, severidade) deixa de ser só um card
bonito na UI e vira **entrada do ciclo de manutenção**:

```
Design Review → issues no GitHub → humano aplica `agent:ready`
→ agente corrige → PR draft com antes/depois → humano aprova
```

O pedaço que vale mostrar no código é a fronteira entre os dois
mundos: `reviewToIssues()` é uma função pura que transforma o objeto
validado em `{ title, body, labels }`. O workflow não conhece zod, não
conhece modelo — só conhece o contrato.

E repare no marcador `<!-- design-review-id: ... -->` no corpo de cada
issue: invisível para quem lê, mas é ele que impede o agendamento de
segunda-feira de abrir a mesma issue toda semana. Loop que roda sozinho
precisa ser idempotente.
