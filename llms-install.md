# Install Moedux in Cline (remote MCP server)

Moedux is a **remote** MCP server. There is nothing to clone, install or build, and
the preview tools need **no API key and no sign-up**.

- Endpoint: `https://www.moedux.com/mcp`
- Transport: Streamable HTTP (set `"type": "streamableHttp"` explicitly; Cline
  defaults to the legacy SSE transport when `type` is omitted)
- Auth: none for the tools listed below

## 1. Add the server

Open **MCP Servers → Configure MCP Servers** and merge this entry into the
`mcpServers` object (do not replace the other servers):

```json
{
  "mcpServers": {
    "moedux": {
      "type": "streamableHttp",
      "url": "https://www.moedux.com/mcp",
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

Then click **Done** / reconnect. The server reports `serverInfo.name = "moedux"`
and exposes 19 tools; the version it reports in the `initialize` handshake is the
same one published in the registry listing (`server.json`), so a stale copy is
visible immediately.

## 2. Verify the install

Ask Cline, in Portuguese or English:

> Qual é a data do Mar Vermelho com saldo inicial de R$ 3.000, entrada mensal de
> R$ 2.500 e conta fixa de R$ 3.500?

Cline should call `prever_mar_vermelho` with
`{"saldo": 3000, "entrada": 2500, "saida": 3500}` and receive an answer like:

> Mar Vermelho previsto: 10/02/2027. Próximo passo nesta amostra: reduzir a Saída
> mensal em R$ 800 evita o Mar Vermelho nos próximos 12 meses. …
> https://www.moedux.com/previa-mar-vermelho?…

The link in the answer is pre-filled and carries `utm_source=mcp`, so it opens the
same projection on the website without the user typing the numbers again.

## 3. Tools

Every tool takes plain numbers in Brazilian reais and answers in Portuguese.

| Tool | Answers |
| --- | --- |
| `prever_mar_vermelho` | The date the balance hits zero and the smallest monthly cut that avoids it in 12 months |
| `projetar_saldo_12_meses` | Month-by-month balance curve, the zero date and the smallest cut |
| `calcular_rescisao` | Severance total and per-line breakdown (salary balance, 13th, vacation, notice, FGTS fine) |
| `calcular_seguro_desemprego` | Number and value of unemployment-insurance installments |
| `calcular_salario_liquido` | Net salary after INSS and 2026 income tax |
| `calcular_ferias` | Vacation pay with the 1/3 bonus, INSS/IRRF and the 10-day cash-out |
| `calcular_fgts` | FGTS balance from gross salary and months worked |
| `calcular_saque_aniversario_fgts` | FGTS birthday-withdrawal value, remaining balance and effective rate |
| `calcular_decimo_terceiro` | 13th salary gross, 1st and 2nd installments after INSS and IR |
| `calcular_inss` | INSS discount, effective rate and salary after the discount |
| `calcular_pis_pasep` | PIS/PASEP allowance and eligibility |
| `calcular_horas_extras` | Hour value, 50%/100% overtime and the DSR |
| `calcular_reserva_emergencia` | Emergency-fund target and time to reach it |
| `calcular_quanto_guardar_por_mes` | Monthly contribution needed to hit a goal |
| `simular_emprestimo_pessoal` | Price-table installment, total paid, interest and CET |
| `calcular_quanto_posso_gastar_por_dia` | Daily spending ceiling from income, fixed bills and savings goal |
| `calcular_financiamento` | Price-table installment, total, interest and minimum income |
| `calcular_irpf` | Payroll income tax (2026 table, Lei 15.270/2025 reduction) and net salary |
| `simular_consorcio` | Consortium installment, administration fee and comparison with financing |
| `dividir_contas_casal` | Proportional split of the couple's shared bills, the 50/50 alternative and each method's share of each income |
| `calcular_quanto_rende_por_mes` | Monthly and 12-month yield on savings, CDI, Tesouro Selic, CDB or LCI/LCA, with the November/2026 reference rate or a custom monthly rate, and the IR treatment |
| `calcular_quitar_dividas` | Months until a debt is paid off, total paid, interest and the minimum payment that makes the balance fall |
| `calcular_pagar_minimo_cartao` | The cost of paying only the card minimum for 12 months: total paid, interest, remaining balance and time to halve it |

## 4. Notes for the agent

- All monetary values are BRL. Pass numbers, not formatted strings.
- The tools are read-only previews: they compute from the arguments the user
  provides and do not read the user's account.
- The authenticated product (transactions, scenarios, net worth) is a separate
  REST API with API keys created inside the app; see `openapi.json` and
  <https://www.moedux.com/developers>.
- If a call fails, check that `type` is `streamableHttp` and that the URL ends in
  `/mcp`. A `406` on initialize means the client sent `Accept` without
  `text/event-stream`; Cline does not hit this.
