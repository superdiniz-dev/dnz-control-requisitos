# Entidade base

<!-- RASCUNHO — brainstorm em andamento com o desenvolvedor (2026-09-11). -->

## Modelo

Toda entidade do sistema herda de uma **entidade básica** comum, que carrega estes atributos:

| Atributo | O que guarda |
|---|---|
| **Status** (ativo/inativo) | A base da [exclusão lógica](exclusao-logica.md) — "excluir" é marcar como inativo, nunca apagar. |
| **Data de criação** | Data e hora de criação da entidade. |
| **Data de atualização** | Data e hora da última atualização da entidade. |

Confirmado em 2026-09-11.

## Em aberto — a aprofundar depois

- Existe mais algum atributo básico além destes três (ex.: quem criou/quem atualizou por último)?
- "Data de atualização" registra a última mudança de **qualquer** campo, ou só de campos de
  negócio (mudar o status pra inativo conta como atualização)?
- Isso muda algo nas regras de imutabilidade já registradas (lançamento de comprovante, estorno,
  arquivo de comprovante)? Ex.: um registro imutável ainda pode ter sua "data de atualização"
  tocada pela própria exclusão lógica (mudança de status), mesmo sem edição de conteúdo de
  negócio?

## Impacto (a confirmar quando o modelo fechar)

- `dnzcontrol-api` — classe/entidade base no modelo de dados, da qual toda entidade herda.
- `superdiniz-hub/regras/banco-de-dados.md` — hoje não cita entidade base nem estes três campos
  como padrão; migra quando este modelo fechar.
- Formaliza o mecanismo de [`exclusao-logica.md`](exclusao-logica.md), que até aqui falava em
  "campo de exclusão lógica" de forma genérica — agora é o campo **Status** da entidade base.
