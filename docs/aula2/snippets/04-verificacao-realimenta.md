# 04 — A verificação realimentando o erro

```ts
// agent/prompts.ts — a saída real do lint/teste vira a próxima
// mensagem do usuário:
export function verificationFeedback(attempt: number, max: number, output: string): string {
  return [
    `A verificação falhou (tentativa ${attempt}/${max}). Saída real do verificador:`,
    "",
    "```",
    output, // ← as últimas linhas do npm test / eslint, sem edição
    "```",
    "",
    "Corrija o problema editando os arquivos novamente. ...",
  ].join("\n");
}

// agent/verify.ts — roda no shell do runner, não no modelo:
exec(command, { cwd, timeout: 5 * 60_000 }, (error, stdout, stderr) => { ... });
```

## O que o aluno deve perceber

A diferença entre "agente que chuta" e "loop de engenharia" é esta
função. Sem ela, o modelo declara vitória no primeiro edit — e ele
**não sabe que não sabe**.

Perceba o que viaja de volta: não é um resumo bonitinho, é a saída crua
do verificador (cortada nos últimos 4.000 caracteres para caber no
contexto). Stack trace, regra do ESLint, matcher do Vitest. O modelo é
bom em ler erro — a gente só precisa entregar o erro a ele.

E tem prova disso no teste: `agent/__tests__/loop.test.ts` força uma
falha de lint fake e confere que a string `"LINT ERRO simulado"` aparece
no prompt da 2ª tentativa. O loop tem teste — não é artigo de fé.
