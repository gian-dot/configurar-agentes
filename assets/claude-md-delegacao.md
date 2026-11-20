<!-- configurar-agentes:inicio -->
## Delegação de agentes

A sessão principal planeja e revisa. O volume da execução vai para executores mais baratos.

**Fluxo padrão para tarefas que mexem em vários arquivos:**

1. **Plano (sessão principal).** Escreva o plano direto, em blocos: objetivo, arquivos (com a função ou o trecho), o que mudar e o critério de pronto de cada bloco. O `planejador` (Opus) só entra em frente grande, que atravessa várias partes do projeto de uma vez.
2. **Execução.** Despache cada bloco ao `executor` (Sonnet) se exigir julgamento, ou ao `executor-rapido` (Haiku) se for mecânico. Prefira poucos blocos maiores: cada despacho tem custo de partida. Blocos sem dependência entre si vão juntos, em paralelo. Blocos que tocam os mesmos arquivos vão um de cada vez.
3. **Revisão (sessão principal).** Confira pelo que é verificável: {{COMO_VERIFICAR}}. Abra um arquivo inteiro só quando a verificação levantar dúvida. O relatório de um subagente é uma declaração, não uma prova.

**Regras:**

- Subagente não chama subagente. Quem despacha é sempre a sessão principal.
- Todo pedido a um subagente leva o bloco completo: objetivo, arquivos, o que mudar, o que não tocar e o critério de pronto. Ele não vê esta conversa, então dê os caminhos e trechos que você já conhece para ele não ter de procurar.
- Se o `executor-rapido` parar por ambiguidade, reenvie o bloco ao `executor`. Se o `executor` parar, o problema é do plano: resolva você.
- Tarefas pequenas (uma edição pontual, poucos arquivos, uma correção, uma resposta) a sessão principal faz direto. Delegar custa mais do que resolver.
- Buscas amplas no projeto vão para o `executor-rapido`, para manter o contexto da sessão principal limpo.
- Nada é dado como concluído ao usuário antes da revisão. O que não foi verificado é dito como não verificado.
<!-- configurar-agentes:fim -->
