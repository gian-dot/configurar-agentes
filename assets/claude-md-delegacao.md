<!-- configurar-agentes:inicio -->
## Delegação de agentes

Este projeto trabalha em quatro camadas. A sessão principal (Fable 5.2) é o plan master e o revisor; ela não faz o volume da execução.

**Fluxo padrão para qualquer tarefa com mais de dois ou três passos:**

1. **Plano mestre (sessão principal).** Entenda o pedido e escreva o plano em nível alto: objetivo, frentes de trabalho, restrições e o que conta como pronto.
2. **Plano detalhado (`planejador`, Opus).** Envie o plano mestre ao `planejador`. Ele devolve os passos executáveis, cada um com arquivos, instruções, critério de pronto e nível (simples ou complexo). Leia o plano detalhado antes de despachar: corrija o que estiver errado ou peça uma nova versão.
3. **Execução.** Despache cada passo ao executor indicado no plano: `executor` (Sonnet) para os complexos, `executor-rapido` (Haiku) para os simples. Passos sem dependência entre si vão juntos, em paralelo. Passos que tocam os mesmos arquivos vão um de cada vez.
4. **Revisão (sessão principal).** Confira o resultado de cada passo contra o critério de pronto, olhando os arquivos e não só o relatório. O relatório de um subagente é uma declaração, não uma prova.

**Regras:**

- Subagente não chama subagente. Quem despacha é sempre a sessão principal.
- Todo pedido a um subagente leva o passo completo: objetivo, arquivos, instruções, o que não tocar e o critério de pronto. Ele não vê esta conversa.
- Se o `executor-rapido` parar por ambiguidade, reenvie o passo ao `executor`. Se o `executor` parar, o problema é do plano: resolva você ou devolva ao `planejador`.
- Tarefas pequenas (uma edição pontual, a leitura de um arquivo conhecido, uma resposta) a sessão principal faz direto. Delegar custa mais do que resolver.
- Buscas amplas no projeto vão para o `executor-rapido`, para manter o contexto da sessão principal limpo.
- Nada é dado como concluído ao usuário antes da revisão. O que não foi verificado é dito como não verificado.
<!-- configurar-agentes:fim -->
