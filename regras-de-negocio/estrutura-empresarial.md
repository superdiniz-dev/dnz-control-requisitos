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

## Cadastro / provisionamento

- Um `GrupoEmpresa` novo, junto com seu **primeiro usuário Diretoria**, só é criado pelo
  **Administrador**. Não é self-service.
- Papéis externos **não podem** cadastrar `GrupoEmpresa` — **nem Diretoria**. Confirmado (e
  reconfirmado) em 2026-09-11: só o Administrador cria `GrupoEmpresa`, sem exceção.
- A partir do momento em que existe ao menos uma Diretoria na rede, **essa Diretoria pode cadastrar
  outras Diretorias e Gerentes** dentro do mesmo `GrupoEmpresa`.

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

## Relação com multi-tenancy

Isso é o desenho concreto da decisão de multi-tenancy que estava em aberto no hub
(`decisoes-pendentes.md` #3): o tenant não é a `Empresa` isolada, é o `GrupoEmpresa` — cada rede é
o limite de isolamento de dados **e** de configuração white-label. "Superdiniz" (citado no hub
como tenant de exemplo) é, neste modelo, um `GrupoEmpresa`.

## Em aberto — a aprofundar depois

- Existe alguma **operação** no nível do `GrupoEmpresa`, além do catálogo de categorias da rede?
  (Relatório já respondido em 2026-09-18: pode consolidar qualquer conjunto de empresas do escopo
  de quem gera, inclusive a rede inteira — ver [`relatorios.md`](relatorios.md).)
- Parâmetros **funcionais** por `GrupoEmpresa` (funcionalidades habilitadas/desabilitadas?) —
  nada detalhado ainda. A parte visual está acima.
- App próprio na loja: publicado na conta de desenvolvedor do DNZ Control ou na da própria rede?
  Toda rede ganha app próprio, ou só algumas, com as demais usando um app padrão DNZ Control?
- Domínio próprio: obrigatório, ou a rede pode usar um endereço padrão do DNZ Control?

## Impacto (a confirmar quando o modelo fechar)

- `dnzcontrol-api` — entidades `GrupoEmpresa` e `Empresa`, atribuição Gerente↔Empresa,
  autorização por rede, armazenamento da configuração visual/funcional por `GrupoEmpresa`.
- `dnzcontrol-app` — precisa carregar configuração visual/funcional a partir do `GrupoEmpresa` do
  usuário logado.
- `superdiniz-hub/regras/banco-de-dados.md` — modelagem de multi-tenancy por `GrupoEmpresa`.
- `superdiniz-hub/contexto/arquitetura.md` — mecanismo de white-label (hoje não detalhado no hub).
- `superdiniz-hub/contexto/decisoes-pendentes.md` #3 — quase toda respondida: desenho de tenant
  (`GrupoEmpresa` → `Empresa`), vínculo fixo, cadastro restrito ao Administrador, e onde mora a
  configuração white-label. Só os pontos "em aberto" acima seguem sem fechar.
