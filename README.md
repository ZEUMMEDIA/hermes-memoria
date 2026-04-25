# Hermes — Memoria Expandida

Este repositorio armazena contexto de longa duracao para o Hermes Agent, complementando a memoria permanente de 2.2k chars com arquivos versionados e sem limite de tamanho.

## Estrutura

```
ultima-atividade.md     — Resumo da ultima tarefa + proximo passo
kickoff-projetos/       — Contexto inicial de projetos grandes
  mission-control.md
automacoes/             — Calendario de crons, squads, status
  crons-status.md
```

## Fluxo

1. Hermes atualiza ultima-atividade.md ao final de cada tarefa complexa
2. Ao iniciar nova conversa, Hermes busca session_search + le ultima-atividade.md do repo
3. Alteracoes sao commitadas e pushadas automaticamente

## Convencoes

- Commits no formato `chore(memoria): descricao`
- Manter arquivos em portugues (pt-BR)
- Nao incluir tokens, senhas ou dados sensiveis
