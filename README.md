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

## Onde paramos — retomar aqui na próxima sessão

**Última sessão: 2026-09-18.** Fechamos todo o "Em aberto" de
[`regras-de-negocio/dados-do-lancamento.md`](regras-de-negocio/dados-do-lancamento.md) e boa parte
do de [`regras-de-negocio/lancamentos.md`](regras-de-negocio/lancamentos.md):

- **Duplicidade** — na mesma `Empresa`, lançamento com o mesmo ID da transação ou do mesmo arquivo
  de um lançamento vigente é recusado; checado após a leitura e na confirmação; vale para
  comprovante e manual, não para estorno/ajuste. É o que a regra inegociável 5 do hub precisava.
- **Boleto** — ID da transação é a autenticação bancária; código de barras e valor original
  obrigatórios (mínimo lido: 10 de 13); recebedor é o beneficiário.
- **Forma de pagamento** — escolhida antes da análise (PIX, boleto, dinheiro ou "não sei"); no
  "não sei", testa do conjunto mais exigente para o menos; trocável na aprovação, com aviso de
  divergência.
- **Moeda** — só real, sem campo no MVP.
- **Comprovante em lote** — `LoteLancamentos` (guarda-chuva sem valor, guarda o arquivo) com
  lançamentos filhos; limiar e duplicidade por item; aprovação conjunta tudo ou nada.
- **Estorno/ajuste** — anula e substitui (todos os dados, motivo obrigatório); cadeia; não
  editável; excluir qualquer elo exclui a cadeia inteira.
- **Categoria** — obrigatória; sugerida pelo histórico do recebedor; só a Diretoria cadastra;
  marcada gasto/receita/ambos; lista fixa definida; excluída some só para o que vier depois.
- **Plano futuro** — expurgo físico de inativos, em
  [`regras-de-negocio/retencao-de-dados.md`](regras-de-negocio/retencao-de-dados.md).

Na sessão de 2026-09-17 tinha sido fechado o conteúdo do lançamento: os quatro blocos de dados,
tipo, categoria, qual data manda no relatório e a obrigatoriedade por forma de pagamento.

Em `lancamentos.md`, fechado também: **o comprovante não se exclui sozinho** — só sai junto com o
lançamento (ou cadeia, ou lote) — e o **prazo de edição do manual é de 24 horas corridas** desde a
criação.

Em `exclusao-logica.md`: **reativação existe só para dado de conta** (usuário, empresa,
categoria), e **quem pode excluir, pode reativar**; dado financeiro não se reativa — registra-se de
novo.

O filtro **"ver inativos" não amplia o escopo** de quem vê — `exclusao-logica.md` está sem nada em
aberto.

Em `relatorios.md`: o relatório pode **consolidar qualquer conjunto de empresas** do escopo de quem
gera (Diretoria: a rede inteira; Gerente: as empresas atribuídas).

**A próxima sessão começa por aqui:** no relatório consolidado, o que fazer com as transferências
entre empresas da rede. A pergunta já foi posta, com as opções A/B/C e a recomendação (C:
descontar só quando as duas pontas estão na seleção, em linha à parte), mas **ainda não foi
respondida** — está no "Em aberto" de
[`regras-de-negocio/relatorios.md`](regras-de-negocio/relatorios.md). Depois: o rastro de relatório
já visto (mesmo arquivo), o "Em aberto" de `entidade-base.md` e o de `estrutura-empresarial.md`. O único item restante de `lancamentos.md`
(limiar de 75% provisório) depende da escolha do leitor de comprovante (decisão pendente #2 do hub).

A lista completa do que segue em aberto está na seção "Em aberto" de cada arquivo de regra.

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
