# Pitch — Ocê Poupa

> Roteiro pensado para uma apresentação de 3 a 5 minutos.

---

## 1. Abertura / Gancho (30s)

> Objetivo: capturar atenção com o problema, não com o produto.

**Roteiro sugerido:**

"Quantas vezes vocês já chegaram no fim do mês sem saber exatamente pra onde foi o dinheiro? A maioria dos apps de banco mostra o extrato certinho — mas ninguém te explica o que fazer com aquilo. É esse o problema que o **Ocê Poupa** resolve."

---

## 2. O Problema (45s)

> Objetivo: deixar claro que é uma dor real, não um problema inventado pra justificar o produto.

- A maioria das pessoas não tem o hábito de acompanhar o próprio orçamento.
- Extratos e apps bancários mostram **dados**, mas não **interpretação**.
- Recomendações financeiras genéricas, sem levar em conta o perfil de risco real da pessoa, podem ser mais prejudiciais do que úteis.
- O usuário só percebe que estourou o orçamento quando já é tarde pra agir.

---

## 3. A Solução (45s)

> Objetivo: apresentar o Ocê Poupa como resposta direta a cada dor citada acima.

"O Ocê Poupa é um assistente financeiro pessoal que:
- Acompanha os gastos do cliente e organiza por categoria automaticamente;
- Avisa **antes** do orçamento estourar, não depois;
- Só sugere produtos financeiros compatíveis com o perfil de risco declarado — nunca empurra investimento genérico;
- Explica conceitos financeiros em linguagem simples, sem jargão.

O nome é uma homenagem à minha cidade, Belo Horizonte — mas o jeito de falar do agente é neutro, pra funcionar bem com qualquer usuário do Brasil."

---

## 4. Demonstração (90s)

> Objetivo: mostrar, não só contar. Preparar 2-3 interações reais no `app.py` antes da apresentação.

**Roteiro sugerido de demo (usar os dados mockados de João Silva):**

1. **Pergunta de rotina:** "Como estão meus gastos esse mês?"
   → Mostra o agente puxando dados reais de `transacoes.csv` e agrupando por categoria.

2. **Pergunta de acompanhamento de meta:** "Estou no caminho certo pra minha reserva de emergência?"
   → Mostra o agente calculando progresso real e identificando a dívida ativa como prioridade — evidência de que ele não dá conselho genérico, ele cruza os dados do cliente.

3. **Caso de guardrail:** "Quero investir em criptoativos"
   → Mostra o agente **recusando** recomendar um produto fora do perfil de risco do cliente, e explicando por quê.

> Dica: o terceiro exemplo costuma ser o mais impactante do pitch — mostra que o agente tem limites de segurança reais, não só fluência de conversa.

---

## 5. Diferenciais (30s)

- **Anti-alucinação por design**: toda resposta é ancorada nos dados do cliente; o agente admite quando não sabe algo.
- **Trava de perfil de risco**: nenhuma recomendação de produto sai do que é compatível com o perfil declarado.
- **Proatividade real**: o agente não espera ser perguntado para sinalizar risco (orçamento estourando, dívida ativa antes de investimento).
- **Persona com identidade, sem perder credibilidade**: nome com toque regional, mas comunicação neutra e profissional o suficiente para tratar de dinheiro.

---

## 6. Limitações Assumidas (15s)

> Objetivo: mostrar maturidade — um agente financeiro que "promete tudo" é menos confiável, não mais.

"O Ocê Poupa não executa transações, não substitui um consultor de investimentos certificado e não acessa dados de mercado em tempo real. Ele é um copiloto de organização financeira, não um piloto automático do seu dinheiro."

---

## 7. Próximos Passos (15s)

> Objetivo: mostrar visão de evolução do produto, não só do protótipo atual.

- Integração com Open Finance para importar transações automaticamente (hoje os dados são mockados).
- Expansão da base de conhecimento com mais produtos e cenários de teste.
- Métricas de uso reais para validar as metas definidas em `04-metricas.md`.

---

## 8. Fechamento (15s)

"O objetivo do Ocê Poupa não é substituir o julgamento financeiro de ninguém — é dar pra qualquer pessoa o mesmo tipo de acompanhamento próximo que hoje só quem tem um consultor particular tem acesso. Obrigado!"

---

## Perguntas Frequentes Esperadas (preparo para Q&A)

| Pergunta provável | Resposta sugerida |
|---------------------|----------------------|
| "O agente dá recomendação de investimento de verdade?" | Não no sentido regulado (suitability formal) — ele filtra produtos compatíveis com o perfil declarado, mas a decisão final e a validação profissional continuam sendo do usuário/consultor certificado. |
| "E se o agente errar um valor?" | O sistema é desenhado pra nunca inventar valor: se o dado não está na base, ele admite que não sabe em vez de arriscar um número. Isso é testado nos casos de teste do `04-metricas.md`. |
| "Por que o nome é mineiro mas ele não fala mineirês?" | Decisão de produto: a identidade fica na marca, não no vocabulário — pra não parecer caricatura nem prejudicar a credibilidade em um tema sério como dinheiro. |
| "Isso funciona com dados reais de banco?" | Hoje os dados são mockados para o protótipo; o próximo passo natural é integração via Open Finance. |

---

## Checklist Pré-Apresentação

- [ ] Testar a demo ao vivo pelo menos uma vez antes da apresentação
- [ ] Ter prints/vídeo de backup da demo, caso a API falhe ao vivo
- [ ] Cronometrar o pitch completo
- [ ] Revisar se os números citados no pitch batem com os dados atuais em `data/`