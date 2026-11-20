---
name: configurar-agentes
description: Configura num projeto a arquitetura de agentes do Gian — Opus 5.5 na sessão principal planejando e revisando, Sonnet e Haiku como executores conforme a complexidade, e um planejador Opus só para frentes grandes — criando os agentes em .claude/agents, fixando o modelo em .claude/settings.json e a regra de delegação no CLAUDE.md. Use sempre que pedirem "configura os agentes", "monta a estrutura de agentes", "coloca o esquema Opus/Sonnet/Haiku aqui", "prepara esse projeto para delegar", ou quando quiserem reaplicar ou atualizar essa configuração num projeto já em curso, mesmo que não citem a palavra skill.
---

# Configurar agentes

Instala num projeto uma divisão de trabalho em que a sessão principal decide e revisa, e modelos mais baratos fazem o volume da execução.

## A arquitetura

| Camada | Modelo | Onde roda | Papel |
|---|---|---|---|
| Plano e revisão | Opus 5.5 | sessão principal | Entende o pedido, escreve o plano em blocos, despacha e revisa tudo antes de dar como pronto |
| Executor | Sonnet | subagente `executor` | Executa os blocos que exigem julgamento |
| Executor rápido | Haiku | subagente `executor-rapido` | Executa os blocos mecânicos e faz buscas |
| Planejador (opcional) | Opus, effort medium | subagente `planejador` | Só para frente grande, que atravessa várias partes do projeto: desdobra o plano mestre em blocos |

O fluxo padrão é: plano (principal) → execução (Sonnet ou Haiku) → revisão (principal). O planejador entra no meio só em frente grande.

**Por que esse desenho.** Ele saiu da medição da primeira versão na NAVE Coletiva, em novembro de 2026. Lá, a sessão principal era o Fable 5.2 e todo pedido passava pelo planejador. O que trouxe resultado foi o método: plano com critério de pronto, teste antes do código e revisão pelo teste. Os commits de correção caíram de quase um por funcionalidade para um a cada três. O que só custou foi o resto:

- o Fable consome demais do limite mensal, e o Gian abre as sessões em Opus;
- um planejador em Opus abaixo de uma principal em Opus não economiza nada, e escrevia planos longos demais;
- a principal não ficava mais leve, porque revisava relendo os arquivos inteiros;
- cada despacho tem custo de partida, e passos miúdos multiplicavam esse custo.

Uma regra de funcionamento molda tudo: subagente não chama outro subagente. Quem distribui os blocos é sempre a sessão principal.

## Passo 1 — Entender o projeto

Antes de escrever qualquer arquivo, leia o suficiente para saber o que o projeto faz: o `CLAUDE.md` (se existir), a estrutura de pastas e os arquivos centrais. Os projetos do Gian nem sempre são de código (há vídeo, conteúdo, planejamento), então descreva o trabalho nos termos do projeto, sem presumir que existe build ou teste.

Anote, para usar nos agentes:
- o que o projeto produz e quais são as tarefas recorrentes;
- pastas e arquivos que importam, e os que não devem ser tocados;
- convenções (nomes, idioma, formato, tom);
- como se verifica que algo ficou certo: o comando de teste, de tipos ou de build, o render, ou a conferência manual. Isso vira o `{{COMO_VERIFICAR}}` do CLAUDE.md, e é a peça mais importante: a revisão é feita por ali.

Registre só o que você leu no projeto. Se ele for novo ou tiver pouco material, o contexto fica curto, e tudo bem. Um contexto curto e verdadeiro serve melhor do que um completo e inventado, porque os agentes vão tratar o que estiver ali como fato. O que for essencial e não der para descobrir (por exemplo, o que não pode ser tocado) pergunte ao usuário numa única mensagem antes de seguir.

Verifique também o que já existe: `.claude/agents/`, `.claude/settings.json`, `CLAUDE.md`. A raiz do projeto é a pasta que contém `.claude/` ou o `CLAUDE.md`. Se a sessão estiver numa subpasta, suba até ela.

## Passo 2 — Criar os agentes

Os modelos estão em `assets/agents/`, ao lado deste arquivo: `executor.md`, `executor-rapido.md` e `planejador.md`. Copie-os para `.claude/agents/` do projeto e adapte duas coisas:

1. **A `description` do frontmatter.** É o que a sessão principal lê para decidir a quem delegar. Mantenha a frase-base e acrescente até três exemplos de tarefas que você viu no projeto. Uma descrição genérica faz a principal delegar mal, mas um exemplo inventado é pior: se não houver tarefa real para citar, deixe só a frase-base.
2. **A seção "Contexto deste projeto".** Troque o marcador `{{CONTEXTO_DO_PROJETO}}` pelo que você anotou no passo 1. O subagente começa do zero, sem ver a conversa: o que não estiver ali ele não sabe. O mesmo bloco de contexto serve aos três agentes.

O planejador é opcional. Em projeto pequeno ou sem frentes grandes (um canal de vídeo, uma pasta de documentos), pergunte se vale criá-lo. Na dúvida, não crie: a principal planeja sozinha.

Não mude os campos `model` e `effort` nem o restante do corpo, a não ser que o usuário peça. Os agentes usam os apelidos `opus`, `sonnet` e `haiku` de propósito, para acompanharem a versão mais recente de cada um. Só o `planejador` tem `tools` restrito a leitura, porque não deve editar nada. Os executores herdam todas as ferramentas, porque precisam delas para executar.

Se já existir um agente com o mesmo nome, mostre a diferença entre o atual e o novo e pergunte antes de substituir: o usuário pode ter ajustado o agente à mão.

## Passo 3 — settings.json do projeto

Em `.claude/settings.json` do projeto:

1. Defina `"model": "claude-opus-5-5"`. Se o arquivo existir, acrescente a chave e preserve todo o resto. Se não existir, crie-o. Se já houver outro `model`, pergunte antes de trocar. O ID é exato, não um apelido: a troca de versão da sessão principal é uma decisão do usuário, feita nesta skill e reaplicada nos projetos.
2. **Se o plugin Superpowers estiver instalado** (procure `superpowers@` em `~/.claude/plugins/installed_plugins.json`), pergunte se o usuário quer desligá-lo neste projeto com `"enabledPlugins": {"superpowers@claude-plugins-official": false}`. Recomende desligar. O gancho dele injeta em toda sessão uma ordem de chamar uma skill antes de qualquer resposta, e as skills de plano, despacho, TDD e verificação dele repetem o método do CLAUDE.md. Dois métodos competindo pioram os dois. O desligamento vale só para o projeto: nos outros, o plugin continua.

No relatório final, avise que o seletor de modelo do app desktop prevalece sobre esse arquivo.

## Passo 4 — Regra de delegação no CLAUDE.md

Insira no `CLAUDE.md` do projeto o conteúdo de `assets/claude-md-delegacao.md`, incluindo os comentários marcadores de início e fim. Troque `{{COMO_VERIFICAR}}` pelos comandos ou conferências do passo 1, por exemplo "rode `npm test` e `npx tsc -b` e olhe o `git diff` do bloco". Se o projeto não tiver verificação automática, escreva a conferência real ("assista ao render", "leia contra o roteiro aprovado"). Sem essa regra, a sessão principal tende a fazer tudo sozinha e os agentes ficam sem uso.

- Se o `CLAUDE.md` não existir, crie-o só com essa seção.
- Se existir e não tiver os marcadores, acrescente a seção no fim, sem alterar o restante.
- Se já tiver os marcadores, substitua apenas o trecho entre eles. É isso que permite rodar a skill de novo para atualizar.
- Se tiver só um dos dois marcadores, não tente adivinhar onde o trecho termina: mostre a situação ao usuário e pergunte.

Se o planejador não foi criado, tire a frase sobre ele do item 1.

## Passo 5 — Conferir e relatar

Confira que:
- os agentes têm frontmatter válido (`name`, `description`, `model`);
- não sobrou nenhum `{{CONTEXTO_DO_PROJETO}}` nem `{{COMO_VERIFICAR}}`;
- o `settings.json` é JSON válido.

Entregue um relatório curto:
- arquivos criados e arquivos alterados;
- como as descrições foram adaptadas a este projeto;
- o que ficou em dúvida ou foi deixado como estava;
- o lembrete de que os agentes e a troca de plugin só valem em sessões abertas depois da mudança.

## Limites

Mexa apenas em `.claude/agents/`, `.claude/settings.json` e `CLAUDE.md`. Não faça commit: o usuário decide quando versionar.

Se pedirem os agentes "para todos os projetos", crie-os em `~/.claude/agents/` com a seção de contexto genérica, e explique que agentes de projeto com o mesmo nome têm prioridade sobre os do usuário.
