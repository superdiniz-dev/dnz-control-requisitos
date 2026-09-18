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

## Plano futuro — expurgo físico de inativos

Levantado pelo desenvolvedor em 2026-09-18, **fora do MVP**: uma rotina agendada que **apaga
fisicamente** registros inativos (excluídos logicamente) depois de um período longo, em
conformidade com a LGPD e para preservar espaço no banco. Nasceu do caso do lançamento excluído que
ficou só para rastreio porque já existe outro vigente com o mesmo ID da transação — ver
[`dados-do-lancamento.md`](dados-do-lancamento.md).

Quando for detalhado, precisa responder:

- **Conflito com regras atuais.** Hoje nenhum registro é apagado fisicamente
  ([`exclusao-logica.md`](exclusao-logica.md)) e a regra inegociável 4 do hub diz que histórico
  financeiro não se apaga. O expurgo seria a exceção explícita a essas duas regras — e as duas
  precisam passar a dizê-lo.
- **Prazo.** "Um bom tempo" precisa virar número, e o piso vem da guarda legal/fiscal de documento
  financeiro — o que traz de volta a pergunta desta página, hoje sem obrigação levantada.
- **Alcance.** Só lançamentos inativos, ou também o arquivo do comprovante, usuários, empresas?
- **Rastro do expurgo.** Fica algum registro de que algo existiu e foi apagado, e por quem/quando?

## Impacto (a confirmar quando o modelo fechar)

- `superdiniz-hub/contexto/decisoes-pendentes.md` #12 — respondida a princípio: sem retenção
  mínima/máxima definida; revisitar se surgir exigência legal/fiscal (LGPD, guarda fiscal).
- `superdiniz-hub/regras/banco-de-dados.md` — nenhuma rotina de expurgo por prazo a implementar
  por ora.
