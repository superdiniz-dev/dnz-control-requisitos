# Entidade base

<!-- RASCUNHO — brainstorm em andamento com o desenvolvedor (2026-09-11). -->

## Modelo

Toda entidade do sistema herda de uma **entidade básica** comum, que carrega estes atributos:

| Atributo | O que guarda |
|---|---|
| **Status** (ativo/inativo) | A base da [exclusão lógica](exclusao-logica.md) — "excluir" é marcar como inativo, nunca apagar. |
| **Data de criação** | Data e hora de criação da entidade. |
| **Data de atualização** | Data e hora da última atualização da entidade. |
| **Criado por** | O usuário que criou a entidade. |
| **Atualizado por** | O usuário que fez a última atualização da entidade. |

Os três primeiros foram confirmados em 2026-09-11. **Criado por** e **atualizado por** entraram em
2026-09-18, para que o sistema saiba dizer quem criou cada registro e quem mexeu por último,
inclusive quem excluiu (excluir é mudar o status). Descartados: não registrar autoria (o sistema
não saberia dizer quem criou um lançamento manual nem quem excluiu um usuário) e histórico completo
de cada mudança, com o antes e o depois (volume e esforço maiores do que a necessidade atual).

## O que conta como atualização

**Qualquer mudança conta, inclusive de status.** Confirmado em 2026-09-18. Excluir ou reativar muda
a data de atualização e o "atualizado por", como qualquer edição.

**A imutabilidade vale para o conteúdo de negócio, não para a entidade base.** Um registro imutável
(lançamento de comprovante, estorno/ajuste, arquivo de comprovante) nunca tem valor, datas, partes
ou arquivo alterados. Mas a exclusão lógica muda o status dele e, com isso, a data de atualização
e o "atualizado por". Num lançamento excluído, portanto, "atualizado por" e data de atualização
dizem quem excluiu e quando. Descartado: só contar mudança de conteúdo (exigiria campos próprios de
"excluído por" e "excluído em").

## Em aberto — a aprofundar depois

Nada em aberto por ora.

## Impacto (a confirmar quando o modelo fechar)

- `dnzcontrol-api` — classe/entidade base no modelo de dados, da qual toda entidade herda.
- `superdiniz-hub/regras/banco-de-dados.md` — hoje não cita entidade base nem estes cinco campos
  como padrão; migra quando este modelo fechar.
- Formaliza o mecanismo de [`exclusao-logica.md`](exclusao-logica.md), que até aqui falava em
  "campo de exclusão lógica" de forma genérica — agora é o campo **Status** da entidade base.
