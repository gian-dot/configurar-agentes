# configurar-agentes

Skill para o Claude Code que instala num projeto uma arquitetura de agentes em quatro camadas:

| Camada | Modelo | Papel |
|---|---|---|
| Plan master e revisor | Fable 5.2 (sessão principal) | Escreve o plano mestre, despacha e revisa tudo |
| Planejador minucioso | Opus (`planejador`) | Desdobra o plano mestre em passos executáveis |
| Executor | Sonnet (`executor`) | Executa os passos complexos |
| Executor rápido | Haiku (`executor-rapido`) | Executa os passos simples e faz buscas |

## O que a skill faz num projeto

1. Lê o projeto para entender o que ele produz e suas convenções.
2. Cria os três agentes em `.claude/agents/`, com descrição e contexto adaptados ao projeto.
3. Define `"model": "claude-fable-5-2"` em `.claude/settings.json`.
4. Insere a seção "Delegação de agentes" no `CLAUDE.md`, entre marcadores, para poder ser atualizada depois.

Ela não sobrescreve agentes existentes sem perguntar e não faz commit.

## Instalação

Clone este repositório dentro da pasta de skills do usuário:

```bash
git clone <url-deste-repositorio> ~/.claude/skills/configurar-agentes
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
    │   ├── planejador.md
    │   ├── executor.md
    │   └── executor-rapido.md
    └── claude-md-delegacao.md
```
