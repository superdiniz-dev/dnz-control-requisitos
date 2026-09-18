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
| **Lançamento** | Registro de gasto ou receita de uma `Empresa`. Origem **comprovante** (parseado automaticamente, mas sempre aprovado pelo usuário antes de salvar; não editável depois) ou **manual** (via formulário, editável por 24 horas corridas após a criação), além do **estorno/ajuste**, que corrige os outros dois. Vale uma regra de duplicidade por ID da transação e por arquivo. Ver [`regras-de-negocio/lancamentos.md`](regras-de-negocio/lancamentos.md). |
| **Recebedor** | Parte do lançamento: quem recebeu o dinheiro (nome, CPF/CNPJ, instituição). Ver [`regras-de-negocio/dados-do-lancamento.md`](regras-de-negocio/dados-do-lancamento.md). |
| **Pagador** | Parte do lançamento: quem efetivamente pagou (nome, CPF/CNPJ, instituição). |
| **Devedor** | Parte **opcional** do lançamento: quem devia, quando o comprovante distingue isso de quem pagou. |
| **Tipo (do lançamento)** | Gasto ou receita. Campo próprio do lançamento: o sistema sugere ao ler o comprovante e o usuário confirma antes de salvar. |
| **Categoria** | Classificação do lançamento usada para agrupar relatórios. O catálogo mistura uma lista fixa do sistema com categorias cadastradas pela Diretoria do `GrupoEmpresa`; cada categoria é marcada como gasto, receita ou ambos. Obrigatória em todo lançamento. |
| **Forma de pagamento** | Como o pagamento foi feito. No MVP: PIX, boleto ou dinheiro. É ela que define quais campos são obrigatórios naquele lançamento — ver [`regras-de-negocio/dados-do-lancamento.md`](regras-de-negocio/dados-do-lancamento.md). |
| **ID da transação** | Identificador do pagamento na instituição financeira (não no DNZ Control). Dentro de uma `Empresa`, não se repete entre lançamentos vigentes — é por ele que o sistema recusa pagamento registrado duas vezes. Dispensado em dinheiro. Ver [`regras-de-negocio/dados-do-lancamento.md`](regras-de-negocio/dados-do-lancamento.md). |
| **Valor original** | Valor da transação antes de desconto, juros ou multa. Obrigatório no boleto (igual ao valor pago quando não há acréscimo nem desconto); opcional no PIX e no dinheiro. |
| **LoteLancamentos** | Agrupa os lançamentos gerados de um comprovante com vários pagamentos (pagamento em lote), declarado como tal pelo usuário ao enviar. Guarda o arquivo; **não tem valor, tipo nem partes e não entra no relatório**. Os lançamentos do lote são lançamentos comuns. Excluí-lo exclui (logicamente) os lançamentos vigentes dele. Ver [`regras-de-negocio/dados-do-lancamento.md`](regras-de-negocio/dados-do-lancamento.md). |
| **Estorno/ajuste** | Terceira origem de lançamento (além de comprovante e manual). Corrige um lançamento que não pode mais ser editado — de comprovante, ou manual passadas 24 horas da criação — sem editar o original. **Anula e substitui:** carrega todos os dados corrigidos e um motivo obrigatório; o original fica intacto, marcado como substituído, e sai do relatório. |
