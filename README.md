# Moedux — ferramentas para agentes de IA

[Moedux](https://www.moedux.com/?utm_source=github&utm_medium=repo&utm_campaign=agent-tools) é um aplicativo brasileiro de finanças pessoais: transações, contas e estimativas de gasto viram um diagnóstico legível e uma próxima ação concreta.

Este repositório reúne os artefatos que um agente de IA precisa para usar o Moedux sem adivinhar formato de campo nem permissão de API. É um espelho público e versionado do que o próprio app publica em `/developers`.

## Contrato da API

- Especificação OpenAPI 3.1: [`openapi.json`](openapi.json) — espelho de https://www.moedux.com/openapi.json
- Documentação para humanos: https://www.moedux.com/developers?utm_source=github&utm_medium=repo&utm_campaign=agent-tools
- Autenticação: HTTP Bearer com API Key criada em Configurações > API Keys no app.

Cada operação carrega `x-permission`. Peça sempre a permissão mínima: `diagnosis:read`, `transactions:read`, `transactions:write`, `scenarios:compare`, `scenarios:apply` ou `networth:read`.

## Skill do CLI

[`SKILL.md`](SKILL.md) ensina um agente a instalar e usar o `moedux-cli`, o cliente oficial da API `/api/v1` com entrada e saída JSON. Instalação:

```sh
curl -fsSL https://www.moedux.com/static/cli-install.sh | sh
```

O binário aceita `diagnosis`, `transactions list|add|edit|remove`, `networth` e `scenarios compare|apply`. A especificação em `openapi.json` é a fonte canônica quando houver divergência.

## Começando sem cartão

O cadastro cria 14 dias de teste gratuito: https://www.moedux.com/app/login?utm_source=github&utm_medium=repo&utm_campaign=agent-tools

## Guias de finanças pessoais

Conteúdo gratuito e independente, mantido no gist canônico do projeto:

- [Guia prático de controle financeiro com modelos CSV](https://gist.github.com/jhonnysabino/8e2a46d8947166f362cfaab7f3f7cee7#file-guia-controle-financeiro-md)
- [Reserva de emergência: como montar e quanto guardar](https://gist.github.com/jhonnysabino/8e2a46d8947166f362cfaab7f3f7cee7#file-guia-reserva-emergencia-md)
- [Comparativo de aplicativos de finanças pessoais](https://gist.github.com/jhonnysabino/8e2a46d8947166f362cfaab7f3f7cee7#file-guia-comparativo-apps-md)

## Atualização

Os arquivos deste repositório são gerados a partir do código do app. Para atualizar, copie `cmd/moedux-cli/SKILL.md` e o `GET /openapi.json` da versão publicada.

---

Moedux · [Termos de uso](https://www.moedux.com/terms-of-use?utm_source=github&utm_medium=repo&utm_campaign=agent-tools) · [Privacidade](https://www.moedux.com/privacy-policy?utm_source=github&utm_medium=repo&utm_campaign=agent-tools)
