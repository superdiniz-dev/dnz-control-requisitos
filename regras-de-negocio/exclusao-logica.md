# Exclusão lógica

<!-- RASCUNHO — brainstorm em andamento com o desenvolvedor (2026-09-11). -->

## Regra

**Nenhum registro é fisicamente apagado do sistema.** Toda "exclusão" é lógica — o registro é
marcado como excluído/inativo, mas permanece no banco. Confirmado como regra geral em 2026-09-11,
no contexto de exclusão de usuário (papel), mas nasce com vocação de valer para o domínio inteiro
(comprovante, lançamento, relatório, empresa, grupo — a confirmar caso a caso conforme o brainstorm
avança). Mecanismo concreto: o campo **Status** (ativo/inativo) da
[entidade base](entidade-base.md), que toda entidade do sistema herda.

**Por quê:** auditoria (histórico de quem existiu/agiu no sistema não pode sumir) e conformidade
com a LGPD (guarda de dado, rastreabilidade — decisão pendente #12 do hub sobre retenção segue
relacionada, mas não é a mesma coisa: retenção é *por quanto tempo* guardar, exclusão lógica é
*como* remover sem apagar).

## Visibilidade

- **No painel** (telas operacionais), um registro excluído logicamente **não é mais
  visualizável** — desaparece da tela normal.
- **Nos relatórios do painel web** (futuro), será possível **filtrar por inativos** — quem gera o
  relatório pode escolher ver também o que foi excluído logicamente. **O filtro não amplia o
  escopo:** mostra só os inativos que o papel veria se estivessem ativos (um Gerente vê os
  inativos das empresas a que está atribuído, e perde tudo de uma empresa, ativos e inativos, ao
  perder a atribuição). Confirmado em 2026-09-18 — abrir mais visibilidade seria furo na regra
  inegociável 1 do hub.
- **Usuário excluído logicamente perde o login imediatamente.** Confirmado em 2026-09-11.

## Reativação

**Existe só para dado de conta; dado financeiro não se reativa.** Confirmado em 2026-09-18.

- **Dado de conta** (usuário, `Empresa`, categoria…) **pode ser reativado.** Recriar partiria a
  identidade: um funcionário que sai e volta viraria outro usuário, e o histórico de quem lançou o
  quê ficaria dividido; uma empresa reaberta perderia a ligação com os próprios lançamentos.
- **Dado financeiro** (lançamento, cadeia de estorno, `LoteLancamentos`, comprovante) **não é
  reativado.** Excluído por engano, registra-se de novo — o excluído não bloqueia o ID da
  transação nem o arquivo. Reativar esbarraria em cadeia, lote, checagem de ID e arquivo liberado;
  registrar de novo resolve com um registro limpo. O excluído fica só para rastreio e auditoria.

Descartados: não existir reativação nenhuma (partiria a identidade de usuário e empresa) e
reativação para tudo (complexidade no dado financeiro sem ganho).

**Quem pode excluir, pode reativar.** Confirmado em 2026-09-18. A permissão de reativar é simétrica
à de excluir: a Diretoria reativa um Gerente ou uma categoria da rede; o Administrador reativa uma
Diretoria. Entre usuários do mesmo papel ninguém exclui o outro, então ninguém reativa o outro
([`papeis-e-hierarquia.md`](papeis-e-hierarquia.md)). Descartados: só o Administrador reativar
(tiraria da rede a autonomia de readmitir um funcionário) e qualquer papel acima reativar mesmo
sem poder excluir (quebraria a simetria).

## Em aberto — a aprofundar depois

Nada em aberto desde 2026-09-18. O expurgo físico de inativos (plano futuro) está em
[`retencao-de-dados.md`](retencao-de-dados.md).

## Impacto (a confirmar quando o modelo fechar)

- `dnzcontrol-api` — todo modelo de dados herda o campo **Status** da
  [entidade base](entidade-base.md) em vez de sofrer `DELETE` físico; toda leitura precisa filtrar
  inativos por padrão.
- `superdiniz-hub/regras/banco-de-dados.md` — hoje não cita exclusão lógica como padrão; migra
  quando este modelo fechar.
- Relaciona-se com a regra inegociável 4 do hub (histórico financeiro não se apaga nem se
  reescreve; relatório não tem fechamento e é calculado dos lançamentos vigentes — ver
  [`relatorios.md`](relatorios.md)) — ambas tratam de **não apagar/alterar histórico**, mas não são
  a mesma regra.
