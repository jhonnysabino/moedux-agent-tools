---
name: moedux-cli
description: >-
  Instruções para um agente de IA usar o moedux-cli, o cliente oficial da API
  /api/v1 da Moedux: instalar, autenticar com API Key, ler diagnóstico
  financeiro (Gasto Diário, Mar Vermelho), transações e patrimônio, e criar,
  editar ou excluir transações e aplicar cenários. Use when o usuário pedir
  para consultar a conta Moedux, analisar as finanças, listar ou alterar
  transações, comparar cenários, ou quando o comando `moedux` estiver
  disponível no ambiente.
---

# moedux-cli

CLI Go com entrada/saída JSON para a conta Moedux. O cliente não tem regra de
negócio: o servidor decide, o CLI transpõe o contrato `/api/v1`.

## Contrato da API

A especificação OpenAPI 3.1 legível por máquina está em
`https://www.moedux.com/openapi.json`. Consulte-a para confirmar caminhos,
corpos de requisição, formatos de resposta e a permissão exata (`x-permission`)
de cada operação antes de chamar a API — não adivinhe o formato de um campo.
A CLI cobre todas as operações publicadas; o contrato é a fonte canônica quando
os dois diferirem.

## Instalar

```sh
curl -fsSL https://www.moedux.com/static/cli-install.sh | sh
```

Instala em `~/.local/bin` (sobrescreva com `MOEDUX_CLI_INSTALL_DIR`).

## Autenticação (obrigatória)

1. Crie a API Key na UI: Configurações > API Keys. Escolha permissões mínimas.
2. Exporte uma vez por sessão: `export MOEDUX_API_KEY=moedux_live_<segredo>` (ou `--api-key`, que tem prioridade).
3. A base URL é fixa em `https://www.moedux.com`. Não passe `--base-url`; use apenas para apontar staging/local.

A chave nunca é impressa pelo CLI. Permissões válidas: `diagnosis:read`,
`transactions:read`, `transactions:write`, `scenarios:compare`,
`scenarios:apply`, `networth:read`.

## Comandos de leitura (nunca alteram dados)

```sh
moedux diagnosis [--horizon-days N]        # balance, dailyVariableSpend.value/confidence/windowDays, redSeaDate, projection[], userTier
moedux transactions list [--type income|expense|savings|credit_card] [--from D] [--to D] [--limit N]
moedux networth                            # { items[], total }
```

`confidence` usa o vocabulário canônico: `Sem dados | Aprendendo | Confiável`.

## Comandos de escrita (alteram dados)

```sh
moedux transactions add <json | --fields>
moedux transactions edit <id> --mode only|from|all [--fields]
moedux transactions remove <id> [--mode only|from|all] [--date D]
```

- Campos do JSON/`--fields`: `id` (UUID, opcional), `amount`, `type`, `frequency` (`once|daily|monthly`), `description`, `startDate` (YYYY-MM-DD), `dayOfMonth`, `isStartingBalance`.
- `add --fields` gera o `id` como UUID, para idempotência.
- Editar/excluir recorrência exige `--mode` (`only|from|all`): mesma semântica de escopo da UI. `only/from` exigem `--date`.
- Sempre `list` antes de `edit`/`remove` para obter o `id` real.

## Cenários

```sh
moedux scenarios compare <json> [--horizon-days N]   # NÃO grava. Compare sempre primeiro.
moedux scenarios apply <json>                        # GRAVA. Atômico: converte itens em transações reais.
```

## Códigos de saída (o agente deve reagir a eles)

| Código | Significado | Ação do agente |
|--------|-------------|----------------|
| 0 | sucesso | use o JSON do stdout |
| 1 | uso/argumento | corrija flags ou JSON e repita |
| 2 | autenticação/permissão (401/403) | confira chave e permissões; nunca retente cegamente |
| 3 | validação (422) | `stderr` lista os campos; corrija e repita |
| 4 | servidor/rede | retente uma vez; se persistir, pare e reporte |

JSON de dados sai no stdout; erros saem no stderr. Capture o stdout direto,
sem misturar com logs.

## Receita típica (agente)

```sh
export MOEDUX_API_KEY=moedux_live_xxx
moedux diagnosis | jq -r '.redSeaDate // "sem mar vermelho"'
moedux transactions list --type expense --from "$(date +%F)" | jq '.total'
moedux scenarios compare "$(cat cenário.json)" | jq '.projection[0]'
```

1. Leia sempre `diagnosis` primeiro: o Mar Vermelho (`redSeaDate`) é o alerta
   mais urgente.
2. Antes de qualquer escrita, liste e compare.
3. Nunca aplique um cenário sem ter mostrado o `compare` ao usuário.
4. Não invente valores ou IDs: o que não vier da API, pergunte ao usuário.
