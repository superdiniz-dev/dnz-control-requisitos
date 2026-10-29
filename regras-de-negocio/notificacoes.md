# Notificações

<!-- RASCUNHO — brainstorm em andamento com o desenvolvedor (2026-09-18). -->

## Modelo

Confirmado em 2026-09-18:

- **Central de notificações** no app, visível no painel inicial (ver
  [`painel-inicial.md`](painel-inicial.md)).
- **Pagamentos recorrentes:** dá para cadastrar as datas de pagamentos que se repetem, e o app
  notifica quando elas chegam.
- **Cobranças federais padrão:** o sistema já traz notificações das obrigações federais comuns
  (ex.: vencimentos de tributos federais), **parametrizáveis**.

## Pagamento recorrente é só lembrete

**O pagamento recorrente nunca gera lançamento.** Confirmado em 2026-09-18. Na data, o app avisa
(ex.: "Aluguel vence hoje"), e o lançamento continua nascendo do comprovante, ou do lançamento
manual, quando o pagamento é de fato feito (ver [`lancamentos.md`](lancamentos.md)). Nada entra no
histórico financeiro sem pagamento real, então o lembrete não esbarra em duplicidade,
imutabilidade nem relatório. Descartados: lembrete com atalho "registrar pagamento" já com
categoria e recebedor sugeridos, e lançamento "previsto" que vira real quando o comprovante chega
(um novo estado de lançamento).

### O que se cadastra

Confirmado em 2026-09-18 (cadastro completo):

| Dado | Obrigatório? |
|---|---|
| Descrição (ex.: "Aluguel") | obrigatório |
| `Empresa` | obrigatório |
| Periodicidade — semanal, quinzenal, mensal, trimestral ou anual | obrigatório |
| Valor previsto | opcional |
| Categoria | opcional |
| Data de início — é o primeiro vencimento | obrigatório |
| Data de fim | opcional — em branco, o recorrente não tem fim |
| Recebedor — nome e CPF/CNPJ | opcional |
| Forma de pagamento | opcional |

Descartados: cadastro enxuto (sem recebedor, forma de pagamento, início/fim e só mensal ou anual)
e mínimo (só descrição, empresa e data, sempre mensal). A obrigatoriedade de início, fim,
recebedor e forma de pagamento foi confirmada também em 2026-09-18: um lembrete como "pagar o
contador" não deve travar por falta de CNPJ. Descartados: tudo obrigatório (fim obrigatório não
serve a recorrente sem prazo) e tudo opcional.

### Como se calculam os vencimentos

**Tudo sai da data de início, que é o primeiro vencimento.** Confirmado em 2026-09-18. Não há
campo próprio de "dia do vencimento".

- **Semanal e quinzenal:** a cada 7 ou 14 dias a partir do início.
- **Mensal, trimestral e anual:** no mesmo dia do mês do início. Se esse dia não existe no mês
  (ex.: 31 em fevereiro), o vencimento cai no último dia do mês.
- **Fim de semana e feriado não mudam o vencimento:** o aviso sai na data calculada.

Descartados: mover para o próximo dia útil (exigiria calendário de feriados) e campo próprio de
dia do mês ou da semana.

**Exceção: opção "vencimento no último dia útil do mês", no recorrente mensal.** Confirmado em
2026-10-16. Serve sobretudo ao **parcelamento de débito federal** (Simples, Receita ou PGFN), cuja
parcela vence no último dia útil de cada mês. Com a opção marcada, a data de início só indica o mês
da primeira parcela, e cada vencimento é o último dia útil do mês, calculado pelo calendário de
feriados nacionais do Administrador (o mesmo da cobrança federal). Sem a opção, vale a regra acima:
o vencimento não se move. O parcelamento é cadastrado como recorrente comum, com data de início e
data de fim, e a empresa pode ter vários. Descartados: item "parcelamento federal" no catálogo
(dado novo na ligação, que hoje é só liga/desliga, e não atende bem dois parcelamentos ao mesmo
tempo) e recorrente sem mudança, com dia fixo escolhido pelo usuário (o aviso sai antes do prazo
real e depende de o usuário conhecer a regra).

### Quem cadastra

**Quem lança na empresa cadastra, edita e exclui o pagamento recorrente dela.** Confirmado em
2026-09-18. A Diretoria faz isso em qualquer `Empresa` da rede, e o Gerente nas empresas
atribuídas a ele, que é o mesmo escopo de quem registra lançamento. Descartado: só a Diretoria.

### Quando avisa

**Dois avisos: um antes do vencimento e outro no dia.** Confirmado em 2026-09-18. A antecedência
padrão do primeiro aviso é de **3 dias**, **configurável por `GrupoEmpresa`**: vale para todos os
recorrentes da rede, e não recorrente a recorrente. Descartados: antecedência por recorrente, só
no dia e antecedência fixa sem configuração.

**Só a Diretoria configura a antecedência, de 1 a 30 dias.** Confirmado em 2026-09-18. O aviso no
dia continua sempre, e zero não é permitido (seria o mesmo aviso duas vezes). Descartados: sem
limite e configuração pelo Administrador.

## Cobranças federais padrão

**Catálogo por regime tributário, com liga/desliga por empresa.** Confirmado em 2026-09-18.

- **O Administrador mantém o catálogo padrão**, do sistema inteiro. Cada cobrança tem nome, regra
  de vencimento e os regimes tributários em que se aplica (MEI, Simples Nacional, Lucro Presumido,
  Lucro Real).
- **Cada `Empresa` informa o regime tributário**, e o sistema liga sozinho as cobranças desse
  regime (ver [`estrutura-empresarial.md`](estrutura-empresarial.md)).
- **Quem lança na empresa ajusta:** desliga uma cobrança que não se aplica ou liga outra do
  catálogo.
- Os avisos usam a **mesma antecedência da rede** do pagamento recorrente, mais o aviso no dia.
- Como o recorrente, a cobrança federal é **só lembrete**: nunca gera lançamento.

Descartados: catálogo único sem regime, com tudo desligado de início (depende de o usuário saber o
que a empresa paga), e modelos que viram pagamentos recorrentes comuns (se a data legal mudar, os
recorrentes criados não se atualizam).

**Vencimento em dia não útil segue a regra de cada cobrança.** Confirmado em 2026-09-18. No
catálogo, cada cobrança diz se **antecipa** (dia útil anterior) ou **prorroga** (dia útil seguinte)
quando vence em dia não útil. O sistema usa um **calendário de feriados nacionais mantido pelo
Administrador**, e o aviso sai na data real do prazo. Isso vale só para a cobrança federal: no
pagamento recorrente o vencimento não se move, salvo a opção "último dia útil do mês" (ver "Como se
calculam os vencimentos"). Descartados: não ajustar, como no recorrente (o
aviso "no dia" chegaria depois do prazo quando a regra é antecipar), e sempre antecipar.

**Cobrança que depende de ter empregado nasce desligada.** Confirmado em 2026-09-19. As
cobranças de folha (ex.: contribuição previdenciária e FGTS) ficam no catálogo marcadas como
**fora do liga-automático**: o regime tributário não as liga. Quem lança na empresa liga quando a
empresa tem empregado, como já pode fazer com qualquer cobrança do catálogo. Nenhum dado novo na
`Empresa`. Descartados: marcação "tem empregados" na `Empresa` (mais um campo de cadastro) e
ligar a folha para todos (aviso de FGTS para quem não tem empregado).

**No Lucro Real, IRPJ e CSLL nascem desligados.** Confirmado em 2026-10-16. A empresa no Lucro
Real escolhe entre apuração trimestral e anual por estimativa mensal, e o regime tributário não diz
qual. Por isso as duas cobranças de IRPJ/CSLL ficam **fora do liga-automático para o Lucro Real**,
como a folha: quem lança na empresa liga a que se aplica. Enquanto nenhuma das duas estiver ligada,
a tela de cobranças da empresa **destaca a pendência** ("escolha a apuração de IRPJ/CSLL"). No Lucro
Presumido, o IRPJ/CSLL trimestral segue ligado pelo regime. Nenhum dado novo na `Empresa`.
Descartados: campo "forma de apuração" na `Empresa` (mais um campo de cadastro, como na folha) e
ligar as duas (aviso de uma cobrança que a empresa não paga).

**IRPJ/CSLL trimestral em quotas: a escolha é feita na própria ocorrência.** Confirmado em
2026-10-16. A lei deixa a empresa pagar o IRPJ/CSLL de cada trimestre em quota única ou em até 3
quotas (último dia útil dos 3 meses seguintes ao fim do trimestre), escolhendo trimestre a
trimestre. A 1ª quota vence no mesmo dia da quota única, então o aviso da ocorrência já a cobre. Ao
**marcar a ocorrência do trimestre como paga**, quem lança na empresa pode indicar **"paguei a 1ª de
3 quotas"**; o sistema então cria as ocorrências da 2ª e da 3ª quotas, que seguem o ciclo normal
(avisos, marcar como paga, vencida). Vale onde o IRPJ/CSLL trimestral estiver ligado (Lucro
Presumido e Lucro Real trimestral). Como a cobrança é só lembrete, sem valor, o sistema não confere
o mínimo legal das quotas. Descartados: duas cobranças no catálogo, "quota única" e "em 3 quotas"
(a escolha ficaria fixa, e quem alterna teria de trocar a cobrança ligada a cada trimestre), e só
quota única no MVP (sem aviso da 2ª e da 3ª quotas).

**Desfazer a marcação da 1ª quota tira a 2ª e a 3ª, se nenhuma delas foi marcada.** Confirmado em
2026-10-16. Quando a marcação da ocorrência que gerou as quotas é desfeita, as ocorrências da 2ª e
da 3ª quotas saem da lista ativa, com registro no histórico. Se a 2ª ou a 3ª já estiver marcada como
paga, o sistema **recusa** e pede para desfazer essas marcações antes, da última para a primeira.
Assim nunca sobra quota sem a 1ª. Descartados: desfazer em cascata, inclusive marcações já feitas
nas outras quotas (um clique desfaria várias marcações), e manter as quotas criadas até uma ação
própria "não vou pagar em quotas" (avisos da 2ª e da 3ª sem a 1ª paga).

**Retenções entram no catálogo, fora do liga-automático.** Confirmado em 2026-10-16. O IRRF retido
(sobre serviços de outra empresa e sobre aluguel pago a pessoa física) e o PIS/Cofins/CSLL retidos
de empresa para empresa dependem do que a empresa faz no mês, não do regime tributário. Ficam no
catálogo como a folha: o regime não os liga, quem lança na empresa liga quando a empresa retém e
desliga quando deixa de reter. Sem destaque de pendência na tela (muitas empresas não retêm nada) e
sem campo novo na `Empresa`. **Se a empresa deve reter é decisão do contador**; o sistema só lembra
o prazo de quem ligou. Limitação conhecida: a cobrança é mensal fixa, então o aviso sai mesmo num
mês sem retenção. Descartados: ligar pelo regime (aviso para quem não retém) e deixar fora do MVP,
como pagamento recorrente (o recorrente não antecipa em dia não útil).

**Cobrança anual vinculada à principal.** Confirmado em 2026-10-16. Uma cobrança do catálogo pode
ser **vinculada** a outra, a principal: ligar a principal liga a vinculada junto, e desligar a
principal desliga a vinculada. Quem lança na empresa ainda pode desligar só a vinculada. No catálogo
inicial há dois vínculos: o **saldo do ajuste anual de IRPJ/CSLL** acompanha a **estimativa mensal**
do Lucro Real, e o **INSS sobre o 13º salário** acompanha a **contribuição previdenciária sobre a
folha**. Assim a cobrança anual, a mais fácil de esquecer, não depende de alguém lembrar de ligá-la.
Descartados: itens independentes, ligados um a um (depende de lembrar de uma cobrança que só aparece
uma vez por ano), e a anual como parte da própria cobrança principal (dois padrões de vencimento
dentro de um item só).

### Rascunho do catálogo inicial — A CONFERIR COM O CONTADOR

Montado em 2026-09-19 a partir de fontes públicas, **não validado**. Nada aqui é regra confirmada:
o contador da empresa principal confere cada linha antes de o catálogo ir para produção.

| Cobrança | Regimes | Vencimento | Dia não útil | Fonte |
|---|---|---|---|---|
| DAS — Simples Nacional | Simples Nacional | dia 20 do mês seguinte à receita | prorroga | [Agenda do Simples Nacional](https://www8.receita.fazenda.gov.br/SimplesNacional/ConteudoApoio/agenda/agenda.aspx) |
| DAS-MEI | MEI | dia 20 de cada mês, referente ao mês anterior | prorroga | [gov.br — Como pagar o DAS](https://www.gov.br/empresas-e-negocios/pt-br/empreendedor/servicos-para-mei/pagamento-de-contribuicao-mensal/como-pagar-o-das) |
| PIS/Pasep e Cofins | Lucro Presumido, Lucro Real | dia 25 do mês seguinte ao fato gerador | antecipa | Lei 11.933/2009 |
| IRPJ e CSLL — trimestral | Lucro Presumido (e Lucro Real trimestral) | último dia útil do mês seguinte ao fim do trimestre (abril, julho, outubro, janeiro) | antecipa | Lei 9.430/1996 |
| IRPJ e CSLL — estimativa mensal | Lucro Real anual | último dia útil do mês seguinte | antecipa | Lei 9.430/1996 |
| Contribuição previdenciária e IRRF sobre folha (DCTFWeb) | todos, se houver empregado | dia 20 do mês seguinte | antecipa | [Receita — DCTFWeb](https://www.gov.br/receitafederal/pt-br/assuntos/orientacao-tributaria/declaracoes-e-demonstrativos/DCTFWeb) |
| FGTS (FGTS Digital) | todos, se houver empregado | dia 20 do mês seguinte | antecipa | Lei 8.036/1990, art. 15 |
| IRRF retido — serviços de PJ e aluguel pago a PF | todos, se houver retenção (fora do liga-automático) | dia 20 do mês seguinte | antecipa | [Agenda Tributária — 20/10/2026](https://www.gov.br/receitafederal/pt-br/assuntos/agenda-tributaria/2026/Outubro/dia-20-10-2026) |
| Saldo do ajuste anual de IRPJ/CSLL | Lucro Real anual — vinculada à estimativa mensal | último dia útil de março do ano seguinte | antecipa | Lei 9.430/1996, art. 6º, § 1º |
| INSS sobre o 13º salário | todos, se houver empregado — vinculada à contribuição previdenciária sobre a folha | 20 de dezembro | antecipa | Decreto 3.048/1999, art. 216 (não conferido em fonte oficial) |
| PIS/Cofins/CSLL retidos de PJ para PJ (CSRF) | todos, se houver retenção (fora do liga-automático) | dia 20 do mês seguinte | antecipa | [Agenda Tributária — 20/10/2026](https://www.gov.br/receitafederal/pt-br/assuntos/agenda-tributaria/2026/Outubro/dia-20-10-2026) |

Observações para a conferência:

- **"Último dia útil do mês" cabe no modelo do catálogo**, sem regra nova: vencimento no último dia
  do mês (que já cai no último dia quando o mês é curto) com "antecipa".
- **IRPJ/CSLL trimestral pode ser pago em três quotas**: a escolha é feita ao marcar a ocorrência
  como paga (confirmado acima).
- **Lucro Real** escolhe entre apuração trimestral e anual por estimativa mensal: as duas
  cobranças de IRPJ/CSLL ficam fora do liga-automático no Lucro Real (confirmado acima).
- **Folha (DCTFWeb e FGTS)** só se aplica a quem tem empregado: fica fora do liga-automático
  (confirmado acima).
- No Simples Nacional, a contribuição previdenciária em geral já está no DAS; a exceção de algumas
  atividades fica para o contador conferir.
- Fontes com divergência: há sites que dizem que o DAS "antecipa". A página oficial da agenda do
  Simples Nacional diz que **prorroga** ("dia útil imediatamente posterior").

## Quem recebe

**Todos os usuários que enxergam a empresa recebem os avisos dela, e cada um pode desligá-los
para si, por empresa.** Confirmado em 2026-09-18. Vale para pagamento recorrente e cobrança
federal: a Diretoria da rede e os Gerentes atribuídos à empresa recebem por padrão, e quem não
quer o ruído de uma empresa desliga. Descartados: todos sem opção de desligar (a Diretoria de uma
rede grande receberia todos os avisos de todas as empresas) e um responsável por recorrente.

## Canal

**Central no app, push no celular e e-mail.** Confirmado em 2026-09-18.

- **Central de notificações:** toda notificação fica nela, no celular e na web.
- **Push no celular:** é o que faz o lembrete funcionar com o app fechado. Na web não há push, só
  a central.
- **E-mail:** também faz parte do MVP.

Descartados: só central com push, sem e-mail, e só a central.

**O e-mail é um resumo diário, com a marca da rede.** Confirmado em 2026-09-18. Um e-mail por dia,
de manhã, reúne os avisos do dia ("vence hoje" e "vence em X dias") de todas as empresas que o
usuário recebe, e só sai se houver aviso. Leva o nome e o logo da rede (white-label). O usuário
desliga o e-mail separado do push. O push continua um por aviso. Descartados: um e-mail por aviso
(enche a caixa de entrada) e resumo com a marca DNZ Control (quebra o white-label).

## Ciclo da notificação

**A ocorrência se marca como paga, valendo para todos.** Confirmado em 2026-09-19. Quem lança na
empresa marca um vencimento específico (ex.: "Aluguel — 10/10") como pago. É só uma marcação: não
gera lançamento nem entra no histórico financeiro. Os avisos que ainda faltam daquela ocorrência
deixam de sair para todos que os recebem, e a central mostra quem marcou e quando. Vale para
pagamento recorrente e cobrança federal. Descartados: sem marcação de pago (o aviso do dia sai
mesmo com a conta paga, para todos) e baixa automática quando entra um lançamento parecido
(heurística sobre recebedor e categoria, que são opcionais, e sujeita a falso positivo).

**Ocorrência vencida e não marcada fica pendente na central, sem novos avisos.** Confirmado em
2026-09-19. Passado o dia do vencimento sem marcação de pago, a ocorrência aparece na central como
**"vencida"** até alguém marcar, e não sai mais push nem e-mail sobre ela. Descartados: aviso de
atraso repetido até marcar (volta o ruído, sobretudo quando a conta foi paga e só faltou marcar) e
nada depois do dia (a conta esquecida some da atenção).

**A marcação de pago se desfaz, por quem lança na empresa, a qualquer momento.** Confirmado em
2026-09-19. Desmarcada, a ocorrência volta ao estado que teria sem a marcação (pendente, ou
"vencida" se a data já passou), e os avisos futuros dela voltam a sair; os que já passaram não são
reenviados. A central guarda o histórico da marcação: quem marcou, quem desmarcou e quando. A
marcação não é dado financeiro, então a imutabilidade não se aplica a ela. Descartados: só quem
marcou desmarca (trava quando a pessoa não está disponível ou saiu) e marcação definitiva (erro de
clique sem conserto).

**Na própria central, cada usuário tem lida/não lida e dispensar, só para si.** Confirmado em
2026-09-19.

- **Lida/não lida:** a notificação chega **não lida**, com contador no ícone da central, e fica
  **lida** quando o usuário abre a central ou toca no push. Dá para voltar a "não lida".
- **Dispensar:** tira o aviso da lista principal só para quem dispensou; ele continua acessível num
  filtro "dispensadas". Dispensar uma ocorrência "vencida" não muda nada para os outros: ela segue
  vencida até alguém marcar como paga.

Descartados: só lida/não lida (a única forma de limpar a lista seria marcar como pago, o que
incentiva marcação falsa) e só dispensar, sem estado de lida.

## Em aberto — a aprofundar depois

- **Cobranças federais — conteúdo inicial do catálogo:** quais cobranças entram no MVP, e com
  quais regras de vencimento? Precisa ser conferido com fonte oficial (Receita Federal) ou com o
  contador da empresa principal. Rascunho montado em 2026-09-19, em "Rascunho do catálogo
  inicial", aguardando o contador.

## Impacto (a confirmar quando o modelo fechar)

- `dnzcontrol-app` — central de notificações; notificação push (se confirmada), que depende do app
  próprio de cada rede na loja (ver [`estrutura-empresarial.md`](estrutura-empresarial.md)).
- `dnzcontrol-api` — cadastro de recorrências, calendário de cobranças federais parametrizável,
  agendamento de disparo de notificações.
