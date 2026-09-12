# Relatórios

<!-- RASCUNHO — brainstorm em andamento com o desenvolvedor (2026-09-11). -->

## Modelo

Relatório é uma **visão de um período** — calculado a partir dos lançamentos vigentes daquele
período no momento em que é gerado. **A princípio, não existe uma ação de "fechar período".**
Não há snapshot armazenado à parte: se os lançamentos do período mudarem depois, o relatório
gerado de novo para o mesmo período reflete a mudança.

Isso responde a regra inegociável 4 do hub (`superdiniz-hub/regras/seguranca.md`, hoje marcada
`[RASCUNHO]`) — que supunha um fechamento imutável. Confirmado em 2026-09-11: **não existe esse
fechamento**, pelo menos a princípio.

## Compartilhamento

**Não existe compartilhamento vivo dentro do sistema** (ex.: link com token que dá acesso sem
login). O compartilhamento acontece **por exportação**: o relatório pode ser exportado em **PDF**,
**XLS** e **SVG**; uma vez exportado, o compartilhamento é do arquivo, fora do sistema. Confirmado
em 2026-09-11 — responde a decisão pendente #11 do hub.

Isso significa que a seção de "tokens de compartilhamento" cogitada em `regras/seguranca.md` não
se aplica — não há necessidade de token de acesso público a relatório.

## Em aberto — a aprofundar depois

- Se um lançamento de um período já "visto" em relatório é editado depois, o relatório antigo
  simplesmente deixa de existir como registro (só existe o relatório gerado agora, com o dado
  atual), ou fica algum rastro de que o relatório foi visto com outros valores em outro momento?
- Existe algum tipo de trava para editar lançamento muito antigo, mesmo sem "fechamento" formal —
  ou lançamento de qualquer data pode ser editado a qualquer momento, sem limite de tempo?
- Relatório é sempre por `Empresa`, ou também existe no nível `GrupoEmpresa` (consolidado — mesma
  pergunta já aberta em `estrutura-empresarial.md`)?

## Impacto (a confirmar quando o modelo fechar)

- `superdiniz-hub/regras/seguranca.md` — regra inegociável 4 deixa de ser rascunho de "snapshot
  imutável" e passa a documentar que relatório é visão dinâmica, sem fechamento. Atualizar quando
  este modelo migrar para o hub.
- `superdiniz-hub/contexto/decisoes-pendentes.md` #4 — respondida: não há fechamento de
  período/relatório, a princípio.
- `superdiniz-hub/contexto/decisoes-pendentes.md` #11 — respondida: compartilhamento é só por
  exportação de arquivo (PDF/XLS/SVG), não por link/token dentro do sistema.
- `dnzcontrol-api` — relatório não precisa de tabela própria de "snapshot fechado"; é uma consulta
  sobre lançamentos vigentes no período pedido. Precisa de geração de export em PDF, XLS e SVG.
