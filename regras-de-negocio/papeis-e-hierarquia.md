# Papéis e hierarquia

<!-- RASCUNHO — brainstorm em andamento com o desenvolvedor (2026-09-11). -->

## Modelo

- Um usuário pode ter **um ou mais papéis**.
- Papel é classificado como **interno** (parte da operação do próprio DNZ Control) ou **externo**
  (parte de uma empresa/tenant que usa o produto).
- Hoje existem três papéis:
  - **Administrador** — interno. Administra o sistema em si; externo a qualquer empresa cliente.
  - **Diretoria** — externo. Proprietário do `GrupoEmpresa` (a rede); gerencia **todas** as
    empresas da rede, sem precisar de atribuição empresa a empresa.
  - **Gerente** — externo. Gestor financeiro; tem acesso apenas às empresas da rede às quais foi
    **atribuído** — ver [`estrutura-empresarial.md`](estrutura-empresarial.md).

## Hierarquia

- Papéis se organizam hierarquicamente. Um papel de nível maior pode **ver e editar** o que um
  papel de nível menor vê e edita; o inverso não vale.
- Ordem, do maior para o menor: **Administrador → Diretoria → Gerente**.
- **Regra categórica:** todo papel interno tem prioridade maior que todo papel externo — mesmo
  havendo, no futuro, mais de um papel dentro de cada categoria.
- **A princípio, o acesso do papel interno sobre o externo é irrestrito** — o Administrador vê e
  edita qualquer dado de qualquer empresa cliente, extrato financeiro incluído, sem limite
  declarado hoje. Confirmado pelo desenvolvedor em 2026-09-11 como ponto de partida; não fechado
  como definitivo — pode ganhar exceção quando o modelo for aprofundado.
- **Cada usuário externo (Gerente ou Diretoria) pertence a exatamente um `GrupoEmpresa`** —
  confirmado em 2026-09-11. Ver [`estrutura-empresarial.md`](estrutura-empresarial.md).
- **A hierarquia só vale entre papéis diferentes.** Entre usuários do **mesmo papel** (ex.: duas
  Diretorias da mesma rede), um pode **editar** o outro, mas **nenhum pode excluir o outro**.
  Confirmado em 2026-09-11.
- **Toda exclusão de usuário é lógica, nunca física** — ver
  [`exclusao-logica.md`](exclusao-logica.md).
- **A princípio, Gerente e Diretoria usam a mesma interface** — mesmas telas/menus. A diferença
  entre os dois papéis é só **escopo de dado** (quais `Empresa` cada um enxerga), não telas ou
  opções exclusivas de um papel. Confirmado em 2026-09-11; não fechado como definitivo.

## Em aberto — a aprofundar depois

- Se e quando surgem outros papéis, dentro de cada categoria.

## Impacto (a confirmar quando o modelo fechar)

- `dnzcontrol-api` — modelagem de papel/hierarquia, autorização em toda mutação/leitura de recurso
  (regra inegociável 1 do hub).
- `superdiniz-hub/regras/seguranca.md` e `regras/banco-de-dados.md` — hoje ainda não citam papel;
  migram quando este modelo fechar.
- `superdiniz-hub/contexto/decisoes-pendentes.md` #10 — parcialmente respondida (existe papel
  administrativo distinto do operador, com acesso irrestrito a princípio), mas segue aberta até o
  restante da granularidade e a lista de papéis fechar.
- `superdiniz-hub/regras/seguranca.md` — regra inegociável 2 (nunca logar segredo/comprovante) e o
  acesso irrestrito do Administrador não são a mesma coisa: acesso amplo no domínio não abre
  exceção para logar dado sensível. Confirmar isso explicitamente quando a regra migrar para o hub.
