# 03 — O loop (o coração da aula)

```ts
// agent/loop.ts — cabe numa tela. Isso É o "Loop Engineering".
for (let attempt = 1; attempt <= config.maxAttempts; attempt++) {
  const result = await generateText({
    model,
    instructions: systemPrompt,
    messages,
    tools,
    // Guardrail 1: teto de passos dentro da tentativa.
    stopWhen: stepCountIs(config.maxStepsPerAttempt),
  });

  totalTokens += result.totalUsage?.totalTokens ?? 0;

  // Guardrail 2: orçamento total de tokens. Estourou, encerra digno.
  if (totalTokens > config.maxTotalTokens) {
    return finish(false, attempt, true);
  }

  lastVerification = await verify(config.verifyCommands, config.workDir);
  if (lastVerification.ok) {
    return finish(true, attempt, false);
  }

  // O LOOP: o erro volta para o modelo como mensagem do usuário.
  messages.push(...result.response.messages);
  messages.push({ role: "user", content: verificationFeedback(/* ... */) });
}
```

## O que o aluno deve perceber

Um "agente em loop" nada mais é do que um `for` com `generateText`
dentro. A mágica que os gurus vendem por 5 mil reais está aqui: o modelo
edita, um verificador **de verdade** (lint + testes no shell) julga, e o
erro volta como próxima mensagem. Três tentativas, depois pede ajuda.

Três detalhes para apontar na tela:

1. `stopWhen: stepCountIs(10)` — sem teto, o modelo fica chamando tool
   para sempre (e cobrando por isso);
2. o orçamento de tokens é checado **em código**, não pedido por favor
   no prompt;
3. `messages.push(...)` — a conversa sobrevive entre tentativas, então
   na vez 2 o modelo já sabe o que tentou e quebrou.

Note a ausência: o agente **não tem tool de shell**. Ele não pode rodar
`npm test` sozinho nem marcar a própria prova. Modelo propõe, código
verifica.
