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

## Registro de geração

**Toda geração de relatório deixa um registro: quem gerou, quando, com quais empresas, período e
filtros, sem os valores.** Confirmado em 2026-09-18. Serve para responder "quem viu o quê, e
quando". Se um período muda depois de visto (por estorno/ajuste ou exclusão lógica), não há um
"relatório antigo" guardado. Os valores daquele momento podem ser reconstruídos, se preciso, pelo
histórico dos lançamentos, que nunca se apaga e tem data em cada estorno e exclusão.

**Só a Diretoria consulta os registros, e vê os da rede inteira**, inclusive os relatórios gerados
pelos Gerentes. O Gerente não vê registro nenhum, nem os próprios. O Administrador vê tudo, como em
qualquer dado da rede (ver [`papeis-e-hierarquia.md`](papeis-e-hierarquia.md)). Confirmado em
2026-09-18. Descartados: Gerente ver os registros das empresas atribuídas (passaria a ver o que a
Diretoria consultou) e Gerente ver só os próprios (histórico pessoal, que não é o propósito do
registro).

**A exportação fica marcada no próprio registro da geração**, não num registro à parte: o registro
diz se o relatório foi exportado e, se foi, em qual formato (PDF, XLS ou SVG). Confirmado em
2026-09-18. Assim a Diretoria sabe quem tirou os números do sistema, e em que arquivo. Se o mesmo
relatório gerado for exportado mais de uma vez, **o registro acumula todos os formatos** (ex.:
"PDF, XLS") — nenhuma saída se perde.

Descartados: **nenhum rastro** (o sistema não saberia dizer quem consultou os números) e
**registro com os totais** (vira um quase-fechamento, descartado em 2026-09-11, e guarda valor
financeiro fora do lançamento).

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

### Transferências entre empresas da rede no consolidado

**A transferência só é descontada dos totais quando as duas pontas estão na seleção.** Confirmado
em 2026-09-18. Um PIX interno da empresa A para a B aparece como gasto em A e receita em B, sem o
dinheiro ter saído da rede. No consolidado:

- Um lançamento da categoria "Transferência entre empresas da rede" é tratado como **transferência
  interna** quando a outra ponta — o recebedor, se é gasto; o pagador, se é receita — tem o
  CNPJ de uma `Empresa` **que está na seleção** do relatório.
- A transferência interna **sai dos totais de gastos e de receitas** e aparece numa **linha à
  parte** ("Transferências internas: R$ …"), para ficar visível que o valor existiu.
- Se a outra ponta **não** está na seleção, o lançamento conta como gasto ou receita normal: para
  o conjunto escolhido, o dinheiro de fato entrou ou saiu. Exemplo: consolidar A e C com um PIX
  A→B mantém o gasto de A.
- No relatório de uma empresa só, nada muda: a transferência é gasto ou receita como qualquer
  outro lançamento, na sua categoria.

Descartados: **não descontar** (o consolidado da rede fica com gasto e receita inflados pelo
dinheiro que só circulou dentro dela) e **descontar sempre a categoria inteira** (apaga gasto ou
receita real quando a outra ponta está fora da seleção).

**A outra ponta é reconhecida pelo CNPJ.** O CPF/CNPJ do recebedor e do pagador é obrigatório no
lançamento (ver [`dados-do-lancamento.md`](dados-do-lancamento.md), "Obrigatoriedade"), e o CNPJ
da `Empresa` é obrigatório e único na rede (ver
[`estrutura-empresarial.md`](estrutura-empresarial.md)).

## Compartilhamento

**Não existe compartilhamento vivo dentro do sistema** (ex.: link com token que dá acesso sem
login). O compartilhamento acontece **por exportação**: o relatório pode ser exportado em **PDF**,
**XLS** e **SVG**; uma vez exportado, o compartilhamento é do arquivo, fora do sistema. Confirmado
em 2026-09-11 — responde a decisão pendente #11 do hub.

Isso significa que a seção de "tokens de compartilhamento" cogitada em `regras/seguranca.md` não
se aplica — não há necessidade de token de acesso público a relatório.

## Em aberto — a aprofundar depois

Nada em aberto por ora.

## Impacto (a confirmar quando o modelo fechar)

- `superdiniz-hub/regras/seguranca.md` — regra inegociável 4 já documenta que relatório é visão
  dinâmica, sem fechamento (migrado).
- `superdiniz-hub/contexto/decisoes-pendentes.md` #4 — respondida: não há fechamento de
  período/relatório, a princípio.
- `superdiniz-hub/contexto/decisoes-pendentes.md` #11 — respondida: compartilhamento é só por
  exportação de arquivo (PDF/XLS/SVG), não por link/token dentro do sistema.
- `dnzcontrol-api` — relatório não precisa de tabela própria de "snapshot fechado"; é uma consulta
  sobre lançamentos vigentes no período pedido. Precisa de geração de export em PDF, XLS e SVG.
  Cada geração grava um registro (quem, quando, empresas, período, filtros), sem valores, com um
  indicador booleano de exportação e os formatos exportados (acumulados) quando houver; consulta
  restrita à Diretoria (e ao Administrador).
  No consolidado, reconhece a transferência interna cruzando o CPF/CNPJ da outra ponta com o CNPJ
  das `Empresa` selecionadas.
