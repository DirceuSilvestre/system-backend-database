# 📚 Modelo Entidade-Relacionamento (MER) — Delicatessen

---

## 1. Entidades

* `CLIENTE`
* `FUNCIONARIO`
* `SOMMELIER`
* `PRODUTO`
* `QUEIJO`
* `PAO`
* `BEBIDA`
* `ACESSORIO`
* `PRODUCAO_PROPRIA`
* `ESTOQUE`
* `PEDIDO_PERSONALIZADO`
* `ITEM_PEDIDO`

---

## 2. Atributos

* **Atributos de Cliente:** `nome_cliente`, `telefone_cliente_1`, `telefone_cliente_2`, `data_cadastro`, `data_nascimento`, `cep`
* **Atributos de Funcionário:** `cpf_funcionario`, `nome_funcionario`, `cargo_funcionario`
* **Atributos de Sommelier:** `formacao_sommelier`
* **Atributos de Produto:** `id_produto`, `nome_produto`, `descricao_produto`, `preco_venda`, `pais_origem`, `cidade_origem`, `regiao_origem`
* **Atributos de Queijo:** `sabor_queijo`, `textura_queijo`, `maturacao_queijo`
* **Atributos de Pão:** `padeiro_fabricante_pao`, `formato_pao`
* **Atributos de Bebida:** `teor_alcoolico`, `tipo_bebida`
* **Atributos de Acessório:** `tipo_preparo`, `tempo_preparo`, `bebida_preparada`
* **Atributos de Produção Própria:** `data_confeccao`, `hora_confeccao`
* **Atributos de Estoque:** `lote_produto`, `data_validade`, `quantidade_produto`, `valor_custo`
* **Atributos de Pedido Personalizado:** `id_pedido`, `data_criacao_pedido`, `hora_criacao_pedido`, `data_retirada_pedido`, `hora_retirada_pedido`, `status_pedido`
* **Atributos de Item do Pedido:** `id_pedido`, `id_produto`, `quantidade_item`, `preco_unitario_praticado`, `status_item`, `data_hora_inicio_preparo`, `data_hora_fim_preparo`

---

## 3. Generalizações e Especializações

### 3.1 Hierarquia de Funcionários (`FUNCIONARIO`)

* **Entidade Pai (Genérica):** `FUNCIONARIO`
* **Especialização:** `SOMMELIER`
* **Tipo de Herança:** Parcial e Discreta $(p, d)$. Nem todo funcionário é sommelier, e as especializações são mutuamente exclusivas.



### 3.2 Hierarquia de Produtos (`PRODUTO`)

* **Entidade Pai (Genérica):** `PRODUTO`
* **Especializações:** `QUEIJO`, `PAO`, `BEBIDA`, `ACESSORIO`, `PRODUCAO_PROPRIA`
* **Tipo de Herança:** Parcial e Discreta $(p, d)$.



---

## 4. Entidades junto de seus Atributos

### 4.1 Módulo: Clientes e Atendimento

#### `CLIENTE`

| Atributo | Tipo de Dado | Domínio / Regra | Tipo de Chave | Nulo? | Descrição |
| --- | --- | --- | --- | --- | --- |
| `nome_cliente` | Varchar(100) | Nome completo do cliente. | **PK** (Composta) | Não | Parte 1 da PK. Identificação do cliente. |
| `telefone_cliente_1` | Varchar(15) | Formato numérico / E.164. | **PK** (Composta) | Não | Parte 2 da PK. Telefone principal de contato. |
| `telefone_cliente_2` | Varchar(15) | Formato numérico / E.164. | - | Sim | Telefone secundário para contato. |
| `data_cadastro` | Date | Data em que o cliente foi registrado ($\le \text{hoje}$). | - | Não | Data de cadastro no sistema. |
| `data_nascimento` | Date | Data de nascimento. | - | Sim | Data para verificação de idade / promoções. |
| `cep` | Varchar(8) | 8 dígitos numéricos. | - | Sim | Código de Endereçamento Postal. |

---

### 4.2 Módulo: Quadro de Funcionários

#### `FUNCIONARIO` (Entidade Genérica)

| Atributo | Tipo de Dado | Domínio / Regra | Tipo de Chave | Nulo? | Descrição |
| --- | --- | --- | --- | --- | --- |
| `cpf_funcionario` | Varchar(11) | Validação pelo Módulo 11. Regex: `^\d{11}$`<br> | **PK**<br> | Não | CPF único do colaborador. |
| `nome_funcionario` | Varchar(100) | Nome completo civil. | - | Não | Nome do funcionário. |
| `cargo_funcionario` | Varchar(50) | Ex: `'Atendente'`, `'Cozinheiro'`, `'Gerente'`, `'Sommelier'`. | - | Não | Cargo exercido na empresa. |

#### `SOMMELIER` (Especialização de Funcionário)

| Atributo | Tipo de Dado | Domínio / Regra | Tipo de Chave | Nulo? | Descrição |
| --- | --- | --- | --- | --- | --- |
| `cpf_funcionario` | Varchar(11) | Referência à tabela `FUNCIONARIO`. | **PK / FK**<br> | Não | CPF herdado da entidade pai `FUNCIONARIO`. |
| `formacao_sommelier` | Varchar(100) | Ex: *WSET Level 3*, *ABS*, *SENAC*. | - | Não | Certificação ou instituição de formação. |

---

### 4.3 Módulo: Catálogo de Produtos e Subtipos

#### `PRODUTO` (Entidade Genérica)

| Atributo | Tipo de Dado | Domínio / Regra | Tipo de Chave | Nulo? | Descrição |
| --- | --- | --- | --- | --- | --- |
| `id_produto` | Inteiro | Chave primária gerada pelo sistema. | **PK**<br> | Não | Código identificador único do produto. |
| `nome_produto` | Varchar(100) | Nome comercial do item. | - | Não | Nome exibido em listas e cardápios. |
| `descricao_produto` | Text | Detalhamento descritivo do item. | - | Não | Descrição completa do produto. |
| `preco_venda` | Decimal(10,2) | Valor numérico maior que zero ($> 0.00$). | - | Não | Preço praticado para venda. |
| `pais_origem` | Varchar(50) | País de procedência do item. | - | Sim | País de fabricação/extração. |
| `cidade_origem` | Varchar(50) | Cidade de procedência. | - | Sim | Cidade de origem do produto. |
| `regiao_origem` | Varchar(50) | Região demarcada. | - | Sim | Denominação de origem geográfica. |

#### `QUEIJO` (Especialização)

| Atributo | Tipo de Dado | Domínio / Regra | Tipo de Chave | Nulo? | Descrição |
| --- | --- | --- | --- | --- | --- |
| `id_produto` | Inteiro | Referência à tabela `PRODUTO`. | **PK / FK**<br> | Não | Identificador herdado de `PRODUTO`. |
| `sabor_queijo` | Varchar(50) | Ex: *Suave*, *Intenso*, *Picante*. | - | Não | Perfil de sabor do queijo. |
| `textura_queijo` | Varchar(50) | Ex: *Macia*, *Cremosa*, *Semidura*, *Dura*. | - | Não | Consistência física da massa. |
| `maturacao_queijo` | Varchar(50) | Ex: *Fresco*, *30 dias*, *12 meses*. | - | Não | Período ou estágio de cura. |

#### `PAO` (Especialização)

| Atributo | Tipo de Dado | Domínio / Regra | Tipo de Chave | Nulo? | Descrição |
| --- | --- | --- | --- | --- | --- |
| `id_produto` | Inteiro | Referência à tabela `PRODUTO`. | **PK / FK**<br> | Não | Identificador herdado de `PRODUTO`. |
| `padeiro_fabricante_pao` | Varchar(100) | Nome do mestre padeiro ou padaria parceira. | - | Sim | Responsável pela fornada/receita. |
| `formato_pao` | Varchar(50) | Ex: *Baguete*, *Redondo*, *Focaccia*. | - | Não | Apresentação física do pão. |

#### `BEBIDA` (Especialização)

| Atributo | Tipo de Dado | Domínio / Regra | Tipo de Chave | Nulo? | Descrição |
| --- | --- | --- | --- | --- | --- |
| `id_produto` | Inteiro | Referência à tabela `PRODUTO`. | **PK / FK**<br> | Não | Identificador herdado de `PRODUTO`. |
| `teor_alcoolico` | Decimal(4,2) | Percentual de $0.00$ a $100.00\%$. | - | Não | Graduação alcoólica (% vol). |
| `tipo_bebida` | Varchar(50) | Ex: *Vinho Tinto*, *Espumante*, *Cerveja*. | - | Não | Classificação da bebida. |

#### `ACESSORIO` (Especialização)

| Atributo | Tipo de Dado | Domínio / Regra | Tipo de Chave | Nulo? | Descrição |
| --- | --- | --- | --- | --- | --- |
| `id_produto` | Inteiro | Referência à tabela `PRODUTO`. | **PK / FK**<br> | Não | Identificador herdado de `PRODUTO`. |
| `tipo_preparo` | Varchar(50) | Ex: *Prensa Francesa*, *Decantação*. | - | Não | Método de preparo/serviço associado. |
| `tempo_preparo` | Inteiro | Tempo estimado em minutos ($\ge 0$). | - | Sim | Tempo médio de preparo. |
| `bebida_preparada` | Varchar(50) | Ex: *Café*, *Chá*, *Vinho*. | - | Sim | Tipo de bebida harmonizada ou preparada. |

#### `PRODUCAO_PROPRIA` (Especialização)

| Atributo | Tipo de Dado | Domínio / Regra | Tipo de Chave | Nulo? | Descrição |
| --- | --- | --- | --- | --- | --- |
| `id_produto` | Inteiro | Referência à tabela `PRODUTO`. | **PK / FK**<br> | Não | Identificador herdado de `PRODUTO`. |
| `data_confeccao` | Date | Data da confecção ($\le \text{hoje}$). | - | Não | Data de preparação do lote. |
| `hora_confeccao` | Time | Formato `HH:MM:SS`. | - | Não | Horário de conclusão do preparo. |

---

### 4.4 Módulo: Estoque, Encomendas e Itens do Pedido

#### `ESTOQUE`

| Atributo | Tipo de Dado | Domínio / Regra | Tipo de Chave | Nulo? | Descrição |
| --- | --- | --- | --- | --- | --- |
| `lote_produto` | Varchar(50) | Código identificador do lote. | **PK** (Composta) | Não | Identificador do lote físico no estoque. |
| `id_produto` | Inteiro | Referência à tabela `PRODUTO`. | **PK / FK** | Não | Produto armazenado no lote. |
| `data_validade` | Date | Data limite para consumo ($\ge \text{hoje}$). | - | Não | Data de expiração da validade do lote. |
| `quantidade_produto` | Inteiro | Quantidade física disponível ($\ge 0$). | - | Não | Unidades físicas em estoque. |
| `valor_custo` | Decimal(10,2) | Valor unitário de aquisição ($> 0.00$). | - | Não | Custo de aquisição/produção do lote. |

#### `PEDIDO_PERSONALIZADO`

| Atributo | Tipo de Dado | Domínio / Regra | Tipo de Chave | Nulo? | Descrição |
| --- | --- | --- | --- | --- | --- |
| `id_pedido` | Inteiro | Identificador autoincrementável. | **PK**<br> | Não | Código único da encomenda personalizada. |
| `data_criacao_pedido` | Date | Data em que o pedido foi efetuado. | - | Não | Data de abertura do pedido. |
| `hora_criacao_pedido` | Time | Horário no formato `HH:MM:SS`. | - | Não | Hora de abertura do pedido. |
| `data_retirada_pedido` | Date | Data prevista para a retirada do pedido. | - | Não | Data em que o cliente buscará a encomenda. |
| `hora_retirada_pedido` | Time | Hora prevista para a retirada do pedido. | - | Não | Horário estipulado para busca. |
| `status_pedido` | Varchar(30) | `'Pendente'`, `'Em Preparo'`, `'Pronto'`, `'Retirado'`, `'Cancelado'`. | - | Não | Situação global da encomenda. |

#### `ITEM_PEDIDO` (Entidade Associativa)

| Atributo | Tipo de Dado | Domínio / Regra | Tipo de Chave | Nulo? | Descrição |
| --- | --- | --- | --- | --- | --- |
| `id_pedido` | Inteiro | Referência a `PEDIDO_PERSONALIZADO`. | **PK / FK**<br> | Não | Pedido ao qual o item pertence. |
| `id_produto` | Inteiro | Referência a `PRODUTO`. | **PK / FK**<br> | Não | Produto associado. |
| `quantidade_item` | Inteiro | Quantidade solicitada ($> 0$). | - | Não | Unidades do produto no pedido. |
| `preco_unitario_praticado` | Decimal(10,2) | Preço histórico negociado/aplicado. | - | Não | Preço praticado no momento da compra. |
| `status_item` | Varchar(30) | `'Pendente'`, `'Em Preparo'`, `'Concluído'`, `'Falta Ingrediente'`. | - | Não | Status individual de preparação do item. |
| `data_hora_inicio_preparo` | DateTime | Formato `YYYY-MM-DD HH:MM:SS`. | - | Sim | Momento do início da preparação na cozinha. |
| `data_hora_fim_preparo` | DateTime | Formato `YYYY-MM-DD HH:MM:SS`. | - | Sim | Momento do término do preparo. |

---

## 5. Relacionamentos com suas Cardinalidades

As cardinalidades e nomenclaturas abaixo foram mapeadas diretamente do Diagrama de Entidade-Relacionamento fornecido:

| Entidade A | Card. A | Relacionamento | Card. B | Entidade B | Descrição / Regra de Negócio |
| --- | --- | --- | --- | --- | --- |
| **CLIENTE** | $(0, n)$ | **realiza** | $(1, 1)$ | **PEDIDO_PERSONALIZADO** | Um cliente realiza de $0$ a vários pedidos. Todo pedido personalizado pertence obrigatoriamente a $1$ cliente. |
| **FUNCIONARIO** | $(0, n)$ | **monta** | $(1, 1)$ | **PEDIDO_PERSONALIZADO** | Um funcionário (atendente) monta $0$ a vários pedidos. Todo pedido é montado por $1$ funcionário. |
| **PEDIDO_PERSONALIZADO** | $(1, 1)$ | **pedido_item** | $(1, n)$ | **ITEM_PEDIDO** | Todo pedido contém obrigatoriamente de $1$ a N itens. Cada item de pedido pertence a $1$ pedido específico. |
| **FUNCIONARIO** | $(0, n)$ | **prepara** | $(0, 1)$ | **ITEM_PEDIDO** | Um funcionário (cozinheiro) prepara de $0$ a N itens. Um item de pedido pode ser preparado por até $1$ funcionário. |
| **ITEM_PEDIDO** | $(1, 1)$ | **item_produto** | $(0, n)$ | **PRODUTO** | Cada item de pedido refere-se a $1$ produto cadastrado. Um produto pode figurar em $0$ ou vários itens de pedidos. |
| **FUNCIONARIO** | $(0, n)$ | **cozinha** | $(1, 1)$ | **PRODUCAO_PROPRIA** | Um funcionário (cozinheiro) cozinha $0$ a vários produtos de produção própria. Cada item de produção própria é cozinhado por $1$ funcionário. |
| **FUNCIONARIO** | $(0, n)$ | **aquisicao** | $(1, 1)$ | **PRODUTO** | Um funcionário (gerente) realiza a aquisição de $0$ a N produtos. Todo produto cadastrado possui a supervisão/aquisição de $1$ funcionário. |
| **PRODUTO** | $(1, 1)$ | **produto_estoque** | $(1, n)$ | **ESTOQUE** | Todo produto cadastrado deve ter pelo menos $1$ ou mais entradas/lotes no estoque. |
| **SOMMELIER** | $(0, n)$ | **Rel** | $(1, 1)$ | **harmonização** | O sommelier registra recomendações na relação ternária de harmonização. |
| **BEBIDA** | $(0, n)$ | **Rel** | $(1, 1)$ | **harmonização** | A bebida compõe a relação ternária de harmonização. |
| **QUEIJO** | $(0, n)$ | **Rel** | $(1, 1)$ | **harmonização** | O queijo compõe a relação ternária de harmonização. |

---

## 6. Registros de Decisão de Projeto

1. **Uso do Tipo `DateTime` (`TIMESTAMP`) para Acompanhamento de Preparo:**
* *Decisão:* Adotar o tipo `DateTime` para os campos `data_hora_inicio_preparo` e `data_hora_fim_preparo` na tabela `ITEM_PEDIDO`.
* *Justificativa:* Unificar data e hora em uma única estrutura otimiza consultas temporais e previne erros em pedidos iniciados próximo à meia-noite que terminam no dia seguinte.

2. **Separação de Data e Hora de Confecção e Retirada:**
* *Decisão:* Preservação dos campos individuais (`data_confeccao`, `hora_confeccao`, `data_criacao_pedido`, `hora_criacao_pedido`, `data_retirada_pedido`, `hora_retirada_pedido`) conforme especificado na modelagem original.

3. **Mapeamento de Heranças por Tabela Única por Classe Concreta / Tabela Pai:**
* *Decisão:* Manter a tabela pai `PRODUTO` com chave gerada (`id_produto`), e as subclasses herdando `id_produto` como **PK/FK**.

4. **Desmembramento do Lote em Entidade `ESTOQUE`:**
* *Decisão:* Modelar a entidade `ESTOQUE` associada ao `PRODUTO` para permitir múltiplos lotes com datas de validade, custos e quantidades distintas para um mesmo item.

---

## 7. Registro de Decisões de Arquitetura, de Domínio e de Regras de Negócio

### 7.1 Decisões de Arquitetura (ADRs)

* **ADR-001: Rastreabilidade de Responsabilidade Operacional:** Os relacionamentos `monta`, `prepara`, `cozinha` e `aquisicao` exigem o registro do CPF do funcionário envolvido em cada etapa, viabilizando auditoria e indicadores de desempenho (KPIs) por cargo.

* **ADR-002: Isolamento do Módulo de Cobrança / Pagamentos:** O sistema limita-se a gravar o `preco_unitario_praticado` no momento do pedido. Processamentos de cartões ou pagamentos externos não integram o escopo de dados relacional.


### 7.2 Regras de Negócio de Domínio (RN)

* **RN01 — Restrição de Perfil para Harmonização:** Somente colaboradores com o registro de especialização na entidade `SOMMELIER` podem estabelecer o vínculo no relacionamento ternário de **harmonização** entre `BEBIDA` e `QUEIJO`.

* **RN02 — Gestão de Status e Cancelamento por Falta de Insumos:** Quando um cozinheiro altera o `status_item` para `'Falta Ingrediente'`, o sistema dispara uma notificação ao atendente responsável pelo pedido para atualização da encomenda ou estorno financeiro manual.

* **RN03 — Bloqueio de Produtos Vencidos:** Lotes registrados em `ESTOQUE` com `data_validade < hoje` são bloqueados para inclusão em novos pedidos.