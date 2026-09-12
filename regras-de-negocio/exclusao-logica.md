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
  relatório pode escolher ver também o que foi excluído logicamente.
- **Usuário excluído logicamente perde o login imediatamente.** Confirmado em 2026-09-11.

## Em aberto — a aprofundar depois

- Existe reversão (reativar um registro excluído), e quem tem permissão para isso?
- Isso vale igualmente para dado financeiro (lançamento, comprovante) e para dado de conta
  (usuário, papel, empresa, grupo), ou há diferença de tratamento entre os dois?
- O filtro "ver inativos" nos relatórios do painel web vale só para o papel que já teria acesso ao
  registro se ele estivesse ativo, ou abre visibilidade adicional?

## Impacto (a confirmar quando o modelo fechar)

- `dnzcontrol-api` — todo modelo de dados herda o campo **Status** da
  [entidade base](entidade-base.md) em vez de sofrer `DELETE` físico; toda leitura precisa filtrar
  inativos por padrão.
- `superdiniz-hub/regras/banco-de-dados.md` — hoje não cita exclusão lógica como padrão; migra
  quando este modelo fechar.
- Relaciona-se com a regra inegociável 4 do hub (rascunho: fechamento de período/relatório como
  snapshot imutável) — ambas tratam de **não apagar/alterar histórico**, mas não são a mesma regra.
