# Atividade Prática: Ambiente PostgreSQL com Docker, Inicialização Automática e Persistência

## 1. Contexto e Objetivo

Nesta atividade prática, você deverá preparar e entregar um ambiente conteinerizado com o banco de dados **PostgreSQL** utilizando **Docker** e **Docker Compose** (ou script de automação correspondente).

O objetivo é simular um cenário real de entrega de infraestrutura como código (IaC) para desenvolvimento, onde o professor/avaliador possa clonar o seu repositório no GitHub, rodar um único comando e ter um banco de dados totalmente operacional, já populado com dados iniciais (incluindo o seu cadastro como cliente) e com seus dados persistidos em disco local.

---

## 2. Requisitos Obrigatórios

A solução entregue deve atender rigorosamente aos seguintes critérios:

1. **Orquestração via Docker Compose ou Script:**
   - Criar um arquivo `docker-compose.yml` (ou script shell executável com comandos `docker run`) que inicialize o serviço PostgreSQL.
   - Definir versão estável da imagem oficial do PostgreSQL (ex: `postgres:16` ou `postgres:latest`).
   - Expor a porta padrão `5432:5432` (ou porta alternativa devidamente documentada).
   - Configurar as variáveis de ambiente necessárias (`POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD`).

2. **Carga Automática de Dados com Identificação do Estudante:**
   - Ao subir o container pela primeira vez, o banco de dados deve ser provisionado automaticamente com a tabela `clientes` populada com **5 registros no total**.
   - **Obrigatório:** Você deve utilizar os 4 registros dummy fornecidos na Seção 3 e **adicionar obrigatoriamente o seu próprio nome e dados como o 5º cliente cadastrado**.
   - *Dica técnica:* utilize o recurso padrão da imagem oficial do Postgres montando o arquivo SQL no diretório `/docker-entrypoint-initdb.d/` do container (ex: `./init.sql:/docker-entrypoint-initdb.d/init.sql`).

3. **Persistência de Dados via Volume Espelhado (Bind Mount):**
   - Os dados do PostgreSQL **devem ser espelhados em uma pasta local do usuário** (por exemplo, na pasta `./data/` ou `./pgdata/` dentro da raiz do projeto).
   - *Atenção:* caso o container seja parado e recriado (`docker compose down` seguido de `docker compose up`), os dados devem permanecer salvos e íntegros no diretório local da máquina hospedeira.

---

## 3. Script SQL Fornecido (Estrutura e Carga Inicial)

Utilize o script SQL abaixo no seu arquivo de inicialização (`init.sql`). Observe que o 5º registro deve conter **os seus dados reais de estudante**:

```sql
-- ==========================================================
-- SCRIPT DE INICIALIZAÇÃO: BANCO DE DADOS E REGISTROS DUMMY
-- ==========================================================

-- Criação da tabela de clientes
CREATE TABLE IF NOT EXISTS clientes (
    id SERIAL PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    email VARCHAR(120) NOT NULL UNIQUE,
    telefone VARCHAR(20),
    cidade VARCHAR(80),
    data_cadastro TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Inserção de registros: 4 registros de exemplo + 1 registro do estudante
INSERT INTO clientes (nome, email, telefone, cidade) VALUES
('Ana Clara Souza', 'ana.souza@email.com', '(11) 98765-4321', 'São Paulo'),
('Bruno Eduardo Lima', 'bruno.lima@email.com', '(21) 97654-3210', 'Rio de Janeiro'),
('Carla Mendes da Silva', 'carla.mendes@email.com', '(31) 96543-2109', 'Belo Horizonte'),
('Diego Martins Ferreira', 'diego.martins@email.com', '(41) 95432-1098', 'Curitiba'),
-- ATENÇÃO: Substitua a linha abaixo pelos seus dados completos (Nome, E-mail, Telefone, Cidade)
('[SEU NOME COMPLETO AQUI]', '[seu.email@exemplo.com]', '(11) 90000-0000', '[Sua Cidade]');
```

---

## 4. Estrutura de Diretórios Recomendada

A organização do seu repositório no GitHub deve seguir o padrão:

```text
entrega-postgres-docker/
├── docker-compose.yml          # Definição do serviço, portas, volumes e variáveis
├── init.sql                    # Script SQL com a tabela e os 5 registros (com seus dados)
├── .env.example                # (Opcional/Recomendado) Exemplo das variáveis de ambiente
├── data/                       # Diretório local espelhado para os dados do PostgreSQL
├── .gitignore                  # Ignorar arquivos gerados pelo banco na pasta data/
└── README.md                   # Instruções de execução e credenciais de acesso
```

---

## 5. Instruções de Verificação (Como o Professor Irá Avaliar)

O professor avaliará a sua entrega executando os seguintes passos:

1. **Clonar o repositório do GitHub e subir o ambiente:**
   ```bash
   docker compose up -d
   ```
2. **Verificar os dados inseridos automaticamente (inclusive o seu cadastro):**
   ```bash
   docker exec -it <NOME_DO_CONTAINER> psql -U <USUARIO> -d <BANCO> -c "SELECT * FROM clientes;"
   ```
   *Resultado esperado:* Os 5 registros devem ser retornados imediatamente, contendo o registro com o seu nome.

3. **Testar a persistência com volume espelhado:**
   - Inserir um novo registro via cliente SQL ou linha de comando.
   - Parar e remover o container:
     ```bash
     docker compose down
     ```
   - Subir o container novamente:
     ```bash
     docker compose up -d
     ```
   - Confirmar se todos os registros continuam persistidos e se os arquivos foram criados no diretório local da máquina hospedeira.

---

## 6. Prazos e Pontuação

Fique atento às datas de entrega e à pontuação máxima correspondente. O fechamento oficial do bimestre será no dia **02/10**.

| Período de Entrega | Prazo Final | Pontuação Máxima |
| :--- | :--- | :---: |
| **1ª Semana (Prazo Regular)** | **Até 28/09 (segunda-feira)** | **Até 10,0 pontos** |
| **2ª Semana (Prazo Prorrogado)** | **Até 30/09 (quarta-feira)** | **Até 5,0 pontos** |
| **Prazo Final de Tolerância** | **Até 01/10 (quinta-feira)** | **Até 3,0 pontos** |
| **Fechamento de Bimestre** | **02/10** | *Não serão aceitas entregas* |

---

## 7. Instruções de Envio

Para submeter sua atividade, siga rigorosamente as orientações abaixo:

1. **Hospedagem no GitHub:**
   - O projeto deve estar publicado em um repositório no **GitHub** contendo todos os arquivos (`docker-compose.yml`, `init.sql`, `README.md`, etc.).
   - O repositório deve estar **público** para permitir a avaliação pelo professor.

2. **Envio por E-mail:**
   - **Destinatário:** `sergiogabriel@prof.educacao.sp.gov.br`
   - **Assunto do e-mail:**  
     `Trabalho de Modelagem de Dados com Docker e Postgres - [Nome do Estudante] - 3ºB`  
     *(Substitua `[Nome do Estudante]` pelo seu nome completo)*
   - **Corpo do e-mail:**  
     Cole diretamente o **link do repositório no GitHub** e, se necessário, adicione notas ou instruções complementares de acesso.
