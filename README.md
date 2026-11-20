# configurar-agentes

Skill para o Claude Code que instala num projeto uma arquitetura de agentes em que a sessão principal planeja e revisa, e modelos mais baratos executam:

| Camada | Modelo | Papel |
|---|---|---|
| Plano e revisão | Opus 5.5 (sessão principal) | Planeja em blocos, despacha e revisa pelo teste e pelo diff |
| Executor | Sonnet (`executor`) | Executa os blocos que exigem julgamento |
| Executor rápido | Haiku (`executor-rapido`) | Executa os blocos mecânicos e faz buscas |
| Planejador (opcional) | Opus, effort medium (`planejador`) | Só para frentes grandes |

## O que a skill faz num projeto

1. Lê o projeto para entender o que ele produz, suas convenções e como se verifica o trabalho.
2. Cria os agentes em `.claude/agents/`, com descrição e contexto adaptados ao projeto.
3. Define `"model": "claude-opus-5-5"` em `.claude/settings.json` e, se o Superpowers estiver instalado, pergunta se ele deve ser desligado no projeto.
4. Insere a seção "Delegação de agentes" no `CLAUDE.md`, entre marcadores, para poder ser atualizada depois.

Ela não sobrescreve agentes existentes sem perguntar e não faz commit.

## Histórico

- **v2 (nov/2026):** Opus na principal, no lugar do Fable 5.2. O planejador vira opcional e curto. O trabalho passa a ser dividido em blocos, e a revisão é feita pela verificação do projeto. A mudança veio da medição da v1 na NAVE Coletiva.
- **v1:** quatro camadas fixas: Fable, planejador Opus, Sonnet e Haiku.

## Instalação

Clone este repositório dentro da pasta de skills do usuário:

```bash
git clone https://github.com/gian-dot/configurar-agentes.git ~/.claude/skills/configurar-agentes
```

## Uso

Abra uma sessão do Claude Code na pasta do projeto e digite `/configurar-agentes`, ou peça "configura os agentes neste projeto". Depois abra uma sessão nova: agentes só aparecem em sessões iniciadas após a criação.

Para atualizar um projeto já configurado, rode a skill de novo.

## Estrutura

```
configurar-agentes/
├── SKILL.md
└── assets/
    ├── agents/
    │   ├── executor.md
    │   ├── executor-rapido.md
    │   └── planejador.md
    └── claude-md-delegacao.md
```
