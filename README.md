# Delicatessen System: BackEnd API & Relational Database

Este repositório contém um sistema BackEnd completo integrado a um Banco de Dados Relacional robusto. O projeto foi concebido a partir de um cenário real (Mini Mundo) fornecido na disciplina TN727 - Banco de Dados da Universidade Federal Rural do Rio de Janeiro (UFRRJ), evoluindo para uma aplicação BackEnd pronta para consumo.

O objetivo deste projeto é demonstrar a fusão entre uma modelagem de dados altamente eficiente (integridade e normalização) e uma camada de serviços (API) performática, escalável e segura.

---

## Stack Tecnológica
* **BackEnd:** Python & FastAPI (Validação de dados, roteamento e alta performance)
* **Banco de Dados:** PostgreSQL & Linguagem SQL (DDL/DML)
* **Infraestrutura & Boas Práticas:** Git, GitHub, Linha de Comando e Padrões de Projeto (Design Patterns)

---

## Roadmap de Desenvolvimento (Acompanhamento do Projeto)

Utilize este checklist para acompanhar o progresso real de entrega do sistema. As etapas cobrem desde a concepção dos dados até as regras mais complexas do BackEnd.

### Fase 1: Engenharia e Arquitetura de Dados (Em Andamento)
- [x] **Análise do Mini Mundo:** Extração de entidades, atributos e regras de negócio do cenário real.
- [ ] **Modelagem Conceitual:** Criação do Diagrama Entidade-Relacionamento (ER) na pasta `/docs`.
- [ ] **Modelagem Lógica & Normalização:** Aplicação estrita das regras até a **3ª Forma Normal (3FN)** para eliminar redundâncias e anomalias.
- [ ] **Script DDL (Data Definition Language):** Criação física das tabelas no banco com integridade referencial (`PRIMARY KEY`, `FOREIGN KEY`, `NOT NULL`, `CHECK`).
- [ ] **Script DML (Data Manipulation Language):** População do banco de dados com dados de teste coerentes e criação de queries complexas (INNER JOINs, agregações e filtros).

### Fase 2: Construção do BackEnd Completo (Em Andamento)
- [x] **Configuração do Ambiente:** Inicialização do Git, estruturação de pastas profissionais e gerenciamento de dependências (`requirements.txt`).
- [ ] **Camada de Conexão (Database Driver):** Implementação do pool de conexões seguro e gerenciamento de sessões com o banco de dados.
- [ ] **Schemas de Validação (Pydantic):** Criação de modelos de dados para garantir que qualquer informação que entre na API seja sanitizada e validada antes de tocar o banco de dados.
- [ ] **Desenvolvimento dos Endpoints (CRUD Completo):** Construção das rotas de criação, leitura, atualização e deleção para as principais entidades do negócio.
- [ ] **Tratamento de Exceções Global (Middleware):** Implementação de capturas de erro customizadas para responder com os códigos HTTP corretos (400, 404, 500), blindando a aplicação contra quedas inesperadas.
- [ ] **Otimização de Consultas na API:** Integração das queries SQL otimizadas diretamente nas rotas do FastAPI, garantindo respostas rápidas mesmo com grandes volumes de dados.
- [ ] **Documentação Interativa (Swagger UI):** Revisão e descrição detalhada de cada rota e parâmetro para facilitar o consumo por desenvolvedores FrontEnd.

---

## Estrutura de Pastas do Projeto

```text
├── docs/               # Diagramas, modelo ER e documento descritivo do Mini Mundo
├── sql/                # Scripts de banco de dados (.sql) estruturados
│   ├── ddl_schema.sql  # Criação de tabelas e restrições
│   └── dml_queries.sql # Carga de dados e consultas de performance
├── src/                # Código-fonte da aplicação BackEnd
│   ├── main.py         # Ponto de entrada do FastAPI e inicialização do servidor
│   ├── config/         # Configurações de ambiente e variáveis seguras
│   ├── models/         # Schemas de validação e mapeamento de dados
│   └── routes/         # Controladores e definição de rotas/endpoints da API
├── .gitignore          # Proteção contra arquivos temporários e locais
├── LICENSE             # Licença MIT de uso livre
└── README.md           # Guia de bordo e documentação oficial
```

## API Endpoints Principais (Arquitetura Restful)

A API expõe o banco de dados de forma padronizada. A documentação completa e interativa do ecossistema é gerada nativamente pelo FastAPI via Swagger UI nas seguintes rotas core:

*   `GET /produtos` - Retorna a listagem de produtos integrando filtros de estoque em tempo real via SQL.
*   `POST /produtos` - Realiza a inserção de novos itens validando a tipagem de dados na entrada.
*   `GET /pedidos/{id}` - Consulta consolidada de um pedido específico utilizando INNER JOIN para unificar dados de cliente, itens e produtos em uma única chamada performática.
*   `POST /pedidos` - Registra uma nova venda, disparando as regras de validação de integridade.

---

## Como Executar a Aplicação (Ambiente Local)

Para clonar, configurar e executar este ecossistema BackEnd em sua máquina local, execute os comandos abaixo no seu terminal:

1. **Clonar o repositório:**
```bash
git clone https://github.com/DirceuSilvestre/system-backend-database.git
cd system-backend-database
```

2. **Configurar o Ambiente Virtual (Python venv):**
```bash
python -m venv venv
```

3. **Ativar o Ambiente Virtual:**
     * No Windows:
    ```bash
    venv\Scripts\activate
    ```
     * No Linux/macOS:
    ```bash
    source venv/bin/activate
    ```
    
4. **Instalar as Dependências:**
```bash
pip install -r requirements.txt
```

5. **Inicializar o Servidor BackEnd:**
```bash
uvicorn src.main:app --reload
```

Após o servidor iniciar, a documentação viva e interativa dos endpoints estará disponível em: http://127.0.0.1:8000/docs

## Licença

Este projeto está sob a licença MIT. Consulte o arquivo LICENSE para obter mais detalhes.
