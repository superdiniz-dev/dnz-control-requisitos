# Dados do lançamento

<!-- RASCUNHO — brainstorm em andamento com o desenvolvedor (2026-09-17 e 2026-09-18). -->

O que um lançamento carrega. As regras de origem, edição e correção estão em
[`lancamentos.md`](lancamentos.md); aqui é só o conteúdo.

## De onde vem o conteúdo

A entrada principal do sistema é um **comprovante em PDF ou imagem**, que o sistema lê e mapeia
para o lançamento. Por isso o conteúdo do lançamento é modelado a partir do que um comprovante de
pagamento traz, e não de um formulário contábil genérico. Confirmado em 2026-09-17.

O lançamento manual preenche os mesmos dados por formulário, sem o comprovante, com a mesma matriz
de obrigatoriedade e a mesma regra de duplicidade. O estorno/ajuste também carrega todos os
blocos — ver [`lancamentos.md`](lancamentos.md), "Como o estorno/ajuste corrige".

## Blocos de dados

Quatro blocos. **Devedor é o único bloco opcional inteiro.** A obrigatoriedade dos demais campos
depende da forma de pagamento — ver [Obrigatoriedade](#obrigatoriedade).

### Recebedor

Quem recebeu o dinheiro.

| Dado | Observação |
|---|---|
| Nome | |
| CPF/CNPJ | Pessoa física ou jurídica |
| Instituição | Instituição financeira do recebedor |

### Transação

| Dado | Observação |
|---|---|
| Tipo | **Gasto ou receita.** Campo próprio do lançamento — ver "Gasto ou receita" abaixo |
| Categoria | Classificação do lançamento — ver "Categoria" abaixo |
| Descrição | **Opcional.** O PIX quase sempre vem sem mensagem; exigi-la faria o usuário digitar em quase todo lançamento |
| Valor | Valor efetivamente transacionado |
| Valor original | Valor antes de desconto, juros ou multa. **Obrigatório no boleto** (igual ao valor pago quando não há acréscimo nem desconto); opcional no PIX e no dinheiro |
| Forma de pagamento | **PIX, boleto ou dinheiro** no MVP. Define quais outros campos são obrigatórios |
| Data/hora da transação | Quando o pagamento aconteceu |
| Data de vencimento | Quando era devido |
| ID da transação | Identificador da transação no comprovante. **No boleto, é a autenticação bancária** — ver "ID da transação do boleto" abaixo |
| Código de barras | **Só boleto.** Linha digitável/código de barras do boleto pago. Guardado, mas fora da checagem de duplicidade — ver "ID da transação do boleto" abaixo |

### Devedor

**Bloco opcional.** Quem devia — nem todo comprovante distingue devedor de pagador.

| Dado | Observação |
|---|---|
| Nome | |
| CPF/CNPJ | |
| Instituição | |

### Pagador

Quem efetivamente pagou.

| Dado | Observação |
|---|---|
| Nome | |
| CPF/CNPJ | |
| Instituição | |

## Gasto ou receita

**É um campo próprio do lançamento, não algo derivado das partes.** Confirmado em 2026-09-17.
O sistema **sugere** o tipo ao ler o comprovante e o usuário **confirma na tela de aprovação**
(passo 5 de [`lancamentos.md`](lancamentos.md)), podendo corrigir antes de salvar. No lançamento
manual, o usuário escolhe.

Como o tipo fica gravado no lançamento, o relatório de gastos e lucros lê esse campo — não depende
de comparar CPF/CNPJ da `Empresa` com o das partes.

## As duas datas de negócio

O lançamento tem **data/hora da transação** (quando o dinheiro se moveu) e **data de vencimento**
(quando era devido) — além da data de criação do registro, que vem da
[entidade base](entidade-base.md) e é técnica, não de negócio. O relatório usa a **data da
transação por padrão**, e o filtro permite trocar para a data de vencimento. Confirmado em
2026-09-17 — ver [`relatorios.md`](relatorios.md).

## Categoria

Classificação do lançamento (aluguel, fornecedor, folha…), usada para agrupar no relatório.
Confirmado em 2026-09-17, **a princípio**:

- O catálogo é **misto**: uma **lista fixa do sistema**, igual para todas as redes, mais as
  categorias que cada **`GrupoEmpresa` cadastra** por conta própria.
- No relatório, agrupar por categoria é **opcional** — quem gera escolhe se quer o agrupamento.
  Ver [`relatorios.md`](relatorios.md).
- **Categoria é obrigatória** para salvar, em toda forma de pagamento (ver a matriz em
  [Obrigatoriedade](#obrigatoriedade)) — não existe lançamento "sem categoria".

**Sugestão pelo histórico do recebedor.** Confirmado em 2026-09-18. Se a `Empresa` já tem
lançamento para o **mesmo recebedor** (mesmo CPF/CNPJ), o sistema sugere a categoria usada da
última vez; o usuário confirma ou troca. Sem histórico, o campo fica em branco para o usuário
escolher. Vale para comprovante, lote e manual (assim que o CPF/CNPJ do recebedor é informado).
Motivo: pagamentos recorrentes (aluguel, fornecedor fixo, contas de consumo) são a maior parte dos
gastos, e a regra é previsível e não depende do leitor de comprovante. Descartados: sem sugestão, e
sugestão deduzida do conteúdo do comprovante (menos previsível, presa à decisão pendente #2 do hub).

**Só a Diretoria cadastra categoria da rede** (e o Administrador, pela hierarquia). Confirmado em
2026-09-18. O Gerente usa as categorias existentes, mas não cria, edita nem exclui. Não contraria
"mesma interface, diferença só de escopo de dado" ([`papeis-e-hierarquia.md`](papeis-e-hierarquia.md)):
o catálogo é dado **da rede**, fora do escopo do Gerente. Descartados: Gerente cadastrando para a
rede inteira (mexeria no que aparece em empresas que ele não vê, e o catálogo acumularia
duplicatas) e categoria por `Empresa` (terceiro nível no catálogo).

**Cada categoria é marcada como gasto, receita ou ambos.** Confirmado em 2026-09-18. Ao escolher
o tipo do lançamento, só aparecem as categorias daquele tipo e as marcadas como "ambos" (ex.:
"Aluguel", que a empresa paga e também pode receber). Consequências:

- No relatório, o agrupamento por categoria acontece **dentro de cada tipo** — uma categoria
  "ambos" aparece nos gastos e nas receitas, com os totais separados, nunca somados entre si.
- **Trocar o tipo** na tela de aprovação mantém a categoria se ela servir ao novo tipo ("ambos") e
  a limpa se não servir.
- A sugestão pelo histórico só sugere categoria compatível com o tipo do lançamento.

Descartados: lista única sem marcação (permitiria "Venda de mercadoria" num gasto) e cada categoria
pertencer a um único tipo (obrigaria duplicar "Aluguel" em gasto e receita).

**Excluir categoria em uso: some só para o que vier depois.** Confirmado em 2026-09-18. A
exclusão (lógica) tira a categoria da escolha em lançamentos novos e da sugestão pelo histórico.
Os lançamentos que já a usam **continuam com ela**, e o relatório continua agrupando-os sob esse
nome. Descartados: bloquear a exclusão enquanto houver uso (como lançamento de comprovante e
estorno não se editam, nunca mais seria possível excluir) e obrigar a remanejar para outra
categoria (reescreveria o histórico financeiro, contra a regra inegociável 4 do hub).

**Renomear categoria em uso: o nome novo vale para todo o histórico.** Confirmado em 2026-09-19.
É correção de rótulo: o lançamento continua na mesma categoria, e valor, data e classificação não
mudam, então a regra inegociável 4 não é atingida. Fica registrado quem renomeou, quando e o nome
anterior. A **marcação gasto/receita/ambos não se altera** em categoria em uso, porque isso mudaria
o relatório. Como a lista fixa do sistema é igual para todas as redes, a rede renomeia só as
categorias que ela mesma cadastrou. Descartados: proibir renomear em uso (excluir e criar outra
quebra a série histórica do relatório em duas linhas) e guardar no lançamento o nome da época
(complexo, e o relatório teria de escolher qual nome exibir).

**A Diretoria oculta categorias da lista fixa, só para a própria rede.** Confirmado em
2026-09-19. A categoria oculta sai da escolha em lançamentos novos e da sugestão pelo histórico,
como na exclusão; os lançamentos que já a usam continuam com ela e o relatório segue mostrando-a. A
Diretoria reexibe quando quiser. Para o sistema e para as outras redes nada muda. Descartados:
lista fixa sempre inteira (polui a escolha de um campo obrigatório e aumenta o erro de
classificação) e ocultar por `Empresa` (categoria por `Empresa` já foi descartada).

### Lista fixa do sistema

Confirmada em 2026-09-18. Igual para todas as redes; cada `GrupoEmpresa` acrescenta as suas.

| Categoria | Tipo |
|---|---|
| Fornecedores e mercadorias | gasto |
| Folha de pagamento | gasto |
| Pró-labore | gasto |
| Impostos e taxas | gasto |
| Água | gasto |
| Energia | gasto |
| Telefone e internet | gasto |
| Serviços de terceiros (contador, advogado…) | gasto |
| Manutenção | gasto |
| Material de consumo e escritório | gasto |
| Marketing e publicidade | gasto |
| Transporte e combustível | gasto |
| Tarifas bancárias | gasto |
| Equipamentos e investimentos | gasto |
| Outros gastos | gasto |
| Venda de mercadorias | receita |
| Prestação de serviços | receita |
| Rendimentos financeiros | receita |
| Outras receitas | receita |
| Aluguel | ambos |
| Transferência entre empresas da rede | ambos |

- **Sem categoria "Juros e multa"**, de propósito: esse valor já sai da diferença entre valor
  original e valor pago no boleto; uma categoria contaria a mesma coisa de dois jeitos.
- **"Transferência entre empresas da rede"** marca o PIX interno (gasto numa empresa, receita na
  outra), para que o relatório consolidado da rede consiga identificar esse dinheiro, que não saiu
  da rede. No consolidado, ela sai dos totais só quando as duas pontas estão na seleção — ver
  [`relatorios.md`](relatorios.md), "Transferências entre empresas da rede no consolidado".

## Obrigatoriedade

Confirmado em 2026-09-17. Dois conjuntos diferentes, que não se confundem:

### Formas de pagamento no MVP

**PIX, boleto e dinheiro.** Cartão, transferência (TED/DOC), débito automático e cheque ficam como
melhoria futura. Confirmado em 2026-09-17.

**Forma de pagamento é sempre obrigatória**, porque é ela que define o que mais é obrigatório.

**Moeda: só real, sem campo de moeda no MVP.** Confirmado em 2026-09-18. PIX, boleto e dinheiro
são em reais por natureza; moeda estrangeira só viria com cartão internacional ou remessa, fora do
MVP. Somar moedas no relatório exigiria conversão e cotação — decisão que volta junto com o cartão.

### Para salvar o lançamento

Regra geral: **tudo é obrigatório, exceto valor original (salvo no boleto), descrição e o bloco
Devedor**. Mas cada
forma de pagamento dispensa o que ela não tem — não se cobra instituição de um pagamento em
dinheiro, nem vencimento de um PIX à vista.

| Campo | PIX | Boleto | Dinheiro |
|---|---|---|---|
| Tipo (gasto/receita) | obrigatório | obrigatório | obrigatório |
| Categoria | obrigatório | obrigatório | obrigatório |
| Descrição | opcional | opcional | opcional |
| Valor | obrigatório | obrigatório | obrigatório |
| Valor original | opcional | **obrigatório** | opcional |
| Forma de pagamento | obrigatório | obrigatório | obrigatório |
| Data/hora da transação | obrigatório | obrigatório | obrigatório |
| Data de vencimento | dispensado | **obrigatório** | dispensado |
| ID da transação | obrigatório | obrigatório | dispensado |
| Código de barras | dispensado | **obrigatório** | dispensado |
| Recebedor — nome | obrigatório | obrigatório | obrigatório |
| Recebedor — CPF/CNPJ | obrigatório | obrigatório | obrigatório |
| Recebedor — instituição | obrigatório | obrigatório | dispensado |
| Pagador — nome | obrigatório | obrigatório | obrigatório |
| Pagador — CPF/CNPJ | obrigatório | obrigatório | obrigatório |
| Pagador — instituição | obrigatório | obrigatório | dispensado |
| Devedor (bloco) | opcional | opcional | opcional |

PIX e dinheiro confirmados em 2026-09-17; **boleto confirmado em 2026-09-18**, já com o código de
barras (linha acrescentada à matriz) e o valor original obrigatórios.

**Recebedor no boleto é o beneficiário** — quem emitiu a cobrança, com o banco emissor como
instituição; pagador é quem pagou, com o banco por onde pagou. Sacador/avalista (quando o boleto é
emitido por intermediário) fica **fora do MVP**. Confirmado em 2026-09-18.

**Dispensado ≠ proibido:** se o comprovante trouxer um campo dispensado (um PIX cobrança com
vencimento, por exemplo), o sistema grava o valor. Dispensado só quer dizer que a falta dele não
impede salvar.

O usuário **completa na tela de aprovação** o que o comprovante não trouxe: a confirmação fica
bloqueada até todos os obrigatórios daquela forma de pagamento estarem preenchidos. O sistema não
salva lançamento incompleto — não existe estado "pendente de revisão".

### Para chegar à tela de aprovação

O comprovante precisa ter sido lido em **pelo menos 75% dos campos obrigatórios**, arredondando o
mínimo para cima. Abaixo disso, o sistema mostra erro e não gera lançamento para aprovação — o
usuário registra por lançamento manual se quiser.

**O denominador é só o que vem do comprovante.** Tipo e categoria são decisão do usuário, não
leitura do arquivo, e por isso ficam fora da conta:

| Forma | Campos obrigatórios no comprovante | Mínimo lido |
|---|---:|---:|
| PIX | 10 | 8 |
| Boleto | 13 | 10 |
| Dinheiro | 7 | 6 |

O boleto passou de 11 para 13 campos em 2026-09-18, com a entrada do código de barras e do valor
original como obrigatórios; o mínimo subiu de 9 para 10.

Esse limiar **substitui o antigo "~50% de aproveitamento"** citado em
[`lancamentos.md`](lancamentos.md): a conta é sobre os campos obrigatórios, não sobre o
aproveitamento geral do comprovante.

### Forma de pagamento escolhida antes da análise

Confirmado em 2026-09-18. Como a forma de pagamento define o conjunto sobre o qual o limiar de 75%
é calculado, **o usuário a escolhe ao enviar o comprovante, antes da análise**: PIX, boleto,
dinheiro ou **"não sei"**.

- **Forma escolhida:** o limiar usa o conjunto dela. A forma deixa de ser leitura do arquivo, mas
  os mínimos não mudam na prática (PIX: 7 dos outros 9; boleto: 9 dos outros 12; dinheiro: 5 dos
  outros 6).
- **"Não sei":** o sistema tenta identificar a forma pelo comprovante. Se identificar, segue com
  ela. Se não, **testa os conjuntos do mais exigente para o menos exigente** — boleto (10 de 13),
  PIX (8 de 10), dinheiro (6 de 7). O primeiro que atingir 75% define a forma, que chega
  **preenchida como sugestão** na tela de aprovação, corrigível pelo usuário. Se nenhum atingir,
  o comprovante cai em erro como qualquer leitura abaixo de 75%.

**Por que do mais exigente para o menos:** os campos do dinheiro (valor, data, nomes, CPF/CNPJ)
existem também em PIX e boleto bem lidos. Testando do menos exigente, quase todo comprovante
passaria como dinheiro. Um comprovante que passa no conjunto do boleto quase certamente é boleto.

Descartados: cair em erro sempre que a forma não for lida (rígido demais), contar pelo conjunto
mais exigente (equivale a erro para quase todo PIX) e abrir a tela de aprovação com a forma em
branco (repetitivo para quem já respondeu "não sei").

### Troca de forma de pagamento na tela de aprovação

Confirmado em 2026-09-18.

- **A troca é permitida**, e os obrigatórios são recalculados na hora: trocar PIX por boleto passa
  a exigir vencimento, código de barras e valor original, e a confirmação trava até preenchê-los.
- **O limiar de 75% não é recalculado.** Ele é a porta de entrada da tela de aprovação, e o
  comprovante já passou por ela; recalcular derrubaria o usuário para o erro no meio da revisão.
- **Nada do que foi lido se perde.** Campo lido que a nova forma dispensa continua gravado
  (dispensado ≠ proibido).
- **Aviso de divergência.** Se o usuário escolheu uma forma antes da análise, mas o sistema
  encontra sinais claros de outra (código de barras num suposto PIX, por exemplo), segue com a
  escolha do usuário e **mostra um aviso** na tela de aprovação ("este comprovante parece ser um
  boleto"). Seguir calado foi descartado: forma errada salva só se corrige por estorno.

## ID da transação e duplicidade

O ID da transação identifica o pagamento **na instituição financeira**, não no DNZ Control. Por
isso ele é o que permite reconhecer que um pagamento já foi registrado.

**Pagamento repetido é recusado.** Confirmado em 2026-09-18. Se já existe lançamento com o mesmo
ID da transação, o sistema **não deixa seguir**: mostra que aquele pagamento já está registrado
(data, valor e quem registrou) e não oferece "salvar mesmo assim". O motivo: mesmo ID é o mesmo
dinheiro que se moveu uma vez só, e um segundo lançamento dobraria o valor no relatório. Avisar e
deixar o usuário decidir foi descartado — quem aprova sem ler duplicaria o valor com um clique.

Se existir caso legítimo de dois lançamentos com o mesmo ID, ele entra como **regra explícita**,
não como exceção decidida pelo usuário na hora.

**A unicidade vale por `Empresa`.** Confirmado em 2026-09-18. O mesmo ID da transação não se
repete dentro de uma `Empresa`, seja qual for o tipo (gasto ou receita). Empresas diferentes podem
ter o mesmo ID — é o caso legítimo do PIX entre duas empresas da mesma rede: gasto na que pagou,
receita na que recebeu, um lançamento em cada. A checagem nunca olha outra `Empresa`, então não
revela a quem não tem acesso (um Gerente, por exemplo) o que foi lançado em empresa alheia.

Descartados: unicidade na rede inteira (a segunda empresa não conseguiria registrar o seu lado do
PIX interno) e por `Empresa` e tipo (gasto e receita com o mesmo ID na mesma empresa seria mais
provável erro de classificação do que lançamento legítimo).

**Só lançamento vigente bloqueia o ID.** Confirmado em 2026-09-18. Lançamento excluído
(logicamente) não conta para a duplicidade: depois de excluído, o mesmo pagamento pode ser
registrado de novo. Coerente com o relatório, que já só considera lançamentos vigentes — dinheiro
excluído não está registrado. Bloquear pelo excluído foi descartado: um lançamento excluído por
engano deixaria aquele pagamento fora do relatório para sempre.

**Lançamento excluído não se reativa.** Confirmado em 2026-09-18. Primeiro decidiu-se que a
reativação não poderia furar a duplicidade; em seguida, que dado financeiro **não se reativa em
caso nenhum** ([`exclusao-logica.md`](exclusao-logica.md), "Reativação"). Excluído por engano,
registra-se de novo; o excluído fica só para rastreio e auditoria.

**A checagem acontece em dois momentos.** Confirmado em 2026-09-18.

1. **Logo após a leitura**, antes da tela de aprovação: se o ID lido já está num lançamento
   vigente da `Empresa`, o sistema recusa ali mesmo — o usuário não perde tempo revisando o que
   não vai poder salvar.
2. **De novo na confirmação**, com o ID como está no momento de salvar. É esta que garante a
   regra, porque cobre o que a primeira não vê: o ID que o parser não leu e o usuário completou à
   mão, o ID que o usuário corrigiu na tela de aprovação, e dois envios simultâneos do mesmo
   comprovante que passam juntos pela primeira checagem.

**Arquivo idêntico também é recusado, em qualquer forma de pagamento.** Confirmado em 2026-09-18.
Se o **mesmo arquivo** de comprovante já originou um lançamento vigente da mesma `Empresa`, o
sistema recusa o novo envio. Nasceu do dinheiro, que não tem ID da transação, mas vale para PIX e
boleto também — cobre o caso em que o parser não leu o ID. Segue as mesmas regras do ID: por
`Empresa`, só lançamento vigente bloqueia.

Limite conhecido e aceito: uma **foto nova do mesmo recibo** é outro arquivo para o sistema e
passa. Aí a proteção é a revisão do usuário na tela de aprovação. Comprovante que caiu em erro
(menos de 75% lido) não gerou lançamento e, portanto, não bloqueia nada.

Descartado: avisar por semelhança (mesmo valor, data e recebedor). Em dinheiro, dois pagamentos
iguais no mesmo dia são legítimos com frequência — o aviso dispararia à toa e passaria a ser
ignorado.

**Lançamento manual segue a mesma regra.** Confirmado em 2026-09-18.

- **Na criação:** lançamento manual com ID da transação que já está num lançamento vigente da
  `Empresa` é recusado. Sem isso, o manual viraria o atalho para contornar a recusa do upload.
- **Na edição** (dentro das 24 horas após a criação): trocar o ID por um que já existe na `Empresa` é
  recusado, pela mesma checagem da confirmação.

**Estorno/ajuste fica fora da checagem de ID.** Confirmado em 2026-09-18. Ele não é um pagamento
novo, é a correção de um existente, e se identifica pelo vínculo com o lançamento original. Como
excluir qualquer elo exclui a cadeia inteira, o estorno nunca sobrevive ao original — ver
[`lancamentos.md`](lancamentos.md).

### ID da transação do boleto

**É a autenticação bancária.** Confirmado em 2026-09-18. O comprovante de boleto traz três
números, e só a autenticação identifica o **pagamento** — linha digitável/código de barras e Nosso
Número identificam o **boleto** (a cobrança). A diferença importa no boleto pago duas vezes por
engano: o dinheiro saiu duas vezes e precisa aparecer duas vezes no relatório. Com a autenticação,
são dois IDs diferentes e os dois lançamentos entram; com o código de barras, o segundo seria
recusado como duplicado.

**O código de barras também é guardado.** Confirmado em 2026-09-18 como dado importante do boleto
— serve para localizar a cobrança. Fica **fora da checagem de duplicidade**, pelo motivo acima.
Linha digitável e código de barras carregam a mesma informação, então é um campo só.

**O código de barras é obrigatório no boleto.** Confirmado em 2026-09-18. Custo aceito: quando o
parser não o lê, e no lançamento manual de boleto, o usuário digita 47 ou 48 dígitos. Na prática
quase todo comprovante de boleto o imprime.

### Valor original no boleto

**Sempre obrigatório no boleto.** Confirmado em 2026-09-18. Quando não houve juros, multa nem
desconto, ele é igual ao valor pago. O motivo é o relatório poder mostrar **quanto a empresa pagou
de juros e multa por atraso** (e quanto economizou em desconto) — o que só funciona se o dado
estiver sempre lá. Descartado: obrigatório só quando houvesse acréscimo ou desconto — regra
condicional que mudaria o número de campos obrigatórios de um boleto para outro, com o mesmo
resultado.

### Comprovante em lote

**Um comprovante pode gerar vários lançamentos**, no caso do comprovante com vários pagamentos
(o pagamento em lote do internet banking empresarial, por exemplo). Confirmado em 2026-09-18.

- **Declaração explícita.** O usuário declara, ao enviar, que o comprovante é de lote. O sistema
  não tenta descobrir sozinho — sem a declaração, o comprovante é tratado como de um pagamento só.
- **Pai e filhos.** Um registro **pai** guarda o arquivo e funciona como **guarda-chuva** do
  lote; cada pagamento é um lançamento **filho**, que referencia o pai. (Na primeira formulação o
  pai era "um dos lançamentos"; o refinamento abaixo o tornou um registro próprio, sem valor.)

**O pai se chama `LoteLancamentos`.** Confirmado em 2026-09-18. Nome próprio, e não "lançamento
pai", para que ninguém o some no relatório nem aplique a ele as regras de lançamento (edição em 1
dia, estorno). Os filhos são lançamentos comuns que **pertencem a um `LoteLancamentos`**. Nesta
seção, "pai" é só apelido do `LoteLancamentos`.

**O pai é só guarda-chuva, sem valor.** Confirmado em 2026-09-18. Ele guarda o arquivo e agrupa
o lote, mas **não entra no relatório**: não tem valor, tipo nem partes. **Cada pagamento do lote é
um filho**, lançamento normal com os seus dados. Um lote de três boletos (R$ 100, R$ 200, R$ 300)
vira um pai e três filhos, e o relatório soma R$ 600. Descartados: o pai ser o primeiro pagamento
(prenderia o arquivo e o lote a um pagamento que pode ser estornado ou excluído) e o pai carregar o
valor total (risco de contar o dinheiro duas vezes).

**Limiar de 75% por pagamento.** Confirmado em 2026-09-18. Cada pagamento do lote passa pela
mesma regra do comprovante individual: o que atingir 75% dos obrigatórios vai para a aprovação; o
que não atingir **não gera lançamento** e aparece como "não foi possível ler" — o usuário registra
por lançamento manual se quiser. A tela mostra o placar ("5 pagamentos encontrados, 4 lidos"), para
nenhum pagamento sumir sem o usuário perceber. Descartados: tudo ou nada (jogaria fora os bons por
causa de um ruim) e limiar pela média do lote (deixaria passar a leitura ruim que o limiar barra).

**Aprovação do lote numa tela só, confirmada em conjunto.** Confirmado em 2026-09-18. Os
pagamentos lidos aparecem numa lista; o usuário abre qualquer um para revisar, editar e completar,
e pode **descartar** item (um pagamento que não é desta `Empresa`, por exemplo). Um único botão
confirma o lote, liberado só quando todos os itens mantidos estão completos, e **salva tudo ou
nada**. Descartado: aprovar um por um salvando a cada confirmação — um lote interrompido no meio
ficaria pela metade, e o reenvio seria recusado pelo arquivo idêntico, sem saída para o usuário.
Risco aceito: um botão para tudo facilita aprovar sem abrir cada item; a proteção segue sendo o
estorno.

**Formas misturadas no mesmo lote: sim, cada item tem a sua.** Confirmado em 2026-09-18. As
regras do comprovante individual valem item a item:

- forma declarada ao enviar vale para todos os itens;
- com **"não sei"**, o sistema identifica a forma de cada item, testando do conjunto mais exigente
  para o menos;
- na tela de aprovação, o usuário troca a forma item a item, com a mesma regra de troca;
- item de forma fora do MVP (TED, cartão) aparece como **"forma não suportada"**, junto dos não
  lidos, e o usuário lança à mão se quiser.

Descartado: lote de uma forma só — lotes reais do internet banking são mistos.

**Duplicidade no lote: checagem por item.** Confirmado em 2026-09-18. Item cujo ID da transação
já está num lançamento vigente da `Empresa` aparece como **"já registrado"** (com o lançamento
existente), junto dos não lidos e dos não suportados, e não gera lançamento; os demais seguem. O
placar mostra tudo ("5 encontrados, 4 lidos, 1 já registrado"). Descartado: um item repetido
derrubar o lote inteiro.

O arquivo fica com o pai, então **reenviar o mesmo PDF de lote é recusado inteiro** enquanto o lote
estiver vigente. Consequência aceita: item descartado na aprovação ou não lido não se recupera
reenviando o lote — só por lançamento manual, e a checagem de ID segue protegendo.

**Exclusão no lote.** Confirmado em 2026-09-18. Toda exclusão aqui é **lógica**, como em todo o
sistema ([`exclusao-logica.md`](exclusao-logica.md)) — nada é apagado fisicamente.

- **Filho** é lançamento comum: estorno e exclusão seguem as regras de sempre, sem afetar os
  irmãos.
- **Excluir o pai exclui (logicamente) todos os filhos vigentes de uma vez**, após confirmação
  explícita ("isto exclui 15 lançamentos"). Serve para o lote registrado na `Empresa` errada.
  Estorno/ajuste de um filho pertence ao lote, então a exclusão leva a cadeia inteira (ver
  [`lancamentos.md`](lancamentos.md), "Como o estorno/ajuste corrige").
- Se os filhos forem excluídos um a um até não sobrar nenhum vigente, **o pai fica inativo
  sozinho**.
- Pai inativo **libera o arquivo**: o mesmo PDF pode ser reenviado (na `Empresa` certa, por
  exemplo), já que só registro vigente bloqueia duplicidade.

Descartado: pai só inativar depois do último filho, obrigando a excluir um a um.

**"Aplicar tipo e categoria a todos".** Confirmado em 2026-09-18. A tela de aprovação do lote
permite escolher tipo e categoria uma vez e aplicar a todos os itens, mantendo a troca item a item
antes de confirmar. Tipo e categoria seguem sendo escolha do usuário; o risco de aplicar a todos e
não notar a exceção é o mesmo, já aceito, da aprovação conjunta.

### Resumo da regra de duplicidade

Um lançamento é recusado quando, na mesma `Empresa`, já existe lançamento **vigente** com o
**mesmo ID da transação** ou originado do **mesmo arquivo** de comprovante. Vale para comprovante e
para manual (criação e edição); não vale para estorno/ajuste. A checagem ocorre após a leitura do
comprovante e de novo na confirmação. Lançamento excluído não bloqueia e não é reativável.

Excluir um lançamento exclui a **cadeia inteira** (original e estornos), para o estorno — que está
fora da checagem de ID — não sobreviver ao original e duplicar o pagamento num reenvio. Ver
[`lancamentos.md`](lancamentos.md).

No **comprovante em lote**, o ID é checado item a item (o repetido aparece como "já registrado" e
não derruba os demais), e o arquivo pertence ao pai: reenviar o mesmo PDF é recusado enquanto o
lote estiver vigente.

## Em aberto — a aprofundar depois

Nada em aberto desde 2026-09-19.

## Impacto (a confirmar quando o modelo fechar)

- `dnzcontrol-api` — modelagem do lançamento e dos três blocos de partes (recebedor, devedor,
  pagador). Parte ou partes viram entidade própria reaproveitável, ou ficam embutidas no
  lançamento? Decidir quando a modelagem for feita.
- `dnzcontrol-api` — catálogo de categorias com duas procedências (lista do sistema + categorias
  do `GrupoEmpresa`) e relatório que filtra por data de transação ou de vencimento e agrupa por
  categoria sob demanda.
- `dnzcontrol-api` — o que o leitor de comprovante precisa extrair. É esta lista que define o
  contrato da decisão pendente #2 do hub (mecanismo de análise de comprovante).
- `superdiniz-hub/regras/seguranca.md` — CPF/CNPJ e nome de terceiros são dado pessoal (LGPD).
  A regra 2 (nunca logar) precisa citá-los explicitamente quando este modelo fechar.
- `dnzcontrol-app` — formulário de lançamento manual e tela de aprovação do comprovante seguem
  esta mesma estrutura de blocos, com escolha de tipo (gasto/receita) e de categoria.
- `dnzcontrol-api` — regra de duplicidade: unicidade de ID da transação e de arquivo por `Empresa`
  entre lançamentos vigentes, exceto estorno/ajuste; checagem após a leitura e na confirmação (e na
  edição do manual), resistente a envios simultâneos. Dado financeiro não tem reativação.
- `dnzcontrol-api` — `LoteLancamentos` (sem valor, dono do arquivo) com lançamentos filhos; limiar
  e duplicidade por item; confirmação do lote transacional (tudo ou nada); exclusão em cascata.
- `dnzcontrol-api` — forma de pagamento informada no envio (PIX, boleto, dinheiro, "não sei") e
  identificação do "não sei" testando do conjunto mais exigente para o menos.
- `dnzcontrol-api` — categoria com marcação de tipo (gasto, receita, ambos), procedência (sistema
  ou `GrupoEmpresa`), cadastro restrito à Diretoria e sugestão pelo último uso do mesmo recebedor
  na `Empresa`.
- `dnzcontrol-app` — escolha da forma de pagamento (e de "é lote") no envio; tela de aprovação do
  lote com placar (lidos, não lidos, não suportados, já registrados), descarte de item e "aplicar
  tipo e categoria a todos"; aviso de divergência de forma.
- `superdiniz-hub/regras/seguranca.md` — a regra inegociável 5 (upload idempotente) passa a ter
  critério de negócio: é esta regra de duplicidade que define "mesmo comprovante".
- `dnzcontrol-app` — mensagem de recusa por duplicidade mostra o lançamento existente (data, valor
  e quem registrou), tanto logo após a leitura quanto na confirmação.
