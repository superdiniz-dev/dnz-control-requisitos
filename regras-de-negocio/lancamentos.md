# Lançamentos

<!-- RASCUNHO — brainstorm em andamento com o desenvolvedor (2026-09-11 a 2026-09-18). -->

Este arquivo trata de **origem, edição e correção** do lançamento. O **conteúdo** dele — recebedor,
transação, devedor, pagador — está em [`dados-do-lancamento.md`](dados-do-lancamento.md).

## Origem

Um lançamento tem uma de três origens:

- **Comprovante** — a **principal entrada do sistema**. Nasce de um comprovante recebido (foto,
  upload) e analisado/mapeado automaticamente. Um comprovante com vários pagamentos, declarado
  como lote, gera vários lançamentos de origem comprovante, agrupados num `LoteLancamentos`
  (2026-09-18) — ver [`dados-do-lancamento.md`](dados-do-lancamento.md), "Comprovante em lote".
- **Manual** — criado direto por formulário.
- **Estorno/ajuste** — origem própria (não é um lançamento manual com referência; é um terceiro
  tipo de origem). Confirmado em 2026-09-11. Corrige um lançamento que não pode mais ser editado
  — de comprovante, ou manual fora da janela de edição — sem editar o original. Ver "Correção de
  lançamento não editável" abaixo.

## Regra

- **Lançamento oriundo de comprovante não é editável após a criação.** Confirmado em 2026-09-11.
- **Lançamento manual pode ser editado até 1 dia após a criação.** Depois disso, também deixa de
  ser editável. Confirmado em 2026-09-11. **O prazo é de 24 horas corridas a partir do momento da
  criação** (criado às 23h50 de segunda, editável até 23h50 de terça). Confirmado em 2026-09-18 —
  mesmo prazo para todos, sem depender de fuso horário; descartados "até a meia-noite do dia da
  criação" (pune quem lança à noite) e "até o fim do dia seguinte" (prazos desiguais). **Passado
  o prazo, a correção é por estorno/ajuste**, como no lançamento de comprovante. Confirmado em
  2026-09-17.
- **Lançamento de estorno/ajuste não é editável após a criação**, como o de comprovante.
  Confirmado em 2026-09-18 (a trava existia desde 2026-09-11; a granularidade foi fechada agora).
  Estorno errado se corrige com outro estorno sobre ele, em cadeia. Motivo: o estorno já é uma
  correção com motivo registrado — editá-lo seria corrigir a correção sem rastro. Descartado:
  janela de 1 dia como o manual. Com isso, **só o lançamento manual tem janela de edição**.

## Fluxo do lançamento de comprovante

<!-- Correção 2026-09-11: a primeira versão deste fluxo dizia que o sistema confirmava e salvava
sozinho quando não detectava problema. Isso estava errado — o desenvolvedor esclareceu que a
aprovação do usuário é obrigatória em todos os casos que chegam a ter algo pra aprovar. Corrigido
abaixo; não repetir a versão anterior. -->

1. O comprovante é recebido (foto/upload), com a **forma de pagamento escolhida pelo usuário**
   (PIX, boleto, dinheiro ou "não sei") e, se for o caso, a **declaração de que é lote**.
   Acrescentado em 2026-09-18 — ver [`dados-do-lancamento.md`](dados-do-lancamento.md), "Forma
   de pagamento escolhida antes da análise" e "Comprovante em lote".
2. O sistema analisa e mapeia (parseia) para um lançamento — ou, no lote, um por pagamento.
   **Se o arquivo ou o ID da transação lido já estiver num lançamento vigente da `Empresa`, recusa
   ali mesmo** (no lote, marca só o item como "já registrado"). Acrescentado em 2026-09-18 — ver
   [`dados-do-lancamento.md`](dados-do-lancamento.md), "ID da transação e duplicidade".
3. **Se ler menos de 75% dos campos obrigatórios, mostra mensagem de erro** — não chega a gerar
   lançamento para aprovação. Quais campos são obrigatórios depende da forma de pagamento; ver
   [`dados-do-lancamento.md`](dados-do-lancamento.md). Atualizado em 2026-09-17: este limiar
   substitui o "~50% de aproveitamento" da primeira versão, que media o comprovante inteiro em vez
   dos campos obrigatórios.
4. **Se ler 75% ou mais dos campos obrigatórios, mostra ao usuário o que conseguiu parsear para
   aprovação.**
5. **A aprovação do usuário é obrigatória em todos os casos que chegam ao passo 4** — não existe
   confirmação automática sem o usuário ver e aprovar. A tela de aprovação tem **botão de editar**
   para o usuário corrigir qualquer valor que tenha ficado errado antes de confirmar, e é onde ele
   **completa os campos obrigatórios que o comprovante não trouxe**.
6. **A confirmação só é liberada com todos os obrigatórios preenchidos** — o sistema não salva
   lançamento incompleto nem guarda rascunho pendente de revisão. **Na confirmação, a duplicidade
   é checada de novo**, com o ID como está naquele momento (completado ou corrigido à mão). No lote,
   a confirmação é conjunta e salva tudo ou nada.
7. Só depois da aprovação (com ou sem edição) o lançamento é de fato salvo — e a partir daí passa
   a valer a regra de imutabilidade acima.

## Correção de lançamento não editável

### Lançamento de comprovante

Cenário: o lançamento gerado está errado **mesmo depois de aprovado** (passo 5 acima). Duas causas
possíveis:

- **Bug do sistema** — o comprovante em si está correto, mas o sistema não o leu corretamente
  (e o usuário não percebeu/corrigiu no botão de editar do passo 5).
- **Usuário aprovou sem revisar direito** — a aprovação é obrigatória, mas nada garante que o
  usuário revisou com atenção; se aprovar um valor errado, o dado errado fica salvo.

Em qualquer um dos dois casos, **como o lançamento de comprovante não é editável depois de
salvo**, a correção não é edição: **precisamos permitir cadastrar um lançamento de estorno/ajuste
vinculado ao lançamento original**, para corrigir o erro sem alterar o registro original — ver
"Como o estorno/ajuste corrige" abaixo.

### Lançamento manual fora da janela de edição

Dentro das 24 horas após a criação, um lançamento manual errado se corrige editando. **Passado esse
prazo, a correção segue o mesmo caminho do lançamento de comprovante:** um lançamento de
estorno/ajuste vinculado ao original, que continua intacto. Confirmado em 2026-09-17.

### Como o estorno/ajuste corrige

**Anula e substitui.** Confirmado em 2026-09-18.

- O estorno/ajuste nasce **pré-preenchido com todos os dados do original** (os mesmos blocos de
  [`dados-do-lancamento.md`](dados-do-lancamento.md)); o usuário corrige o que estava errado —
  valor, categoria, tipo, partes, qualquer campo.
- **Motivo é obrigatório.**
- O original **continua intacto**, mas fica marcado como **substituído** e **sai do relatório**.
  Quem entra no relatório é o estorno/ajuste.
- O histórico fica completo: original, estorno/ajuste, motivo, quem fez e quando — coerente com a
  regra inegociável 4 do hub (não se apaga nem se reescreve).
- Não conflita com a duplicidade: o estorno/ajuste já está fora da checagem de ID da transação.

Descartados: **ajuste pela diferença** (só corrige valor, não categoria, tipo ou partes) e **só
anular e relançar como manual** (o relançamento teria o mesmo ID do original, ainda vigente, e seria
recusado pela duplicidade).

**Estorno de estorno: cadeia.** Confirmado em 2026-09-18. Um estorno/ajuste errado se corrige com
outro estorno/ajuste **sobre ele**, que o substitui. **Só o último da cadeia é estornável** — o
original e os estornos já substituídos não. Assim, a cada momento existe **um único lançamento
vigente** representando aquele pagamento (o último da cadeia), e o histórico se lê em ordem:
original → estorno 1 → estorno 2, cada um com seu motivo. Descartado: estornar sempre sobre o
original, que acumularia vários estornos e exigiria regra para saber qual vale.

O estorno/ajuste **não é editável** após a criação (ver "Regra" acima) e fica **fora da checagem
de duplicidade** por ID da transação (ver [`dados-do-lancamento.md`](dados-do-lancamento.md)).

**Exclusão é sempre da cadeia inteira.** Confirmado em 2026-09-18. Excluir qualquer elo — o
original ou um estorno — exclui (logicamente) a cadeia toda, após confirmação explícita ("isto
exclui o lançamento e seus 2 estornos"). A exclusão é do **pagamento**, não de uma versão dele.
Motivo: excluir só o original liberaria o ID da transação enquanto o estorno (fora da checagem de
ID) seguiria vigente — reenviar o comprovante contaria o pagamento duas vezes. Descartado: excluir
o último elo "desfazendo" o estorno — desnecessário, porque estorno errado já se corrige com outro
estorno, com motivo registrado.

**Estorno de item de lote pertence ao lote.** Confirmado em 2026-09-18. A cadeia inteira de um
item (original e estornos) pertence ao mesmo `LoteLancamentos`, e excluir o lote exclui
(logicamente) as cadeias inteiras. Descartado: estorno avulso fora do lote — excluído o lote, o
estorno continuaria vigente, o arquivo ficaria liberado, e reenviar o PDF traria o item de volta:
o mesmo pagamento contado duas vezes. Ver [`dados-do-lancamento.md`](dados-do-lancamento.md),
"Comprovante em lote".

## Comprovante (o arquivo)

**O arquivo do comprovante é imutável depois de salvo.** Uma vez criado, não é possível trocar o
arquivo. No máximo, exclusão — que, como toda exclusão no sistema, é lógica, nunca física (ver
[`exclusao-logica.md`](exclusao-logica.md)). Confirmado em 2026-09-11.

**O comprovante não se exclui sozinho.** Confirmado em 2026-09-18. Ele só sai junto com o
lançamento — ou com a cadeia de estornos, ou com o `LoteLancamentos` —, porque a exclusão é do
pagamento, e o comprovante faz parte dele. Não existe "excluir comprovante", só "excluir
lançamento", que leva o arquivo. Descartados: excluir o comprovante excluir o lançamento (mesmo
efeito, com dois caminhos para a mesma coisa) e o lançamento ficar sem o arquivo (lançamento de
comprovante sem prova, impossível de auditar, e fora da proteção de arquivo idêntico).

## Requisito levantado — a resolver depois

**Receber comprovantes variados e mapear para objetos/entidades operáveis pela API, a fim de
gerar relatórios.** Ainda sem mecanismo definido — relaciona-se com a decisão pendente #2 do hub
(OCR próprio vs. Azure Document Intelligence / Google Document AI / Textract / outro). Este
requisito é o "como" desse mapeamento; a decisão #2 é o "com quê".

## Em aberto — a aprofundar depois

- Limiar de 75% dos campos obrigatórios lidos: número provisório, a revisar quando o parser for
  escolhido (decisão pendente #2 do hub). Ver também as pontas soltas em
  [`dados-do-lancamento.md`](dados-do-lancamento.md).

## Impacto (a confirmar quando o modelo fechar)

- `dnzcontrol-api` — lançamento precisa de campo de origem (`comprovante` | `manual` | `estorno`)
  e regra de imutabilidade condicional a essa origem (só o manual edita, por 24 horas corridas).
- `dnzcontrol-api` — cadeia de estorno/ajuste: referência ao lançamento substituído, motivo
  obrigatório, marcação de substituído (sai do relatório), só o último da cadeia estornável,
  exclusão lógica da cadeia inteira; cadeia de item de lote pertence ao `LoteLancamentos`.
- `dnzcontrol-api` — comprovante sem exclusão própria: sai só com o lançamento, a cadeia ou o lote.
- `superdiniz-hub/regras/seguranca.md` / `regras/backend.md` — autorização de edição de lançamento
  passa a depender da origem, não só do papel de quem pede.
- `superdiniz-hub/contexto/decisoes-pendentes.md` #2 — este arquivo é o requisito de negócio que
  sustenta aquela decisão técnica; seguem ligados, mas #2 continua sem mecanismo escolhido.
