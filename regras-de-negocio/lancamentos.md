# Lançamentos

<!-- RASCUNHO — brainstorm em andamento com o desenvolvedor (2026-09-11). -->

## Origem

Um lançamento tem uma de três origens:

- **Comprovante** — a **principal entrada do sistema**. Nasce de um comprovante recebido (foto,
  upload) e analisado/mapeado automaticamente.
- **Manual** — criado direto por formulário.
- **Estorno/ajuste** — origem própria (não é um lançamento manual com referência; é um terceiro
  tipo de origem). Confirmado em 2026-09-11. Corrige um lançamento de comprovante errado sem
  editar o original — ver "Correção de lançamento de comprovante errado" abaixo.

## Regra

- **Lançamento oriundo de comprovante não é editável após a criação.** Confirmado em 2026-09-11.
- **Lançamento manual pode ser editado até 1 dia após a criação.** Depois disso, também deixa de
  ser editável. Confirmado em 2026-09-11 — granularidade exata do prazo (dia corrido a partir do
  timestamp de criação, ou até a virada do dia) ainda não detalhada.
- **Lançamento de estorno/ajuste também tem trava** — não é livremente editável. Confirmado em
  2026-09-11; a granularidade exata (imutável desde a criação como o de comprovante, ou com prazo
  como o manual) ainda não foi detalhada.

## Fluxo do lançamento de comprovante

<!-- Correção 2026-09-11: a primeira versão deste fluxo dizia que o sistema confirmava e salvava
sozinho quando não detectava problema. Isso estava errado — o desenvolvedor esclareceu que a
aprovação do usuário é obrigatória em todos os casos que chegam a ter algo pra aprovar. Corrigido
abaixo; não repetir a versão anterior. -->

1. O comprovante é recebido (foto/upload).
2. O sistema analisa e mapeia (parseia) para um lançamento.
3. **Se não conseguir parsear nada aproveitável, mostra mensagem de erro** — não chega a gerar
   lançamento para aprovação.
4. **Se conseguir parsear ao menos uma parte aproveitável do comprovante** (hoje pensado como um
   limiar de ~50% de aproveitamento — número provisório, a revisar quando entidades e a
   implementação do parser forem discutidas), **mostra ao usuário o que conseguiu parsear para
   aprovação.**
5. **A aprovação do usuário é obrigatória em todos os casos que chegam ao passo 4** — não existe
   confirmação automática sem o usuário ver e aprovar. A tela de aprovação tem **botão de editar**
   para o usuário corrigir qualquer valor que tenha ficado errado antes de confirmar.
6. Só depois da aprovação (com ou sem edição) o lançamento é de fato salvo — e a partir daí passa
   a valer a regra de imutabilidade acima.

## Correção de lançamento de comprovante errado

Cenário: o lançamento gerado está errado **mesmo depois de aprovado** (passo 5 acima). Duas causas
possíveis:

- **Bug do sistema** — o comprovante em si está correto, mas o sistema não o leu corretamente
  (e o usuário não percebeu/corrigiu no botão de editar do passo 5).
- **Usuário aprovou sem revisar direito** — a aprovação é obrigatória, mas nada garante que o
  usuário revisou com atenção; se aprovar um valor errado, o dado errado fica salvo.

Em qualquer um dos dois casos, **como o lançamento de comprovante não é editável depois de
salvo**, a correção não é edição: **precisamos permitir cadastrar um lançamento de estorno/ajuste
vinculado ao lançamento original**, para compensar o valor errado sem alterar o registro
original.

## Comprovante (o arquivo)

**O arquivo do comprovante é imutável depois de salvo.** Uma vez criado, não é possível trocar o
arquivo. No máximo, exclusão — que, como toda exclusão no sistema, é lógica, nunca física (ver
[`exclusao-logica.md`](exclusao-logica.md)). Confirmado em 2026-09-11.

## Requisito levantado — a resolver depois

**Receber comprovantes variados e mapear para objetos/entidades operáveis pela API, a fim de
gerar relatórios.** Ainda sem mecanismo definido — relaciona-se com a decisão pendente #2 do hub
(OCR próprio vs. Azure Document Intelligence / Google Document AI / Textract / outro). Este
requisito é o "como" desse mapeamento; a decisão #2 é o "com quê".

## Em aberto — a aprofundar depois

- Limiar de ~50% de aproveitamento do parse: número provisório, a revisar quando entidades e o
  parser forem detalhados.
- Granularidade exata da trava do estorno/ajuste (imutável desde a criação, ou com prazo como o
  manual). Que dados obrigatórios ele carrega, além da referência ao lançamento original.
- Prazo de 1 dia para editar lançamento manual: conta a partir do timestamp exato de criação, ou
  até a virada do dia (meia-noite)?
- Se o comprovante for excluído (logicamente), o que acontece com o lançamento gerado dele — é
  excluído também, ou fica órfão/mantido como está?

## Impacto (a confirmar quando o modelo fechar)

- `dnzcontrol-api` — lançamento precisa de campo de origem (`comprovante` | `manual` | `estorno`)
  e regra de imutabilidade condicional a essa origem.
- `superdiniz-hub/regras/seguranca.md` / `regras/backend.md` — autorização de edição de lançamento
  passa a depender da origem, não só do papel de quem pede.
- `superdiniz-hub/contexto/decisoes-pendentes.md` #2 — este arquivo é o requisito de negócio que
  sustenta aquela decisão técnica; seguem ligados, mas #2 continua sem mecanismo escolhido.
