# Base de Conhecimento

## Dados Utilizados

| Arquivo | Formato | Utilização no Agente |
|---------|---------|---------------------|
| `historico_atendimento.csv` | CSV | Contextualizar interações anteriores do cliente com o agente (dúvidas já respondidas, temas recorrentes), evitando repetir explicações e permitindo continuidade entre conversas |
| `perfil_investidor.json` | JSON | Adaptar o tom e o nível de detalhe das respostas ao perfil de risco do cliente (conservador, moderado, arrojado) e travar sugestões de produtos/investimentos quando o perfil não estiver definido |
| `produtos_financeiros.json` | JSON | Sugerir produtos financeiros (ex: poupança, CDB, reserva de emergência) compatíveis com o perfil e o objetivo do cliente, sempre citando a fonte do produto na base |
| `transacoes.csv` | CSV | Base principal para a função central do agente: categorizar gastos, identificar padrões de consumo, calcular saldo disponível e gerar alertas de orçamento |

> [!TIP]
> **Quer um dataset mais robusto?** É possível complementar com datasets públicos do [Hugging Face](https://huggingface.co/datasets) relacionados a finanças, desde que adequados ao contexto do desafio.

---

## Adaptações nos Dados
> Você modificou ou expandiu os dados mockados? Descreva aqui.

**`produtos_financeiros.json` foi expandido** de um catálogo genérico para 15 produtos reais do mercado financeiro brasileiro, organizados em 6 categorias: Renda Fixa (Poupança, CDB liquidez diária, CDB prefixado, LCI, LCA), Títulos Públicos (Tesouro Selic, Tesouro IPCA+), Fundos de Investimento (Fundo DI, Multimercado, Ações), Renda Variável (Ações avulsas, FII), Previdência (PGBL, VGBL) e Ativos Digitais (Criptoativos).

Cada produto ganhou campos padronizados para permitir que o agente filtre por compatibilidade antes de sugerir algo:
- `risco` (Muito baixo → Muito alto)
- `perfil_compativel` (lista de perfis de investidor aos quais o produto é adequado)
- `liquidez` (prazo de resgate)
- `protegido_fgc` (se tem garantia do Fundo Garantidor de Créditos)
- `isento_ir` (quando aplicável)

Isso permite uma regra de negócio simples e auditável: **o agente só pode citar um produto se `perfil_compativel` contiver o perfil declarado do cliente** (`perfil_investidor.json`) — reforçando a estratégia anti-alucinação de não recomendar nada fora do perfil de risco.

> Arquivo completo em `data/produtos_financeiros.json`.

**`transacoes.csv` foi expandido** de poucas linhas de exemplo para **3 meses completos** (maio a julho/2026), com colunas `data`, `descricao`, `categoria`, `tipo` (entrada/saída) e `valor`. As categorias (Renda, Moradia, Alimentação, Transporte, Saúde, Assinaturas, Lazer, Educação, Investimentos, Cartão) são consistentes ao longo dos 3 meses, o que permite ao agente comparar gastos mês a mês e identificar tendências reais (ex: aumento de gastos com transporte em julho) em vez de trabalhar com um recorte único e estático.

**`historico_atendimento.csv` foi expandido** para 8 interações cobrindo os principais tipos de pergunta que o agente deve saber tratar: dúvida conceitual (poupança vs. CDB), estouro de orçamento, definição de reserva de emergência, dúvida sobre perfil de risco e, principalmente, uma tentativa de pedir um produto **fora do perfil declarado** (criptoativos para um perfil Moderado) — cenário usado depois como caso de teste na seção de Edge Cases dos prompts.

**`perfil_investidor.json` foi expandido** de um objeto simples (nome + perfil) para uma estrutura completa: dados cadastrais, resultado do questionário de suitability (com pontuação por pergunta, não só o rótulo final), objetivos financeiros com valor de meta e progresso atual, situação atual (reserva de emergência em meses, dívidas ativas, produtos já em carteira) e preferências de comunicação. Isso dá ao agente contexto suficiente para responder perguntas como "estou no caminho certo para minha meta?" sem precisar inventar números.

---

## Estratégia de Integração

### Como os dados são carregados?
> Descreva como seu agente acessa a base de conhecimento.

Os arquivos CSV/JSON da pasta `data/` são carregados no início da sessão (ex: via `pandas.read_csv` e `json.load`) e mantidos em memória durante a conversa. Não há banco de dados externo nesta fase — a base é estática e local, recarregada a cada nova sessão do agente.

### Como os dados são usados no prompt?
> Os dados vão no system prompt? São consultados dinamicamente?

Modelo híbrido:
- **Perfil do cliente e produtos financeiros** (`perfil_investidor.json`, `produtos_financeiros.json`) são resumidos e incluídos diretamente no **system prompt**, já que são poucos dados, mudam raramente durante a sessão e precisam estar sempre disponíveis para o agente não recomendar nada incompatível com o perfil.
- **Transações e histórico de atendimento** (`transacoes.csv`, `historico_atendimento.csv`) são consultados **dinamicamente**: a cada pergunta do usuário, o agente filtra/agrega apenas o recorte de dados relevante (ex: transações do mês atual, últimas 3 interações) e injeta esse recorte no contexto da chamada — evitando estourar o limite de contexto e reduzindo o risco de o modelo "confundir" períodos ou categorias.

---

## Exemplo de Contexto Montado
> Mostre um exemplo de como os dados são formatados para o agente.

```
[SYSTEM PROMPT — dados fixos da sessão]
Perfil do Cliente:
- Nome: João Silva
- Perfil de investidor: Moderado (avaliado em 15/06/2026, válido até 15/12/2026)
- Reserva de emergência: cobre 2,1 meses de despesas (meta: 12 meses)
- Possui dívida ativa: fatura de cartão parcelada

Produtos financeiros disponíveis (filtrados por perfil_compativel = "Moderado"):
- CDB Liquidez Diária — 100% CDI, protegido pelo FGC
- Tesouro Selic — garantido pelo Tesouro Nacional
- LCI/LCA — isentas de IR
- Fundo Multimercado Moderado — meta CDI + 2% a.a.

[CONTEXTO DINÂMICO — injetado conforme a pergunta]
Transações de julho/2026 (recorte relevante):
- 01/07: Salário - R$ 6.500,00 [Renda]
- 02/07: Aluguel - R$ 1.800,00 [Moradia]
- 07/07: iFood - R$ 110,30 [Alimentação]
- 09/07: Combustível - R$ 205,00 [Transporte]
- Total gasto com Alimentação em julho (até dia 12): R$ 253,80

Última interação registrada (historico_atendimento.csv):
- 08/07: Cliente perguntou sobre investir em criptoativos; agente explicou que o produto não é compatível com o perfil Moderado

[PERGUNTA DO USUÁRIO]
"Como estão meus gastos esse mês?"
```

> Observação: nenhum dado fora desse contexto é usado pelo agente para responder — se a informação não está no recorte acima, o Ocê Poupa admite que não tem esse dado (ver seção de Segurança e Anti-Alucinação da documentação principal).