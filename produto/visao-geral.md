# Visão geral do produto

<!-- RASCUNHO — brainstorm em andamento com o desenvolvedor. -->

## Quem usa — papéis

Ponto 1 do brainstorm (2026-09-11). Aprofundado em
[`../regras-de-negocio/papeis-e-hierarquia.md`](../regras-de-negocio/papeis-e-hierarquia.md) e
[`../regras-de-negocio/estrutura-empresarial.md`](../regras-de-negocio/estrutura-empresarial.md).

| Papel | Natureza | Quem é |
|---|---|---|
| **Administrador** | Interno | Administra o sistema DNZ Control em si. Externo a qualquer `GrupoEmpresa` cliente. |
| **Diretoria** | Externo | Proprietário do `GrupoEmpresa` (a rede) — gerencia todas as empresas da rede. |
| **Gerente** | Externo | Gestor financeiro atribuído a uma ou mais `Empresa` específicas dentro da rede. |

## Estrutura empresarial

Tenant = `GrupoEmpresa` (a rede), não `Empresa` isolada. Um `GrupoEmpresa` contém uma ou mais
`Empresa` — "Superdiniz" (citado como tenant de exemplo) é, neste modelo, um `GrupoEmpresa`.
Detalhe completo em `estrutura-empresarial.md`.

*A aprofundar: gestão detalhada de cada papel, se surgem outros papéis além destes três, e os
pontos em aberto listados nos dois arquivos de regras de negócio acima.*
