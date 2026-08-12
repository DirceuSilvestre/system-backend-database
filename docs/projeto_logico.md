# Projeto Lógico de Banco de Dados — Delicatessen

---

## 1. Regras de Mapeamento ER para Lógico

* **Estratégia de Especialização (Herança):**
  * `FUNCIONARIO` / `SOMMELIER`: [Ex: Tabela própria para subclasse com chave primária e estrangeira herdada]
  * `PRODUTO` e Subtipos (`QUEIJO`, `PAO`, `BEBIDA`, etc.): [Ex: Mapeamento de Tabela Pai com subclasses conectadas via PK/FK]
* **Tratamento de Relacionamentos $1:N$:** [Ex: Inclusão da chave primária do lado "1" como chave estrangeira no lado "N"]
* **Tratamento de Relacionamentos $N:N$ / Associativas:** [Ex: Criação da tabela `ITEM_PEDIDO` unindo `PEDIDO_PERSONALIZADO` e `PRODUTO`]
* **Tratamento da Relação Ternária (Harmonização):** [Ex: Criação da tabela de relacionamento contendo as FKs de `SOMMELIER`, `BEBIDA` e `QUEIJO`]

---

## 2. Esquema Relacional (Assinaturas Sintéticas)

> **Convenção:** `NOME_TABELA` (<u>chave_primaria</u>, atributo_normal, *chave_estrangeira*)

* **CLIENTE** (<u>id_cliente</u>, nome_cliente, telefone_cliente_1, telefone_cliente_2, data_cadastro, data_nascimento, cep)
* **FUNCIONARIO** (<u>cpf_funcionario</u>, nome, cargo)
* **SOMMELIER** (<u>*cpf_funcionario*</u>, formacao)
* **PRODUTO** (<u>id_produto</u>, nome, descricao, preco_venda, pais, cidade, regiao, *cpf_funcionario*)
* **QUEIJO** (<u>*id_produto*</u>, sabor, textura, maturacao)
* **PAO** (<u>*id_produto*</u>, fabricante, formato)
* **BEBIDA** (<u>*id_produto*</u>, teor_alcoolico, tipo_bebida)
* **ACESSORIO** (<u>*id_produto*</u>, tipo_preparo, tempo_preparo, bebida)
* **PRODUCAO_PROPRIA** (<u>*id_produto*</u>, data_hora_confeccao, *cpf_funcionario*)
* **ESTOQUE** (<u>lote_produto</u>, <u>*id_produto*</u>, data_validade, quantidade_produto, valor_custo)
* **PEDIDO_PERSONALIZADO** (<u>id_pedido</u>, data_hora_criacao, data_hora_retirada, status, *id_cliente*, *cpf_funcionario*)
* **ITEM_PEDIDO** (<u>*id_pedido*</u>, <u>*id_produto*</u>, quantidade, preco_unitario, status, data_hora_inicio, data_hora_fim, *cpf_funcionario*)
* **HARMONIZACAO** (<u>*cpf_funcionario*</u>, <u>*id_produto*</u>, <u>*id_produto*</u>, Descricao)

---

## 3. Dicionário de Dados (Especificação FÍSICA/SQL)

### 3.1 Tabela: `CLIENTE`

* **Descrição:** Armazena os dados cadastrais dos clientes da delicatessen.

| Coluna | Tipo de Dado (SQL) | Chave | Nulo? | Valor Padrão | Restrições / Validações (CHECK / FK) |
| --- | --- | --- | --- | --- | --- |
| `id_cliente` | `INT AUTO_INCREMENT` | **PK** | Não | - | `CHECK (id_cliente > 0)` |
| `nome_cliente` | `VARCHAR(100)` | - | Não | - | - |
| `telefone_cliente_1` | `VARCHAR(15)` | - | Não | - | Regex formato telefone/E.164 |
| `telefone_2` | `VARCHAR(15)` | - | Sim | `NULL` | - |
| `data_cadastro` | `DATE` | - | Não | `CURRENT_DATE` | `CHECK (data_cadastro <= CURRENT_DATE)` |
| `data_nascimento` | `DATE` | - | Sim | `NULL` | `CHECK (data_nascimento < CURRENT_DATE)` |
| `cep` | `VARCHAR(8)` | - | Sim | `NULL` | Regex: `^\d{8}$` |

---

### 3.2 Tabela: `FUNCIONARIO`

* **Descrição:** Registro geral de todos os colaboradores e funcionários da empresa.

| Coluna | Tipo de Dado (SQL) | Chave | Nulo? | Valor Padrão | Restrições / Validações (CHECK / FK) |
| --- | --- | --- | --- | --- | --- |
| `cpf_funcionario` | `VARCHAR(11)` | **PK** | Não | - | Regex: `^\d{11}$` (Validação Módulo 11) |
| `nome` | `VARCHAR(100)` | - | Não | - | Nome completo civil |
| `cargo` | `VARCHAR(50)` | - | Não | - | Ex: `'Atendente'`, `'Gerente'`, `'Cozinheiro'` |

---

### 3.3 Tabela: `SOMMELIER`

* **Descrição:** Especialização da entidade `FUNCIONARIO` para profissionais certificados em vinhos e harmonização.

| Coluna | Tipo de Dado (SQL) | Chave | Nulo? | Valor Padrão | Restrições / Validações (CHECK / FK) |
| --- | --- | --- | --- | --- | --- |
| `cpf_funcionario` | `VARCHAR(11)` | **PK / FK** | Não | - | Ref: `FUNCIONARIO(cpf_funcionario)` ON DELETE RESTRICT ON UPDATE CASCADE |
| `formacao` | `VARCHAR(100)` | - | Não | - | Certificação profissional (ex: WSET, ABS) |

---

### 3.4 Tabela: `PRODUTO`

* **Descrição:** Catálogo genérico de itens e mercadorias comercializados ou produzidos.

| Coluna | Tipo de Dado (SQL) | Chave | Nulo? | Valor Padrão | Restrições / Validações (CHECK / FK) |
| --- | --- | --- | --- | --- | --- |
| `id_produto` | `INT AUTO_INCREMENT` | **PK** | Não | - | `CHECK (id_produto > 0)` |
| `nome` | `VARCHAR(100)` | - | Não | - | Nome comercial do item |
| `descricao` | `TEXT` | - | Não | - | - |
| `preco_venda` | `DECIMAL(10,2)` | - | Não | - | `CHECK (preco_venda > 0.00)` |
| `pais` | `VARCHAR(50)` | - | Sim | `NULL` | País de origem |
| `cidade` | `VARCHAR(50)` | - | Sim | `NULL` | Cidade de origem |
| `regiao` | `VARCHAR(50)` | - | Sim | `NULL` | Região / Denominação de origem |
| `cpf_funcionario_gerente` | `VARCHAR(11)` | **FK** | Não | - | Ref: `FUNCIONARIO(cpf_funcionario)` ON DELETE RESTRICT ON UPDATE CASCADE |

---

### 3.5 Tabela: `QUEIJO`

* **Descrição:** Especialização de `PRODUTO` para o catálogo de queijos finos e artesanais.

| Coluna | Tipo de Dado (SQL) | Chave | Nulo? | Valor Padrão | Restrições / Validações (CHECK / FK) |
| --- | --- | --- | --- | --- | --- |
| `id_produto` | `INT` | **PK / FK** | Não | - | Ref: `PRODUTO(id_produto)` ON DELETE CASCADE ON UPDATE CASCADE |
| `sabor` | `VARCHAR(50)` | - | Não | - | Ex: `'Suave'`, `'Intenso'`, `'Picante'` |
| `textura` | `VARCHAR(50)` | - | Não | - | Ex: `'Macia'`, `'Semidura'`, `'Dura'` |
| `maturacao` | `VARCHAR(50)` | - | Não | - | Ex: `'Fresco'`, `'30 dias'`, `'12 meses'` |

---

### 3.6 Tabela: `PAO`

* **Descrição:** Especialização de `PRODUTO` contendo características específicas de pães.

| Coluna | Tipo de Dado (SQL) | Chave | Nulo? | Valor Padrão | Restrições / Validações (CHECK / FK) |
| --- | --- | --- | --- | --- | --- |
| `id_produto` | `INT` | **PK / FK** | Não | - | Ref: `PRODUTO(id_produto)` ON DELETE CASCADE ON UPDATE CASCADE |
| `fabricante` | `VARCHAR(100)` | - | Sim | `NULL` | Mestre padeiro ou padaria parceira |
| `formato` | `VARCHAR(50)` | - | Não | - | Ex: `'Baguete'`, `'Redondo'`, `'Focaccia'` |

---

### 3.7 Tabela: `BEBIDA`

* **Descrição:** Especialização de `PRODUTO` para vinhos, cervejas, espumantes e bebidas em geral.

| Coluna | Tipo de Dado (SQL) | Chave | Nulo? | Valor Padrão | Restrições / Validações (CHECK / FK) |
| --- | --- | --- | --- | --- | --- |
| `id_produto` | `INT` | **PK / FK** | Não | - | Ref: `PRODUTO(id_produto)` ON DELETE CASCADE ON UPDATE CASCADE |
| `teor_alcoolico` | `DECIMAL(4,2)` | - | Não | - | `CHECK (teor_alcoolico >= 0.00 AND teor_alcoolico <= 100.00)` |
| `tipo_bebida` | `VARCHAR(50)` | - | Não | - | Ex: `'Vinho Tinto'`, `'Espumante'`, `'Cerveja'` |

---

### 3.8 Tabela: `ACESSORIO`

* **Descrição:** Especialização de `PRODUTO` relacionada a utensílios e itens de preparo/serviço.

| Coluna | Tipo de Dado (SQL) | Chave | Nulo? | Valor Padrão | Restrições / Validações (CHECK / FK) |
| --- | --- | --- | --- | --- | --- |
| `id_produto` | `INT` | **PK / FK** | Não | - | Ref: `PRODUTO(id_produto)` ON DELETE CASCADE ON UPDATE CASCADE |
| `tipo_preparo` | `VARCHAR(50)` | - | Não | - | Ex: `'Prensa Francesa'`, `'Decantação'` |
| `tempo_preparo` | `INT` | - | Sim | `NULL` | `CHECK (tempo_preparo >= 0)` (em minutos) |
| `bebida` | `VARCHAR(50)` | - | Sim | `NULL` | Bebida associada ao acessório |

---

### 3.9 Tabela: `PRODUCAO_PROPRIA`

* **Descrição:** Especialização de `PRODUTO` para itens confeccionados internamente pela equipe da delicatessen.

| Coluna | Tipo de Dado (SQL) | Chave | Nulo? | Valor Padrão | Restrições / Validações (CHECK / FK) |
| --- | --- | --- | --- | --- | --- |
| `id_produto` | `INT` | **PK / FK** | Não | - | Ref: `PRODUTO(id_produto)` ON DELETE CASCADE ON UPDATE CASCADE |
| `data_hora_confeccao` | `DATETIME` | - | Não | - | Data e hora de conclusão do preparo |
| `cpf_funcionario_cozinheiro` | `VARCHAR(11)` | **FK** | Não | - | Ref: `FUNCIONARIO(cpf_funcionario)` ON DELETE RESTRICT ON UPDATE CASCADE |

---

### 3.10 Tabela: `ESTOQUE`

* **Descrição:** Controle físico de lotes de produtos, validade e custos de aquisição.

| Coluna | Tipo de Dado (SQL) | Chave | Nulo? | Valor Padrão | Restrições / Validações (CHECK / FK) |
| --- | --- | --- | --- | --- | --- |
| `lote_produto` | `VARCHAR(50)` | **PK** | Não | - | Identificador alfanumérico do lote |
| `id_produto` | `INT` | **PK / FK** | Não | - | Ref: `PRODUTO(id_produto)` ON DELETE RESTRICT ON UPDATE CASCADE |
| `data_validade` | `DATE` | - | Não | - | `CHECK (data_validade >= CURRENT_DATE)` |
| `quantidade_produto` | `INT` | - | Não | `0` | `CHECK (quantidade_produto >= 0)` |
| `valor_custo` | `DECIMAL(10,2)` | - | Não | - | `CHECK (valor_custo > 0.00)` |

---

### 3.11 Tabela: `PEDIDO_PERSONALIZADO`

* **Descrição:** Cabeçalho de encomendas e pedidos personalizados efetuados pelos clientes.

| Coluna | Tipo de Dado (SQL) | Chave | Nulo? | Valor Padrão | Restrições / Validações (CHECK / FK) |
| --- | --- | --- | --- | --- | --- |
| `id_pedido` | `INT AUTO_INCREMENT` | **PK** | Não | - | `CHECK (id_pedido > 0)` |
| `data_hora_criacao` | `DATETIME` | - | Não | `CURRENT_TIMESTAMP` | - |
| `data_hora_retirada_pedido` | `DATETIME` | - | Não | - | `CHECK (data_hora_retirada_pedido >= data_hora_criacao)` |
| `status` | `VARCHAR(30)` | - | Não | `'Pendente'` | `CHECK (status IN ('Pendente', 'Em Preparo', 'Pronto', 'Retirado', 'Cancelado'))` |
| `id_cliente` | `INT` | **FK** | Não | - | Ref: `CLIENTE(id_cliente)` ON DELETE RESTRICT ON UPDATE CASCADE |
| `cpf_funcionario_atendente_cozinheiro` | `VARCHAR(11)` | **FK** | Não | - | Ref: `FUNCIONARIO(cpf_funcionario)` ON DELETE RESTRICT ON UPDATE CASCADE |

---

### 3.12 Tabela: `ITEM_PEDIDO`

* **Descrição:** Tabela associativa que detalha os produtos contidos em cada pedido e o acompanhamento de preparo.

| Coluna | Tipo de Dado (SQL) | Chave | Nulo? | Valor Padrão | Restrições / Validações (CHECK / FK) |
| --- | --- | --- | --- | --- | --- |
| `id_pedido` | `INT` | **PK / FK** | Não | - | Ref: `PEDIDO_PERSONALIZADO(id_pedido)` ON DELETE CASCADE ON UPDATE CASCADE |
| `id_produto` | `INT` | **PK / FK** | Não | - | Ref: `PRODUTO(id_produto)` ON DELETE RESTRICT ON UPDATE CASCADE |
| `quantidade` | `INT` | - | Não | `1` | `CHECK (quantidade > 0)` |
| `preco_unitario` | `DECIMAL(10,2)` | - | Não | - | `CHECK (preco_unitario > 0.00)` (Preço praticado no momento) |
| `status` | `VARCHAR(30)` | - | Não | `'Pendente'` | `CHECK (status IN ('Pendente', 'Em Preparo', 'Concluído', 'Falta Ingrediente'))` |
| `data_hora_inicio` | `DATETIME` | - | Sim | `NULL` | Início do preparo na cozinha |
| `data_hora_fim` | `DATETIME` | - | Sim | `NULL` | `CHECK (data_hora_fim >= data_hora_inicio)` |
| `cpf_funcionario` | `VARCHAR(11)` | **FK** | Sim | `NULL` | Ref: `FUNCIONARIO(cpf_funcionario)` ON DELETE SET NULL ON UPDATE CASCADE |

---

### 3.13 Tabela: `HARMONIZACAO`

* **Descrição:** Registro ternário das recomendações técnicas de combinação entre bebidas e queijos elaboradas por sommeliers.

| Coluna | Tipo de Dado (SQL) | Chave | Nulo? | Valor Padrão | Restrições / Validações (CHECK / FK) |
| --- | --- | --- | --- | --- | --- |
| `cpf_funcionario_sommelier` | `VARCHAR(11)` | **PK / FK** | Não | - | Ref: `SOMMELIER(cpf_funcionario)` ON DELETE RESTRICT ON UPDATE CASCADE |
| `id_produto_bebida` | `INT` | **PK / FK** | Não | - | Ref: `BEBIDA(id_produto)` ON DELETE RESTRICT ON UPDATE CASCADE |
| `id_produto_queijo` | `INT` | **PK / FK** | Não | - | Ref: `QUEIJO(id_produto)` ON DELETE RESTRICT ON UPDATE CASCADE |
| `descricao` | `TEXT` | - | Não | - | Notas de degustação e justificativa da harmonização |

---

## 4. Validação do Processo de Normalização

### 4.1 Primeira Forma Normal (1FN)
* **Regra:** Eliminação de atributos multivalorados ou compostos.
* **Análise:** [Ex: O atributo composto 'endereço' foi reduzido ao 'cep' e telefones secundários foram tratados/isolados]
* **Status:** [x] Aprovado

### 4.2 Segunda Forma Normal (2FN)
* **Regra:** Atributos não-chave devem depender da TOTALIDADE das chaves compostas.
* **Análise:** [Ex: Na tabela `ITEM_PEDIDO`, o `preco_unitario` depende do par (id_pedido, id_produto), pois representa a foto do preço naquele pedido especificamente]
* **Status:** [x] Aprovado

### 4.3 Terceira Forma Normal (3FN)
* **Regra:** Não deve haver dependências transitivas (atributos não-chave dependendo de outros atributos não-chave).
* **Análise:** [Ex: Removidos calculados como 'valor_total_pedido', que deve ser gerado via VIEW/Query dinamicamente para evitar redundância]
* **Status:** [x] Aprovado

---

## 5. Integridade Referencial (Regras ON DELETE e ON UPDATE)

| Tabela Origem | Coluna FK | Tabela Destino | ON DELETE | ON UPDATE | Justificativa |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `PEDIDO_PERSONALIZADO` | `cpf_funcionario_atendente` | `FUNCIONARIO` | RESTRICT | CASCADE | Impede a exclusão de um funcionário que registrou vendas ativas. |
| `ITEM_PEDIDO` | `id_pedido` | `PEDIDO_PERSONALIZADO` | CASCADE | CASCADE | Ao cancelar/deletar um pedido inteiro, remove automaticamente seus itens. |
| `ESTOQUE` | `id_produto` | `PRODUTO` | RESTRICT | CASCADE | Protege o histórico de movimentação de lote. |

---

## 6. Mapeamento de Regras de Negócio em Constraints (`CHECK`)

1. **Validação de Preço de Venda:** `ALTER TABLE PRODUTO ADD CONSTRAINT CHK_preco_venda CHECK (preco_venda > 0.00);`
2. **Coerência Temporal de Preparo:** `ALTER TABLE ITEM_PEDIDO ADD CONSTRAINT CHK_tempo_preparo CHECK (data_hora_fim_preparo >= data_hora_inicio_preparo);`
3. **Consistência de Quantidade em Estoque:** `ALTER TABLE ESTOQUE ADD CONSTRAINT CHK_qtd_estoque CHECK (quantidade_produto >= 0);`

---

## 7. Estratégia de Indexação (Otimização de Consultas)

| Nome do Índice | Tabela | Coluna(s) | Tipo | Caso de Uso Principal |
| :--- | :--- | :--- | :--- | :--- |
| `IDX_PEDIDO_STATUS` | `PEDIDO_PERSONALIZADO` | `status_pedido` | BTREE | Agilizar filtragem da tela do operador (pedidos em preparo/prontos). |
| `IDX_ESTOQUE_VALIDADE`| `ESTOQUE` | `data_validade` | BTREE | Relatórios de controle de perdas e itens próximos ao vencimento. |

---

## 8. Rascunho das DDLs em SQL (Data Definition Language)

```sql
-- Insira aqui os scripts CREATE TABLE à medida que for finalizando as tabelas
```