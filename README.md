# Moedux — ferramentas para agentes de IA

[Moedux](https://www.moedux.com/?utm_source=github&utm_medium=repo&utm_campaign=agent-tools) é um aplicativo brasileiro de finanças pessoais: transações, contas e estimativas de gasto viram um diagnóstico legível e uma próxima ação concreta.

Este repositório reúne os artefatos que um agente de IA precisa para usar o Moedux sem adivinhar formato de campo nem permissão de API. É um espelho público e versionado do que o próprio app publica em `/developers`.

## Servidor MCP (registro oficial)

O Moedux publica um servidor MCP remoto (Streamable HTTP) no **Registro Oficial de MCP**, sem cadastro nem chave de API:

- Endpoint: `https://www.moedux.com/mcp`
- Registro: `io.github.jhonnysabino/moedux@1.2.0` — https://registry.modelcontextprotocol.io/v0.1/servers/io.github.jhonnysabino%2Fmoedux/versions/1.2.0
- Server Card SEP-1649: https://www.moedux.com/.well-known/mcp.json
- Conector Glama (qualidade das ferramentas): https://glama.ai/mcp/connectors/io.github.jhonnysabino/moedux
- Lista de servidores MCP remotos, categoria Finance: https://github.com/punkpeye/awesome-remote-mcp-servers/pull/5498

Ferramentas disponíveis:

- `prever_mar_vermelho` — a data do Mar Vermelho e o próximo passo (o menor corte de gasto que o afasta), com link pré-preenchido e sem cadastro.
- `projetar_saldo_12_meses` — saldo projetado em 12 meses e a data do Mar Vermelho, com link pré-preenchido e citação da fonte.
- `calcular_rescisao` — quanto recebo de rescisão: total e quebra por verba (saldo, 13º, férias, aviso, multa do FGTS).
- `calcular_seguro_desemprego` — quantas parcelas e o valor de cada parcela do seguro-desemprego.
- `calcular_fgts` — quanto tenho de FGTS: 8% do salário bruto por mês trabalhado.
- `calcular_saque_aniversario_fgts` — valor do saque-aniversário, saldo restante e alíquota efetiva.
- `calcular_decimo_terceiro` — 13º bruto, 1ª e 2ª parcelas já com INSS e Imposto de Renda.
- `calcular_inss` — desconto do INSS, alíquota efetiva e salário após o desconto.

Exemplo de configuração para um cliente MCP:

```json
{
  "mcpServers": {
    "moedux": {
      "type": "streamable-http",
      "url": "https://www.moedux.com/mcp"
    }
  }
}
```

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
- [Como organizar as contas e sair do vermelho](https://www.moedux.com/br/blog/organizar-contas-sair-do-vermelho?utm_source=github&utm_medium=repo&utm_campaign=leia-tambem) — diagnóstico em três passos, sem planilha.
- [Controle financeiro com IA](https://www.moedux.com/br/blog/controle-financeiro-com-ia?utm_source=github&utm_medium=repo&utm_campaign=leia-tambem) — como antecipar o Mar Vermelho com dados reais.
- [Quanto guardar por mês](https://www.moedux.com/br/blog/quanto-guardar-por-mes?utm_source=github&utm_medium=repo&utm_campaign=leia-tambem) — a conta do fluxo antes da meta e o papel do Gasto Diário.
- [Como calcular gastos mensais](https://www.moedux.com/br/blog/como-calcular-gastos-mensais?utm_source=github&utm_medium=repo&utm_campaign=leia-tambem) — separar Contas Fixas do gasto variável e chegar à variação mensal.
- [Orçamento mensal na prática (guia completo no gist)](https://gist.github.com/jhonnysabino/8e2a46d8947166f362cfaab7f3f7cee7#file-guia-orcamento-mensal-md) — passo a passo auto-contido, sem cadastro.

## Atualização

Os arquivos deste repositório são gerados a partir do código do app. Para atualizar, copie `cmd/moedux-cli/SKILL.md` e o `GET /openapi.json` da versão publicada.

---

Moedux · [Termos de uso](https://www.moedux.com/terms-of-use?utm_source=github&utm_medium=repo&utm_campaign=agent-tools) · [Privacidade](https://www.moedux.com/privacy-policy?utm_source=github&utm_medium=repo&utm_campaign=agent-tools)
