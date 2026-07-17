# Engenharia de Requisitos - Sistema Delicatessen

## 1. Atores do Sistema
* **Proprietários / Gerentes:** Responsáveis pela gestão de produtos, estoque e leitura de relatórios.
* **Sommelier:** Especialista responsável pelas recomendações de harmonização de bebidas e queijos.
* **Cozinheiros:** Responsáveis pela preparação interna dos alimentos e atualização de status dos pedidos.
* **Atendentes:** Responsáveis pelo registro de pedidos, atendimento direto e controle de retiradas.
* **Clientes:** Consumidores finais que realizam as encomendas.

## 2. Requisitos Funcionais (RF)
* **RF01:** Permitir o cadastro (inclusão) e manutenção de produtos (contendo descrição e origem).
* **RF02:** Permitir que o Sommelier registre recomendações de harmonização entre queijos e bebidas.
* **RF03:** Permitir o registro de acessórios de café/chá (contendo nome, tipo de preparo, bebidas associadas e tempo de preparo).
* **RF04:** Permitir que o Atendente registre novos pedidos personalizados dos clientes.
* **RF05:** Permitir que os Cozinheiros visualizem a lista de itens pendentes para preparação (com quantidade e descrição).
* **RF06:** Permitir que o Cozinheiro atualize o status do item para "Concluído", informando a data/hora de confecção e a data de validade.
* **RF07:** Permitir que o Cozinheiro sinalize a falta de ingredientes para um item.
* **RF08:** Cancelar automaticamente um item do pedido caso o cozinheiro sinalize falta de ingredientes, disparando uma notificação para o Atendente.
* **RF09:** Emitir relatório para os Gerentes contendo os produtos e suas respectivas quantidades disponíveis para venda.
* **RF10:** Emitir a lista de produtos que ultrapassaram a data de validade.
* **RF11:** Emitir a relação diária de encomendas não retiradas no dia anterior (contendo nome, telefone do cliente, produtos e quantidades).
* **RF12:** Fornecer ao Gerente o relatório dos "n" produtos mais encomendados em um determinado período de tempo.
* **RF13:** Fornecer ao Gerente o histórico de compras de um cliente específico (nome do cliente, produtos, datas e preços).

## 3. Requisitos Não-Funcionais (RNF)
* **RNF01 (Restrição de Escopo):** O sistema NÃO deve contemplar módulos de cobrança ou processamento de pagamentos físicos/digitais (limitação orçamentária).
* **RNF02 (Arquitetura):** O sistema deve ser construído utilizando arquitetura de Banco de Dados Relacional.

## 4. Regras de Negócio (RN)
* **RN01 (Restrição de Acesso):** Apenas o perfil de Sommelier tem permissão para associar ou indicar harmonizações de vinhos e bebidas com queijos.
* **RN02 (Bloqueio de Venda):** Produtos que ultrapassarem a data de validade devem ser bloqueados pelo sistema, tornando sua venda impossível.
* **RN03 (Obrigatoriedade de Dados):** Todo item preparado internamente pela cozinha deve possuir, obrigatoriamente, data de confecção e data de validade antes de ser finalizado.
* **RN04 (Estorno):** Sempre que um item for cancelado por falta de ingredientes, o Atendimento ao Cliente deve ser acionado para realizar o estorno do valor de forma independente.