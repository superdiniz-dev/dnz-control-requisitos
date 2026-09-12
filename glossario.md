# Glossário

<!-- RASCUNHO — brainstorm em andamento com o desenvolvedor. -->

| Termo | Definição |
|---|---|
| **Papel** | Perfil de acesso que um usuário tem no sistema. Um usuário pode ter um ou mais papéis. Classificado como **interno** ou **externo**, e organizado hierarquicamente — ver [`regras-de-negocio/papeis-e-hierarquia.md`](regras-de-negocio/papeis-e-hierarquia.md). |
| **Papel interno** | Papel ligado à operação do próprio DNZ Control, externo a qualquer empresa cliente. Hoje: **Administrador**. |
| **Papel externo** | Papel ligado a um `GrupoEmpresa`/`Empresa` cliente. Hoje, do maior para o menor: **Diretoria**, **Gerente**. |
| **Administrador** | Papel interno que administra o sistema DNZ Control em si. |
| **Diretoria** | Papel externo, proprietário do `GrupoEmpresa` — gerencia todas as empresas da rede. |
| **Gerente** | Papel externo — gestor financeiro atribuído a uma ou mais `Empresa` específicas dentro da rede. |
| **GrupoEmpresa** | A rede — conjunto de uma ou mais `Empresa` sob a mesma Diretoria. É o limite de isolamento (tenant) no modelo de multi-tenancy. Ver [`regras-de-negocio/estrutura-empresarial.md`](regras-de-negocio/estrutura-empresarial.md). |
| **Empresa** | Uma empresa individual dentro de um `GrupoEmpresa` — onde vivem lançamentos, comprovantes e relatórios financeiros. |
| **Exclusão lógica** | Forma padrão de remover qualquer registro no sistema — marcado como excluído, nunca apagado fisicamente. Ver [`regras-de-negocio/exclusao-logica.md`](regras-de-negocio/exclusao-logica.md). |
| **Lançamento** | Registro de gasto ou receita de uma `Empresa`. Origem **comprovante** (parseado automaticamente, mas sempre aprovado pelo usuário antes de salvar; não editável depois) ou **manual** (via formulário, editável até 1 dia após a criação). Ver [`regras-de-negocio/lancamentos.md`](regras-de-negocio/lancamentos.md). |
| **Estorno/ajuste** | Terceira origem de lançamento (além de comprovante e manual). Corrige um lançamento de comprovante errado, sem editar o original (que é imutável). |
