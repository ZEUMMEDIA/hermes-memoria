# Ultima Atividade — 25/04/2026 18:02

## Seletor visual de modelo de IA no Mission Control

### O que foi feito

Transformei o campo de modelo de IA (antes um input de texto livre) num **seletor visual dropdown** na pagina de edicao de agentes do Mission Control.

### Como funciona

1. Abre `https://missioncontrol.zeumedia.com.br/agents`
2. Clica no squad que quer alterar
3. Clica em "Editar"
4. No campo "Modelo", seleciona o provider no dropdown (DeepSeek, OpenAI, Anthropic, OpenRouter)
5. Digita a versao do modelo no campo custom (ex: `deepseek-chat`, `claude-sonnet-4-20250514`, `gpt-4o`)
6. Salva — pronto, o llm_router.py ja usa o novo modelo no proximo ciclo

### Arquivos alterados

- **AgentForm.jsx** — input de texto substituido por dropdown (`model_provider`) + input custom (`model_name`)
- **AgentDetail.jsx** — mostra provider como badge colorido + nome do modelo separadamente
- **Agents.jsx** — tabela mostra provider em badge roxo + nome do modelo

### Schema do banco

A tabela `agents` no PostgREST tem `model_provider` (varchar) e `model_name` (varchar) separados. O router (`llm_router.py`) ja consulta esses campos em runtime.

### Providers disponiveis no seletor

| Provider | Opcao no dropdown | Exemplo de modelo |
|----------|------------------|-------------------|
| deepseek | DeepSeek | deepseek-chat |
| openai | OpenAI | gpt-4o |
| anthropic | Anthropic | claude-sonnet-4-20250514 |
| openrouter | OpenRouter | openai/gpt-4o |

Para usar um provider novo: adiciona a API key no `/root/hermes-heartbeat/.env` e seleciona no dropdown.

### Board

Task #180 criada e movida para `done`.
