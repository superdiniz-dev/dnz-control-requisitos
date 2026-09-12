# Retenção de dados

<!-- RASCUNHO — brainstorm em andamento com o desenvolvedor (2026-09-11). -->

## Regra

**A princípio, sem limite de retenção** — nenhum prazo de guarda definido hoje para comprovante ou
lançamento. Nenhuma obrigação legal/fiscal de guarda foi levantada ainda pelo desenvolvedor.
Confirmado em 2026-09-11 como ponto de partida, não definitivo — revisar assim que uma exigência
legal/fiscal for identificada. Responde a decisão pendente #12 do hub, a princípio.

Distinto de [`exclusao-logica.md`](exclusao-logica.md): retenção é **por quanto tempo** guardar;
exclusão lógica é **como** remover (nunca fisicamente) quando algo é removido. Sem prazo de
retenção definido, a exclusão lógica segue sendo a única forma de remoção — não há expurgo
automático por tempo.

## Impacto (a confirmar quando o modelo fechar)

- `superdiniz-hub/contexto/decisoes-pendentes.md` #12 — respondida a princípio: sem retenção
  mínima/máxima definida; revisitar se surgir exigência legal/fiscal (LGPD, guarda fiscal).
- `superdiniz-hub/regras/banco-de-dados.md` — nenhuma rotina de expurgo por prazo a implementar
  por ora.
