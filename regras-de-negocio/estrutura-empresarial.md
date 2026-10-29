# Estrutura empresarial — GrupoEmpresa e Empresa

<!-- RASCUNHO — brainstorm em andamento com o desenvolvedor (2026-09-11). -->

## Modelo

- **`GrupoEmpresa`** — a rede. Contém uma ou mais `Empresa`.
- **`Empresa`** — uma empresa individual dentro da rede; é onde vivem os lançamentos, comprovantes
  e relatórios financeiros.
- Um `GrupoEmpresa` tem um proprietário: o papel **Diretoria** — ver
  [`papeis-e-hierarquia.md`](papeis-e-hierarquia.md).
- O papel **Gerente** é **atribuído** a uma ou mais `Empresa` específicas dentro da rede — só
  enxerga as empresas às quais foi atribuído, não a rede inteira.
- A **Diretoria** enxerga **todas** as `Empresa` da sua rede automaticamente, sem precisar de
  atribuição empresa a empresa.
- **Cada usuário externo pertence a exatamente um `GrupoEmpresa`** — não há Gerente nem Diretoria
  compartilhado entre redes diferentes. Confirmado em 2026-09-11.
- **`Empresa` não migra de `GrupoEmpresa`** — o vínculo é fixo desde a criação. Confirmado em
  2026-09-11.
- **Toda `Empresa` tem CNPJ, obrigatório e único dentro da rede.** Confirmado em 2026-09-18. É por
  ele que o relatório consolidado reconhece uma transferência interna — ver
  [`relatorios.md`](relatorios.md), "Transferências entre empresas da rede no consolidado". A
  unicidade vale **só dentro do `GrupoEmpresa`**: a checagem nunca revela que um CNPJ já existe em
  outra rede. Matriz e filial têm CNPJs diferentes e podem ser `Empresa` distintas. Descartados:
  único no sistema inteiro (a recusa vazaria que o CNPJ é cliente de outra rede) e opcional (a
  transferência de uma empresa sem CNPJ nunca seria reconhecida como interna).
- **A unicidade conta também as `Empresa` inativas.** Confirmado em 2026-09-18. O CNPJ de uma
  empresa excluída continua bloqueado na rede; quem quer o CNPJ de volta **reativa** a empresa,
  que retorna com o próprio histórico (ver [`exclusao-logica.md`](exclusao-logica.md),
  "Reativação"). Ao tentar cadastrar um CNPJ de empresa inativa, o sistema avisa e, para quem pode
  reativar, oferece a reativação ali mesmo. Descartado: liberar o CNPJ da excluída (o histórico
  ficaria dividido entre duas empresas com o mesmo CNPJ, e a transferência interna poderia casar
  com a empresa errada).

## Cadastro / provisionamento

- Um `GrupoEmpresa` novo, junto com seu **primeiro usuário Diretoria**, só é criado pelo
  **Administrador**. Não é self-service.
- Papéis externos **não podem** cadastrar `GrupoEmpresa` — **nem Diretoria**. Confirmado (e
  reconfirmado) em 2026-09-11: só o Administrador cria `GrupoEmpresa`, sem exceção.
- A partir do momento em que existe ao menos uma Diretoria na rede, **essa Diretoria pode cadastrar
  outras Diretorias e Gerentes** dentro do mesmo `GrupoEmpresa`.
- **A Diretoria cadastra as `Empresa` da rede, desde a primeira.** Confirmado em 2026-09-18. O
  Administrador cria o `GrupoEmpresa` e a primeira Diretoria; as empresas vêm depois, pela
  Diretoria. O Gerente não cadastra empresa.
- **Exceção: rede de empresa única.** Quando o `GrupoEmpresa` é de uma empresa só (o caso base,
  abaixo), **a empresa principal já vem cadastrada pelo Administrador**, junto com o grupo e a
  primeira Diretoria. Confirmado em 2026-09-18.

- **A `Empresa` pode informar o regime tributário** (MEI, Simples Nacional, Lucro Presumido ou
  Lucro Real). Confirmado em 2026-09-18. É por ele que o sistema liga as cobranças federais padrão
  da empresa (ver [`notificacoes.md`](notificacoes.md), "Cobranças federais padrão"). **O regime é
  opcional:** sem ele, a empresa não recebe cobrança federal padrão nenhuma, só as que forem
  ligadas à mão do catálogo. Descartado: regime obrigatório no cadastro.
- **Só a Diretoria altera o regime tributário**, por ser dado cadastral da empresa. Confirmado em
  2026-09-18. Quando o regime muda, ou é informado pela primeira vez, **as cobranças federais da
  empresa voltam ao padrão do novo regime**, e os ajustes manuais somem. O sistema avisa isso
  antes de confirmar. Descartados: manter os ajustes manuais por cima (poderia deixar ligada uma
  cobrança do regime antigo) e o Gerente também alterar o regime.

## Caso base: rede de uma empresa só

**O caso base é um `GrupoEmpresa` com uma única `Empresa`.** Confirmado em 2026-09-18. O modelo é
pensado para redes com várias empresas (white-label expansivo), mas o primeiro cliente, a empresa
principal, é uma empresa só, e o app **tem de funcionar bem nesse caso**, sem obrigar quem usa a
lidar com a ideia de rede quando ela tem uma única empresa.

**"Empresa única" é uma marcação do `GrupoEmpresa`, e só o Administrador a altera.** Confirmado em
2026-09-18 (revisto na mesma sessão: antes se tinha decidido que a marcação cairia sozinha quando
a Diretoria cadastrasse a segunda empresa). O Administrador cria o grupo marcado como de empresa
única, já com a empresa principal. **Enquanto o grupo está marcado, a Diretoria não cadastra outra
empresa**; para a rede crescer, o Administrador desmarca, e a partir daí a Diretoria cadastra as
demais. Voltar a marcar também é só com o Administrador; nada muda a marcação sozinho, nem a
exclusão de empresas. **Marcar exige exatamente uma empresa ativa**: com mais de uma, o sistema
recusa e as outras precisam ser excluídas antes. Assim a marcação nunca esconde empresa que ainda
recebe lançamentos.

**Com o grupo marcado, o app esconde tudo o que é de rede.** Confirmado em 2026-09-18. Não há
seleção de empresa (a empresa única fica implícita em lançamento, relatório e painel), nem opção de
consolidado, nem cadastro de empresa, e a palavra "rede" não aparece: para quem usa, o app é o app
da empresa. Descartado: mostrar a interface de rede com a empresa única pré-selecionada (opções
inúteis à vista). Descartados: sem marcação, derivado só da contagem de empresas, e marcação
que cai sozinha quando a Diretoria cadastra a segunda empresa.

## Configuração visual e funcional (white-label)

**O `GrupoEmpresa` é a fonte dos parâmetros de configuração visual e funcional do app** — cada
rede pode ter sua própria aparência/comportamento no app, configurado no nível do grupo (não por
`Empresa` individual). Confirmado em 2026-09-11. Isso é o mecanismo concreto do white-label
citado no hub (`contexto/produto.md`): "Superdiniz" é um `GrupoEmpresa`, e a configuração
visual/funcional dele é o que muda de uma rede para outra.

A personalização por `GrupoEmpresa` vai além de um tema dentro de um app compartilhado. Confirmado
em 2026-09-17, a rede pode ter:

- **Logo e cores** próprios no app.
- **App próprio publicado na loja**, com nome e ícone da rede.
- **Domínio próprio na web** (ex.: `financeiro.superdiniz.com.br`), com a marca da rede.

**App próprio na loja é opcional; existe um app padrão.** Confirmado em 2026-09-18. Há um app
padrão DNZ Control na loja: a rede entra nele e, depois do login, vê a própria marca (logo e
cores). O app próprio, com nome e ícone da rede, é uma opção ligada pelo Administrador para quem
quiser. Motivo: publicar e manter um app por cliente na loja custa revisão da Apple e do Google,
atualizações e contas. Descartados: app próprio obrigatório para toda rede e só o app da empresa
principal no MVP.

**No MVP só existe o app padrão**, e a empresa principal (o caso base) usa esse app com a marca
dela. Confirmado em 2026-09-18. O app próprio por rede vem depois do MVP, quando existir o fluxo de
publicação por rede. Descartado: a empresa principal já nascer com app próprio.

**O app próprio é publicado na conta de desenvolvedor do DNZ Control.** Confirmado em 2026-09-18.
O DNZ Control publica e mantém todos os apps próprios na conta dele, sem exigir conta do cliente.
Descartado: publicar na conta da própria rede. **Risco a verificar quando o app próprio entrar:**
as regras da App Store contra apps gerados por modelo ou por serviço de white-label (diretriz
4.2.6) podem exigir que o app seja publicado pela conta do dono da marca. Se a Apple recusar, esta
decisão volta à mesa.

**Domínio próprio na web é opcional; existe um endereço padrão.** Confirmado em 2026-09-18. Há um
endereço padrão DNZ Control, em que a rede vê a própria marca depois do login, como no app padrão.
O domínio próprio (ex.: `financeiro.superdiniz.com.br`) é uma opção ligada pelo Administrador, já
disponível no MVP: na web, custa bem menos que um app na loja. Descartados: domínio obrigatório
(o cadastro da rede dependeria de o cliente ter e configurar um domínio) e domínio só depois do
MVP.

**Parâmetros funcionais: o mecanismo nasce pronto, com um parâmetro só.** Confirmado em
2026-09-18. O `GrupoEmpresa` tem um conjunto de parâmetros funcionais (funcionalidades ligadas ou
desligadas por rede), preparado para crescer. No MVP, o único parâmetro é a **marcação de empresa
única** (ver "Caso base", abaixo). Os demais entram à medida que aparecerem, cada um como decisão
própria registrada aqui.

**O idioma é definido pela rede, com português do Brasil como padrão.** Confirmado em 2026-10-29.
Todo usuário da rede vê o app e as mensagens do sistema no idioma do `GrupoEmpresa`. Não existe
escolha de idioma por usuário. **Só o Administrador define e altera o idioma**: ele escolhe ao
criar o grupo (o padrão é português do Brasil) e é o único que muda depois, como a marcação de
empresa única. Hoje só existe português do Brasil, então na prática nada muda até haver um
segundo idioma. Descartados: idioma pelo aparelho ou navegador de cada usuário; preferência salva
por usuário; e a Diretoria também poder trocar o idioma.

## Relação com multi-tenancy

Isso é o desenho concreto da decisão de multi-tenancy que estava em aberto no hub
(`decisoes-pendentes.md` #3): o tenant não é a `Empresa` isolada, é o `GrupoEmpresa` — cada rede é
o limite de isolamento de dados **e** de configuração white-label. "Superdiniz" (citado no hub
como tenant de exemplo) é, neste modelo, um `GrupoEmpresa`.

## Operações no nível da rede

Levantadas em 2026-09-18: cadastro de usuários da rede, cadastro das `Empresa`, catálogo de
categorias, configuração white-label, relatório consolidado e consulta ao registro de geração de
relatório. Depois entrou a antecedência do aviso de pagamento recorrente, configurável por rede
(ver [`notificacoes.md`](notificacoes.md)). Na mesma resposta entraram o painel inicial ([`painel-inicial.md`](painel-inicial.md))
e as notificações ([`notificacoes.md`](notificacoes.md)), cujo escopo (rede ou empresa) ainda
está em aberto nesses arquivos.

## Em aberto — a aprofundar depois

Nada em aberto por ora.

## Impacto (a confirmar quando o modelo fechar)

- `dnzcontrol-api` — entidades `GrupoEmpresa` e `Empresa`, atribuição Gerente↔Empresa,
  autorização por rede, armazenamento da configuração visual/funcional por `GrupoEmpresa`. CNPJ
  obrigatório na `Empresa`, com unicidade por `GrupoEmpresa` (não global), contando as inativas.
  Mecanismo extensível de parâmetros funcionais por `GrupoEmpresa`, com um só parâmetro no MVP: a
  marcação de empresa única, alterável só pelo Administrador; com ela ligada, o
  cadastro de `Empresa` pela Diretoria é recusado; criação do grupo pelo Administrador com a empresa
  principal quando marcado.
- `dnzcontrol-app` — precisa carregar configuração visual/funcional a partir do `GrupoEmpresa` do
  usuário logado, inclusive o idioma.
- `dnzcontrol-api` — idioma do `GrupoEmpresa` (padrão português do Brasil, alterável só pelo
  Administrador) define o idioma das mensagens devolvidas ao usuário da rede.
- `superdiniz-hub/regras/banco-de-dados.md` — modelagem de multi-tenancy por `GrupoEmpresa`.
- `superdiniz-hub/contexto/arquitetura.md` — mecanismo de white-label (hoje não detalhado no hub).
- `superdiniz-hub/contexto/decisoes-pendentes.md` #3 — quase toda respondida: desenho de tenant
  (`GrupoEmpresa` → `Empresa`), vínculo fixo, cadastro restrito ao Administrador, e onde mora a
  configuração white-label. Só os pontos "em aberto" acima seguem sem fechar.
