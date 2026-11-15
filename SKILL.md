---
name: configurar-agentes
description: Configura num projeto a arquitetura de agentes em camadas do Gian — Fable 5.2 como plan master e revisor na sessão principal, Opus como planejador minucioso, Sonnet e Haiku como executores conforme a complexidade — criando os agentes em .claude/agents, fixando o modelo em .claude/settings.json e a regra de delegação no CLAUDE.md. Use sempre que pedirem "configura os agentes", "monta a estrutura de agentes", "coloca o esquema Fable/Opus/Sonnet/Haiku aqui", "prepara esse projeto para delegar", ou quando quiserem reaplicar ou atualizar essa configuração num projeto já em curso, mesmo que não citem a palavra skill.
---

# Configurar agentes

Instala num projeto uma divisão de trabalho em quatro camadas, para que o modelo mais capaz gaste seu tempo decidindo e revisando, e os mais baratos façam o volume.

## A arquitetura

| Camada | Modelo | Onde roda | Papel |
|---|---|---|---|
| Plan master e revisor | Fable 5.2 | sessão principal | Entende o pedido, escreve o plano mestre, despacha o trabalho e revisa tudo antes de dar como pronto |
| Planejador minucioso | Opus | subagente `planejador` | Recebe uma frente do plano mestre e a desdobra em passos executáveis, cada um com arquivos, instruções, critério de pronto e nível de complexidade |
| Executor | Sonnet | subagente `executor` | Executa os passos classificados como complexos |
| Executor rápido | Haiku | subagente `executor-rapido` | Executa os passos simples e mecânicos, e faz buscas |

O fluxo é sempre: plano mestre (Fable) → plano detalhado (Opus) → execução (Sonnet ou Haiku) → revisão (Fable).

Um detalhe de funcionamento que molda tudo: subagente não chama outro subagente. O `planejador` não despacha os executores; ele devolve o plano detalhado à sessão principal, e é ela que distribui cada passo ao executor indicado. Por isso o plano detalhado precisa trazer o nível de cada passo já decidido.

## Passo 1 — Entender o projeto

Antes de escrever qualquer arquivo, leia o suficiente para saber o que o projeto faz: o `CLAUDE.md` (se existir), a estrutura de pastas, e os arquivos centrais. Os projetos do Gian nem sempre são de código (há vídeo, conteúdo, planejamento), então descreva o trabalho nos termos do projeto, sem presumir que existe build ou teste.

Anote, para usar nos agentes:
- o que o projeto produz e quais são as tarefas recorrentes;
- pastas e arquivos que importam, e os que não devem ser tocados;
- convenções (nomes, idioma, formato, tom);
- como se verifica que algo ficou certo (comando, render, conferência manual).

Registre só o que você leu no projeto. Se ele for novo ou tiver pouco material, o contexto fica curto, e tudo bem: um contexto curto e verdadeiro serve melhor do que um completo e inventado, porque os agentes vão tratar o que estiver ali como fato. O que for essencial e não der para descobrir (por exemplo, o que não pode ser tocado), pergunte ao usuário numa única mensagem antes de seguir.

Verifique também o que já existe: `.claude/agents/`, `.claude/settings.json`, `CLAUDE.md`. A raiz do projeto é a pasta que contém `.claude/` ou o `CLAUDE.md`; se a sessão estiver numa subpasta, suba até ela.

## Passo 2 — Criar os três agentes

Os modelos estão em `assets/agents/`, ao lado deste arquivo: `planejador.md`, `executor.md`, `executor-rapido.md`. Copie cada um para `.claude/agents/` do projeto e adapte duas coisas:

1. **A `description` do frontmatter.** É o que a sessão principal lê para decidir a quem delegar. Mantenha a frase-base e acrescente até três exemplos de tarefas que você viu no projeto. Uma descrição genérica faz o coordenador delegar mal, mas um exemplo inventado é pior: se não houver tarefa real para citar, deixe só a frase-base.
2. **A seção "Contexto deste projeto".** Troque o marcador `{{CONTEXTO_DO_PROJETO}}` pelo que você anotou no passo 1. O subagente começa do zero, sem ver a conversa; o que não estiver ali ele não sabe. O mesmo bloco de contexto serve aos três agentes.

Não mude o campo `model` nem o restante do corpo, a não ser que o usuário peça. Os modelos usam os apelidos `opus`, `sonnet` e `haiku` de propósito, para acompanharem a versão mais recente de cada um. Só o `planejador` tem `tools` restrito a leitura, porque ele não deve editar nada; os executores herdam todas as ferramentas, já que precisam delas para executar.

Se já existir um agente com o mesmo nome, mostre a diferença entre o atual e o novo e pergunte antes de substituir. O usuário pode ter ajustado o agente à mão.

## Passo 3 — Fixar o modelo da sessão principal

Em `.claude/settings.json` do projeto, defina `"model": "claude-fable-5-2"`. Se o arquivo existir, acrescente a chave e preserve todo o resto; se não existir, crie-o só com essa chave. Se já houver outro `model` definido, pergunte antes de trocar.

O ID aqui é exato, e não um apelido, porque o plan master foi escolhido como Fable 5.2 especificamente. Quando sair uma versão nova, a troca é uma decisão do usuário, feita nesta skill e reaplicada nos projetos.

Avise no relatório final que, no app desktop, o seletor de modelo da sessão prevalece sobre esse arquivo; vale conferir que a sessão está em Fable 5.2.

## Passo 4 — Regra de delegação no CLAUDE.md

Insira no `CLAUDE.md` do projeto o conteúdo de `assets/claude-md-delegacao.md`, incluindo os comentários marcadores de início e fim. Sem essa regra, a sessão principal tende a fazer tudo sozinha e os agentes ficam sem uso.

- Se o `CLAUDE.md` não existir, crie-o só com essa seção.
- Se existir e não tiver os marcadores, acrescente a seção no fim, sem alterar o restante.
- Se já tiver os marcadores, substitua apenas o trecho entre eles. É isso que permite rodar a skill de novo para atualizar.
- Se tiver só um dos dois marcadores, não tente adivinhar onde o trecho termina: mostre a situação ao usuário e pergunte.

## Passo 5 — Conferir e relatar

Confira que os três agentes têm frontmatter válido (`name`, `description`, `model`), que não sobrou nenhum `{{CONTEXTO_DO_PROJETO}}` e que o `settings.json` é JSON válido.

Entregue um relatório curto:
- arquivos criados e arquivos alterados;
- como as descrições foram adaptadas a este projeto;
- o que ficou em dúvida ou foi deixado como estava;
- o lembrete de que os agentes só aparecem em sessões abertas depois da criação.

## Limites

Mexa apenas em `.claude/agents/`, `.claude/settings.json` e `CLAUDE.md`. Não faça commit: o usuário decide quando versionar.

Se pedirem os agentes "para todos os projetos", crie-os em `~/.claude/agents/` com a seção de contexto genérica, e explique que agentes de projeto com o mesmo nome têm prioridade sobre os do usuário.
