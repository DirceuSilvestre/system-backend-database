# Projeto Lógico de Banco de Dados — Delicatessen

---

## 1. Regras de Mapeamento ER para Lógico

* **Estratégia de Especialização (Herança):**
  * `FUNCIONARIO` / `SOMMELIER`: [Ex: Tabela própria para subclasse com chave primária e estrangeira herdada]
  * `PRODUTO` e Subtipos (`QUEIJO`, `PAO`, `BEBIDA`, etc.): [Ex: Mapeamento de Tabela Pai com subclasses conectadas via PK/FK]
* **Tratamento de Relacionamentos $1:N$:** [Ex: Inclusão da chave primária do lado "1" como chave estrangeira no lado "N"]
* **Tratamento de Relacionamentos $N:M$ / Associativas:** [Ex: Criação da tabela `ITEM_PEDIDO` unindo `PEDIDO_PERSONALIZADO` e `PRODUTO`]
* **Tratamento da Relação Ternária (Harmonização):** [Ex: Criação da tabela de relacionamento contendo as FKs de `SOMMELIER`, `BEBIDA` e `QUEIJO`]

---

## 2. Esquema Relacional (Assinaturas Sintéticas)

> **Convenção:** `NOME_TABELA` (<u>chave_primaria</u>, atributo_normal, *chave_estrangeira*)

* **CLIENTE** (<u>nome_cliente</u>, <u>telefone_cliente_1</u>, telefone_cliente_2, data_cadastro, data_nascimento, cep)
* **FUNCIONARIO** (<u>cpf_funcionario</u>, nome_funcionario, cargo_funcionario)
* **SOMMELIER** (<u>*cpf_funcionario*</u>, formacao_sommelier)
* **PRODUTO** (<u>id_produto</u>, nome_produto, descricao_produto, preco_venda, pais_origem, cidade_origem, regiao_origem, *cpf_funcionario_aquisicao*)
* **QUEIJO** (<u>*id_produto*</u>, sabor_queijo, textura_queijo, maturacao_queijo)
* **PAO** (<u>*id_produto*</u>, padeiro_fabricante_pao, formato_pao)
* **BEBIDA** (<u>*id_produto*</u>, teor_alcoolico, tipo_bebida)
* **ACESSORIO** (<u>*id_produto*</u>, tipo_preparo, tempo_preparo, bebida_preparada)
* **PRODUCAO_PROPRIA** (<u>*id_produto*</u>, data_confeccao, hora_confeccao, *cpf_funcionario_cozinheiro*)
* **ESTOQUE** (<u>lote_produto</u>, <u>*id_produto*</u>, data_validade, quantidade_produto, valor_custo)
* **PEDIDO_PERSONALIZADO** (<u>id_pedido</u>, data_hora_criacao_pedido, data_hora_retirada_pedido, status_pedido, *nome_cliente*, *telefone_cliente_1*, *cpf_funcionario_atendente*)
* **ITEM_PEDIDO** (<u>*id_pedido*</u>, <u>*id_produto*</u>, quantidade_item, preco_unitario_praticado, status_item, data_hora_inicio_preparo, data_hora_fim_preparo, *cpf_funcionario_cozinheiro*)
* **HARMONIZACAO** (<u>*cpf_sommelier*</u>, <u>*id_bebida*</u>, <u>*id_queijo*</u>, observacao_harmonizacao)

---

## 3. Dicionário de Dados (Especificação FÍSICA/SQL)

### 3.1 Tabela: `PEDIDO_PERSONALIZADO`
* **Descrição:** Armazena os pedidos customizados realizados pelos clientes e atendidos pelos funcionários.

| Coluna | Tipo de Dado (SQL) | Chave | Nulo? | Valor Padrão | Restrições / Validações (CHECK / FK) |
| :--- | :--- | :---: | :---: | :---: | :--- |
| `id_pedido` | `INT AUTO_INCREMENT` | **PK** | Não | - | `> 0` |
| `data_hora_criacao_pedido` | `DATETIME` | - | Não | `CURRENT_TIMESTAMP` | - |
| `data_hora_retirada_pedido`| `DATETIME` | - | Não | - | `CHECK (data_hora_retirada_pedido >= data_hora_criacao_pedido)` |
| `status_pedido` | `VARCHAR(30)` | - | Não | `'Pendente'` | `CHECK (status_pedido IN ('Pendente', 'Em Preparo', 'Pronto', 'Retirado', 'Cancelado'))` |
| `nome_cliente` | `VARCHAR(100)` | **FK** | Não | - | Ref: `CLIENTE(nome_cliente)` |
| `telefone_cliente_1` | `VARCHAR(15)` | **FK** | Não | - | Ref: `CLIENTE(telefone_cliente_1)` |
| `cpf_funcionario_atendente`| `VARCHAR(11)` | **FK** | Não | - | Ref: `FUNCIONARIO(cpf_funcionario)` |

*(Repetir a estrutura de tabela para as demais entidades do projeto)*

---

## 4. Validação do Processo de Normalização

### 4.1 Primeira Forma Normal (1FN)
* **Regra:** Eliminação de atributos multivalorados ou compostos.
* **Análise:** [Ex: O atributo composto 'endereço' foi reduzido ao 'cep' e telefones secundários foram tratados/isolados]
* **Status:** [ ] Aprovado

### 4.2 Segunda Forma Normal (2FN)
* **Regra:** Atributos não-chave devem depender da TOTALIDADE das chaves compostas.
* **Análise:** [Ex: Na tabela `ITEM_PEDIDO`, o `preco_unitario_praticado` depende do par (id_pedido, id_produto), pois representa a foto do preço naquele pedido especificamente]
* **Status:** [ ] Aprovado

### 4.3 Terceira Forma Normal (3FN)
* **Regra:** Não deve haver dependências transitivas (atributos não-chave dependendo de outros atributos não-chave).
* **Análise:** [Ex: Removidos calculados como 'valor_total_pedido', que deve ser gerado via VIEW/Query dinamicamente para evitar redundância]
* **Status:** [ ] Aprovado

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