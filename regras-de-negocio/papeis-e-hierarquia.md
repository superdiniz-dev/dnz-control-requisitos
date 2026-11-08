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
- **Um usuário externo (Gerente ou Diretoria) pode pertencer a mais de um `GrupoEmpresa`.**
  Revisto em 2026-11-06; antes, em 2026-09-11, tinha se confirmado "exatamente um". Ver
  "Login e identificação", abaixo, e [`estrutura-empresarial.md`](estrutura-empresarial.md).
- **A hierarquia só vale entre papéis diferentes.** Entre usuários do **mesmo papel** (ex.: duas
  Diretorias da mesma rede), um pode **editar** o outro, mas **nenhum pode excluir o outro**.
  Confirmado em 2026-09-11.
- **Toda exclusão de usuário é lógica, nunca física** — ver
  [`exclusao-logica.md`](exclusao-logica.md).
- **A princípio, Gerente e Diretoria usam a mesma interface** — mesmas telas/menus. A diferença
  entre os dois papéis é só **escopo de dado** (quais `Empresa` cada um enxerga), não telas ou
  opções exclusivas de um papel. Confirmado em 2026-09-11; não fechado como definitivo. Aplicação
  em 2026-09-18: **só a Diretoria cadastra categoria da rede**, porque o catálogo é dado do
  `GrupoEmpresa`, fora do escopo do Gerente — ver
  [`dados-do-lancamento.md`](dados-do-lancamento.md), "Categoria".

## Login e identificação

**O usuário entra com e-mail e senha, e o e-mail é único no sistema inteiro.** Confirmado em
2026-11-06. Uma pessoa é um usuário só, com uma senha só, mesmo quando participa de mais de uma
rede. Descartados: e-mail único só dentro da rede (o login teria de pedir a rede antes do e-mail,
complicando o caso base de empresa única) e outro identificador (CPF, nome de usuário).

**Quem participa de mais de uma rede escolhe a rede na hora do login.** Confirmado em 2026-11-06.
Depois de entrar com e-mail e senha, o usuário escolhe em qual `GrupoEmpresa` vai trabalhar. **Com
uma rede só, a escolha é pulada** e o usuário entra direto nela, sem ver a ideia de rede (ver
[`estrutura-empresarial.md`](estrutura-empresarial.md), "Caso base").

**Papel externo é por rede; papel interno é da pessoa.** Confirmado em 2026-11-06. Cada vínculo da
pessoa com um `GrupoEmpresa` tem o próprio papel externo (Diretoria ou Gerente) e, no Gerente, as
próprias `Empresa` atribuídas. Quem define é a Diretoria daquela rede, sem efeito nenhum nas
outras: a Ana pode ser Diretoria na rede X e Gerente na rede Y. O papel interno (Administrador) não
pertence a rede nenhuma e vale para a pessoa no sistema todo. Descartado: papel externo único da
pessoa, valendo em todas as redes (a Diretoria de uma rede mudaria o que a pessoa faz em outra,
furando o isolamento entre redes).

**Excluir o usuário numa rede exclui só o vínculo dele com aquela rede.** Confirmado em 2026-11-06.
Se a Ana é Diretoria em X e Gerente em Y e a Diretoria de Y a exclui, ela perde na hora o acesso a Y
e continua entrando em X. **Sem nenhum vínculo ativo, o login é recusado, e o usuário de papel externo que fica com zero vínculos ativos passa a inativo** (confirmado em 2026-11-08). Excluir a **pessoa**,
em todas as redes de uma vez, é só do Administrador. A reativação segue a simetria de
[`exclusao-logica.md`](exclusao-logica.md): quem excluiu o vínculo pode reativá-lo. Descartado:
excluir numa rede derrubar o login inteiro (a Diretoria de uma rede tiraria o acesso a outra).

**O papel interno não depende de vínculo: o Administrador tem acesso a todas as redes.**
Confirmado em 2026-11-06. Ele nunca cai na recusa por "nenhum vínculo ativo"; o que tira o acesso
dele é a exclusão da própria pessoa.

**Depois do login, o Administrador escolhe a rede como os demais, só que entre todas.** Confirmado
em 2026-11-06. Não existe, no app do MVP, uma área do sistema acima das redes: a visão de cima (lista
de redes, criação de rede e configurações de sistema) fica num **painel administrativo separado,
fora do MVP**. Descartado para o MVP: uma área do sistema dentro do próprio app.

**Quem é Administrador e também tem vínculo com uma rede escolhe, no login, como vai atuar.**
Confirmado em 2026-11-08. A mesma pessoa, com o mesmo e-mail, pode ter o papel interno e vínculo
externo. Ao entrar, ela escolhe atuar como Administrador ou com o papel do vínculo naquela rede, e
o histórico registra em qual dos dois ela agiu. Descartados: proibir o vínculo (a pessoa precisaria
de um segundo usuário, com outro e-mail) e guardar o vínculo com o acesso de Administrador sempre
prevalecendo (o vínculo não mudaria nada no acesso).

**No MVP, criar uma rede e alterar as marcações dela é operação interna, sem tela.** Confirmado em
2026-11-08. Quem opera o sistema cria o `GrupoEmpresa` (com a primeira Diretoria e, na rede de
empresa única, com a empresa principal) e altera as marcações da rede (empresa única, idioma e
domínio próprio) por um comando próprio. O comando aplica as mesmas regras de negócio, como recusar
a marcação de empresa única com mais de uma empresa ativa, e registra quem fez. Quando o número de
redes justificar, o painel administrativo assume essas operações. Descartados para o MVP: tela de
marcações dentro do app, usada só pelo Administrador, e painel mínimo separado.

**Para mudar de rede, a pessoa sai e entra de novo.** Confirmado em 2026-11-08. Não existe troca de
rede dentro do app: a rede é escolhida só no login, inclusive pelo Administrador, e o mesmo vale
para quem é Administrador e tem vínculo mudar a forma de atuar. Descartado: opção "Trocar de rede"
no menu, sem pedir a senha de novo.

**A entrada numa rede é sempre por convite, igual para quem é novo e para quem já tem usuário.**
Confirmado em 2026-11-08. Quem cadastra vê sempre "convite enviado", e o vínculo só passa a valer
quando a pessoa aceita. A pessoa nova define a senha ao aceitar; quem já tem usuário aceita e
continua com a senha atual. Só o e-mail recebido é diferente: **a lista de usuários da rede mostra
o convite pendente da mesma forma nos dois casos**, para nunca revelar que a pessoa participa de
outra rede. Descartados: vincular direto quem já tem usuário (a diferença entre "ativo" e
"pendente" revelaria a outra rede, e a pessoa constaria numa rede sem ter concordado) e recusar o
e-mail já existente (revela o usuário e impede a mesma pessoa em mais de uma rede).

**Nome e e-mail são da pessoa: só ela e o Administrador alteram.** Confirmado em 2026-11-08. A regra
é igual para todos, tenha a pessoa uma rede ou várias, para nunca revelar que ela participa de outra
rede. A Diretoria continua definindo o papel e as empresas atribuídas, e pode excluir o vínculo, mas
não altera nome nem e-mail; e-mail digitado errado se corrige cancelando o convite pendente e
enviando outro. A troca de e-mail só vale depois de confirmada no endereço novo. Descartados: a
Diretoria alterar valendo em todas as redes (uma rede mudaria o login usado em outra e poderia tomar
a conta) e nome separado por rede (a mesma pessoa apareceria com nomes diferentes, para pouco ganho).

**Quem ficou inativo por chegar a zero vínculos volta sozinho ao ter um vínculo ativo.** Confirmado
em 2026-11-08. A pessoa volta a ativa quando um vínculo é reativado ou quando ela aceita um convite,
mantendo a senha e o histórico. **A exclusão da pessoa pelo Administrador é diferente:** só ele a
desfaz, e convite não a reativa; quem convidou vê "convite enviado", como em qualquer caso.
Descartado: só o Administrador reativar (cada readmissão dependeria de operação interna e
contrariaria "quem pode excluir, pode reativar").

**No domínio próprio, o endereço já define a rede.** Confirmado em 2026-11-08. Quem entra pelo
domínio próprio de uma rede vai direto para ela, sem escolha de rede; sem vínculo ativo com ela, o
login é recusado. O Administrador entra nessa rede, e quem é Administrador e tem vínculo com ela
ainda escolhe como atuar. Para ir a outra rede, a pessoa usa o endereço padrão. O domínio de uma
rede nunca mostra outra. Descartado: a mesma escolha do endereço padrão (o domínio de uma rede
exibiria o nome das outras, e a marca mudaria no meio do login).

**Várias redes em produção ficam fora do MVP, mas a base já nasce pronta para elas.** Confirmado em
2026-11-08. O MVP opera com uma rede só. O cadastro de pessoa, rede e vínculo já segue as regras
desta seção, para que abrir várias redes depois do MVP exija o mínimo de mudança e, se possível,
nenhuma alteração nas entidades.

**O Administrador, a princípio, só é excluído direto no banco**, por quem opera o sistema. Não
existe tela nem endpoint para isso no MVP. Confirmado em 2026-11-06.

## Em aberto — a aprofundar depois

- Se e quando surgem outros papéis, dentro de cada categoria.

## Impacto (a confirmar quando o modelo fechar)

- `dnzcontrol-api` — modelagem de papel/hierarquia, autorização em toda mutação/leitura de recurso
  (regra inegociável 1 do hub).
- `superdiniz-hub/regras/seguranca.md` e `regras/banco-de-dados.md` — hoje ainda não citam papel;
  migram quando este modelo fechar.
- **Revisão de 2026-11-06 (usuário em mais de uma rede):** o hub ainda descreve "usuário externo em
  um só grupo" em `superdiniz-hub/regras/seguranca.md` (seção de isolamento),
  `docs/adr/0006-isolamento-entre-grupos-por-linha.md` (contexto) e `contexto/decisoes-pendentes.md`
  (tabela "Respondidas"). Na API, o grupo deixa de sair só da identidade e passa a sair da rede
  escolhida no login, sempre conferida contra os vínculos do usuário, nunca aceita do app sem essa
  conferência. Tradução técnica no ADR 0010 do hub, aceito em 2026-11-08.
- `superdiniz-hub/contexto/decisoes-pendentes.md` #10 — parcialmente respondida (existe papel
  administrativo distinto do operador, com acesso irrestrito a princípio), mas segue aberta até o
  restante da granularidade e a lista de papéis fechar.
- `superdiniz-hub/regras/seguranca.md` — regra inegociável 2 (nunca logar segredo/comprovante) e o
  acesso irrestrito do Administrador não são a mesma coisa: acesso amplo no domínio não abre
  exceção para logar dado sensível. Confirmar isso explicitamente quando a regra migrar para o hub.
