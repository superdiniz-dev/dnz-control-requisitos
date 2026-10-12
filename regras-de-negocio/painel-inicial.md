# Painel inicial

<!-- RASCUNHO — brainstorm em andamento com o desenvolvedor (2026-09-18). -->

## Modelo

**A página inicial do app é um painel (dashboard)**, um retrato da situação financeira. Confirmado
em 2026-09-18. Mostra:

- **Gastos e receitas por mês.**
- **Últimos lançamentos.**
- **Central de notificações** — ver [`notificacoes.md`](notificacoes.md).

## Escopo

**O painel mostra a rede, ou as empresas liberadas para quem vê, e é filtrável como um BI.**
Confirmado em 2026-09-18. Ele abre com todas as empresas do escopo de leitura de quem vê (a rede
inteira para a Diretoria, as empresas atribuídas para o Gerente), e quem vê filtra à vontade, como
numa ferramenta de BI. No caso base, com uma empresa só, o painel é o da empresa, sem nada de rede
à vista (ver [`estrutura-empresarial.md`](estrutura-empresarial.md), "Caso base"). Descartados:
uma empresa por vez e painel diferente por papel.

### Filtros

Confirmado em 2026-09-18. No MVP, o painel filtra por:

- **Empresa** — uma, várias ou todas as do escopo de quem vê.
- **Período** — pela data da transação (padrão) ou pela de vencimento, como no relatório (ver
  [`relatorios.md`](relatorios.md), "Qual data define o período").
- **Tipo** — gasto ou receita.
- **Categoria** — uma ou mais.
- **Forma de pagamento** — PIX, boleto ou dinheiro.
- **Origem** — comprovante, lote ou manual.
- **Recebedor/pagador** — uma contraparte, por CPF/CNPJ ou nome (ex.: quanto foi pago a um
  fornecedor).

Os filtros se combinam entre si.

**Clicar num gráfico vira filtro.** Confirmado em 2026-09-18. Clicar (ou tocar, no celular) numa
barra ou fatia aplica aquele valor como filtro: ele aparece junto dos demais filtros, pode ser
removido, e todo o painel se atualiza, inclusive os últimos lançamentos. Descartado: gráficos só
de leitura, com filtro apenas pela barra de filtros.

**O painel abre no mês atual.** Confirmado em 2026-09-18. O foco ao abrir é "como estou agora";
para ver vários meses lado a lado, quem vê amplia o período no filtro. Descartados: últimos 12
meses e ano corrente.

**Os números do painel seguem as regras do relatório.** Confirmado em 2026-09-18. Em particular, a
**transferência interna** sai dos totais quando as duas pontas estão entre as empresas filtradas e
aparece à parte ("Transferências internas"), como no consolidado (ver
[`relatorios.md`](relatorios.md)). Assim, o painel e o relatório gerado com os mesmos filtros dão
o mesmo número. Descartado: o painel mostrar tudo bruto (números diferentes do relatório para o
mesmo período).

**Últimos lançamentos: os 5 mais recentes, com "ver todos".** Confirmado em 2026-09-18. A lista
segue os filtros do painel, e "ver todos" abre a lista completa de lançamentos com os mesmos
filtros. Descartados: 10 itens e a lista completa paginada dentro do painel.

**"Mais recentes" é pela data da transação** — o dinheiro que se moveu por último. Confirmado em
2026-09-18. Um comprovante antigo lançado hoje entra na posição da própria data e pode não
aparecer entre os 5. Descartado: ordenar pela data do registro no sistema.

**Abrir ou filtrar o painel não entra no registro de geração de relatório.** Confirmado em
2026-09-18. O painel é a tela inicial, e registrar cada abertura e cada filtro encheria o registro
de ruído. O registro fica para o relatório gerado de propósito e para a exportação (ver
[`relatorios.md`](relatorios.md), "Registro de geração"). Descartado: registrar cada abertura.

## Em aberto — a aprofundar depois

Nada em aberto por ora.

## Impacto (a confirmar quando o modelo fechar)

- `dnzcontrol-app` — tela inicial com painel.
- `dnzcontrol-api` — consulta agregada de gastos/receitas por mês e dos últimos lançamentos,
  respeitando o escopo de leitura (regra inegociável 1 do hub), com filtros combináveis por
  dimensão; o filtro nunca amplia o escopo.
