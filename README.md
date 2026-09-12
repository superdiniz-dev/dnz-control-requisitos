# dnz-control-requisitos

Documentação de **requisitos e produto** do DNZ Control (nome de trabalho), em linguagem de
negócio — visão de produto, regras de negócio e fluxos, sem prender a redação a termos técnicos ou
de implementação. Repositório irmão de `superdiniz-hub` (contexto de engenharia/IA),
`dnzcontrol-api` e `dnzcontrol-app` (código), lado a lado em `C:\dev\git\`.

## Estado

**Tudo aqui é rascunho.** Este repositório nasceu para registrar o brainstorm de produto em
andamento com o desenvolvedor — nada aqui é decisão fechada até o desenvolvedor confirmar. Uma vez
madura, uma decisão migra para `superdiniz-hub/contexto/` (e, se for decisão arquitetural cara de
reverter, vira ADR em `superdiniz-hub/docs/adr/`) — este repositório continua sendo a fonte de
verdade para a visão de produto e regras de negócio em si, mesmo depois de madura.

## Como se organiza

| Pasta | O que registra |
|---|---|
| [`produto/`](produto/) | O que o produto é, para quem, por quê — visão, personas, proposta de valor. |
| [`regras-de-negocio/`](regras-de-negocio/) | Regras que governam o domínio — o que pode, o que não pode, sob que condição. |
| [`fluxos/`](fluxos/) | Como o trabalho acontece do ponto de vista de quem usa — passo a passo, não endpoint a endpoint. |
| [`glossario.md`](glossario.md) | Termos de domínio, com a definição que o negócio usa. |

## Relação com `superdiniz-hub`

Este repositório é a fonte; `superdiniz-hub/contexto/` é o destino técnico depois que uma decisão
amadurece — a tradução para termos de engenharia (entidades, endpoints, regras de autorização)
acontece lá, não aqui. Não duplique conteúdo já fechado no hub; referencie.
