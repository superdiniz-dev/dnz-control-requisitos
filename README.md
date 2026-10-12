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

**Última sessão: 2026-09-18 (virou 19).** Fechamos todo o "Em aberto" de
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
gera (Diretoria: a rede inteira; Gerente: as empresas atribuídas). No consolidado, a
**transferência entre empresas da rede só sai dos totais quando as duas pontas estão na seleção**
— reconhecida pelo CNPJ da outra ponta e mostrada numa linha à parte ("Transferências internas").

Em `estrutura-empresarial.md`: **toda `Empresa` tem CNPJ, obrigatório e único dentro da rede**
(nunca checado entre redes, para não vazar cliente de outra rede), **contando as inativas** — o
CNPJ de empresa excluída se recupera reativando a empresa.

Em `relatorios.md`: **toda geração de relatório deixa um registro** (quem, quando, empresas,
período, filtros), **sem valores**. Não há relatório antigo guardado; os valores de um momento
passado são reconstruídos pelo histórico imutável, se preciso. **Só a Diretoria consulta** esses
registros (da rede inteira); o Gerente não vê nenhum. A **exportação fica marcada no próprio
registro**, acumulando os formatos. `relatorios.md` está sem nada em aberto.

Em `entidade-base.md`: a entidade base ganhou **criado por** e **atualizado por**, com cinco
atributos no total. **Qualquer mudança conta como atualização, inclusive de status**; a
imutabilidade vale para o conteúdo de negócio, não para a entidade base. `entidade-base.md` está
sem nada em aberto.

Em `estrutura-empresarial.md`, também em 2026-09-18:

- **A Diretoria cadastra as `Empresa`**, desde a primeira; na rede de empresa única, o
  Administrador já cria a principal.
- **"Empresa única" é uma marcação do grupo, que só o Administrador altera.** Com ela ligada, a
  Diretoria não cadastra outra empresa; marcar exige exatamente uma empresa ativa; marcado, o app
  esconde tudo o que é de rede. **O caso base é uma rede de uma empresa só.**
- **Parâmetros funcionais** nascem como mecanismo preparado para crescer, com só a empresa única
  no MVP.
- **App próprio na loja é opcional** (no MVP, só o app padrão DNZ Control, com a marca da rede
  depois do login); quando vier, é publicado na conta do DNZ Control, com risco de recusa da Apple
  registrado. **Domínio próprio na web é opcional**, já no MVP.
- **Regime tributário da `Empresa` é opcional**; só a Diretoria altera; mudar faz as cobranças
  federais voltarem ao padrão do novo regime.

`estrutura-empresarial.md` está sem nada em aberto.

Em [`regras-de-negocio/painel-inicial.md`](regras-de-negocio/painel-inicial.md) (novo): a página
inicial é um painel da rede ou das empresas liberadas para quem vê, **filtrável como um BI** (7
dimensões, clique no gráfico vira filtro), **abre no mês atual**, **segue as regras do relatório**,
mostra os **5 últimos lançamentos pela data da transação** com "ver todos", e **não** entra no
registro de geração. Sem nada em aberto.

Em [`regras-de-negocio/notificacoes.md`](regras-de-negocio/notificacoes.md) (novo):

- **Pagamento recorrente é só lembrete**, nunca gera lançamento; cadastro completo (cinco
  periodicidades, só a data de início obrigatória entre os extras); vencimentos calculados da data
  de início; quem lança na empresa cadastra; aviso antecipado (3 dias por padrão, a Diretoria
  configura de 1 a 30) e no dia.
- **Cobranças federais** vêm de um catálogo do Administrador por regime tributário, ligadas sozinhas
  pelo regime da empresa e ajustáveis por quem lança; vencimento em dia não útil antecipa ou
  prorroga conforme a cobrança (calendário de feriados nacionais do Administrador).
- **Recebem** todos que enxergam a empresa, cada um podendo desligar para si, por empresa.
- **Canais:** central, push no celular e e-mail (resumo diário com a marca da rede).
- **Ciclo (2026-09-19):** quem lança na empresa **marca a ocorrência como paga**, valendo para
  todos — só marcação, sem lançamento; os avisos restantes daquela ocorrência param de sair. Vencida sem marcação **fica
  como "vencida" na central**, sem novos avisos. **A marcação se desfaz** por quem lança na
  empresa, a qualquer momento, com histórico de quem marcou e desmarcou. Na própria central, cada
  usuário tem **lida/não lida** (com contador) e **dispensar**, só para si.

**A próxima sessão começa por aqui:** o ciclo da notificação fechou em
2026-09-19. Também fecharam as pontas soltas de categoria em
[`regras-de-negocio/dados-do-lancamento.md`](regras-de-negocio/dados-do-lancamento.md): renomear
categoria em uso vale para todo o histórico, sem mudar a marcação de tipo, e a Diretoria oculta
categorias da lista fixa só para a própria rede. `dados-do-lancamento.md` está sem nada em aberto. Em
[`regras-de-negocio/notificacoes.md`](regras-de-negocio/notificacoes.md) resta só o conteúdo
inicial do catálogo de cobranças federais: há um rascunho com fontes (2026-09-19), **a conferir
com o contador**. Cobrança de folha (depende de ter empregado) fica fora do liga-automático e nasce
desligada. O único item restante de `lancamentos.md` (limiar de 75% provisório) depende da escolha
do leitor de comprovante (decisão pendente #2 do hub).

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
