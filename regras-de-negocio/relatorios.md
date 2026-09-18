# Relatórios

<!-- RASCUNHO — brainstorm em andamento com o desenvolvedor (2026-09-11 a 2026-09-18). -->

## Modelo

Relatório é uma **visão de um período** — calculado a partir dos lançamentos vigentes daquele
período no momento em que é gerado. **A princípio, não existe uma ação de "fechar período".**
Não há snapshot armazenado à parte: se os lançamentos do período mudarem depois, o relatório
gerado de novo para o mesmo período reflete a mudança.

Isso responde a regra inegociável 4 do hub (`superdiniz-hub/regras/seguranca.md`), cuja primeira
versão supunha um fechamento imutável. Confirmado em 2026-09-11: **não existe esse fechamento**,
pelo menos a princípio.

**Relatório "sem fechamento" não significa lançamento editável para sempre.** A trava de edição é
do lançamento, não do período: lançamento de comprovante nunca é editável, lançamento manual só
por 24 horas corridas após a criação, e depois disso a correção é por estorno/ajuste — ver
[`lancamentos.md`](lancamentos.md). O que muda o relatório de um período antigo é, portanto, um
estorno/ajuste ou uma exclusão lógica, não a edição do lançamento original.

**Lançamento substituído por estorno/ajuste sai do relatório**; entra o estorno/ajuste, com os
dados corrigidos. Confirmado em 2026-09-18 — ver [`lancamentos.md`](lancamentos.md), "Como o
estorno/ajuste corrige".

## Qual data define o período

O lançamento tem duas datas de negócio: **data/hora da transação** e **data de vencimento** (ver
[`dados-do-lancamento.md`](dados-do-lancamento.md)). O relatório trabalha com as duas: **a data da
transação é o padrão**, e o filtro permite gerar o mesmo relatório pela data de vencimento.
Confirmado em 2026-09-17.

Na prática, é o relatório em regime de caixa por padrão, com a visão por competência a um filtro de
distância.

## Agrupamento por categoria

Agrupar por categoria é **opcional, escolhido a cada relatório** — quem gera decide se quer o
relatório só com o total de gastos e receitas do período, ou quebrado por categoria. Confirmado
em 2026-09-17. O catálogo de categorias está em
[`dados-do-lancamento.md`](dados-do-lancamento.md).

**O agrupamento acontece dentro de cada tipo.** Confirmado em 2026-09-18: gastos por categoria e
receitas por categoria. Uma categoria marcada como "ambos" (ex.: "Aluguel") aparece nas duas
partes, com totais separados, nunca somados entre si.

## Relatório de várias empresas (consolidado)

**O relatório pode reunir qualquer conjunto de empresas que quem gera enxerga.** Confirmado em
2026-09-18. Quem gera escolhe uma, várias ou todas as empresas do seu escopo: a Diretoria pode
consolidar a rede inteira; um Gerente atribuído a três empresas pode consolidar essas três. Não é
regra nova de acesso — o conjunto disponível é o escopo de leitura de sempre (regra inegociável 1
do hub). Descartados: só por `Empresa` no MVP (empurra a soma para planilha) e consolidado só da
rede inteira, só para a Diretoria (deixa de fora o Gerente com várias empresas).

## Compartilhamento

**Não existe compartilhamento vivo dentro do sistema** (ex.: link com token que dá acesso sem
login). O compartilhamento acontece **por exportação**: o relatório pode ser exportado em **PDF**,
**XLS** e **SVG**; uma vez exportado, o compartilhamento é do arquivo, fora do sistema. Confirmado
em 2026-09-11 — responde a decisão pendente #11 do hub.

Isso significa que a seção de "tokens de compartilhamento" cogitada em `regras/seguranca.md` não
se aplica — não há necessidade de token de acesso público a relatório.

## Em aberto — a aprofundar depois

- Se um período já "visto" em relatório muda depois (por estorno/ajuste ou exclusão lógica), o
  relatório antigo simplesmente deixa de existir como registro (só existe o relatório gerado agora,
  com o dado atual), ou fica algum rastro de que o relatório foi visto com outros valores em outro
  momento?
- **Próxima dúvida do brainstorm (parada em 2026-09-18, antes da resposta).** No consolidado, o
  que fazer com as transferências entre empresas? Opções postas: **(A)** não descontar — tudo
  entra nos totais, o usuário filtra a categoria se quiser; **(B)** descontar sempre tudo da
  categoria "Transferência entre empresas da rede" — erra quando a outra ponta não está na seleção
  (consolidar A e C com transferência A→B apagaria um gasto real do conjunto); **(C)** descontar só
  quando as duas pontas estão na seleção, reconhecendo a outra ponta pelo CPF/CNPJ do
  recebedor/pagador ser o de uma empresa selecionada, e mostrar esses valores numa linha à parte
  ("Transferências internas: R$ …"). Recomendação da IA: **C**. Consequência de C: depende do
  CNPJ de cada `Empresa` estar cadastrado — se não for obrigatório, vira dúvida de
  [`estrutura-empresarial.md`](estrutura-empresarial.md).
  Pergunta original: o consolidado desconta automaticamente os lançamentos da categoria
  "Transferência entre empresas da rede" (o PIX interno
  aparece como gasto numa empresa e receita na outra sem o dinheiro ter saído da rede)? Levantado
  em 2026-09-18 — ver [`dados-do-lancamento.md`](dados-do-lancamento.md), "Lista fixa do sistema".

## Impacto (a confirmar quando o modelo fechar)

- `superdiniz-hub/regras/seguranca.md` — regra inegociável 4 já documenta que relatório é visão
  dinâmica, sem fechamento (migrado).
- `superdiniz-hub/contexto/decisoes-pendentes.md` #4 — respondida: não há fechamento de
  período/relatório, a princípio.
- `superdiniz-hub/contexto/decisoes-pendentes.md` #11 — respondida: compartilhamento é só por
  exportação de arquivo (PDF/XLS/SVG), não por link/token dentro do sistema.
- `dnzcontrol-api` — relatório não precisa de tabela própria de "snapshot fechado"; é uma consulta
  sobre lançamentos vigentes no período pedido. Precisa de geração de export em PDF, XLS e SVG.
