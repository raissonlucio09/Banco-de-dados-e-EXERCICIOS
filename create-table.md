# CREATE DATABASE e CREATE TABLE (DDL)

## Criando e usando um banco
```sql
CREATE DATABASE loja;          -- cria
SHOW DATABASES;                -- lista
USE loja;                      -- seleciona para usar
DROP DATABASE loja;            -- apaga (cuidado!)
```

## Criando uma tabela
```sql
CREATE TABLE nome_tabela (
    coluna1 TIPO [restrições],
    coluna2 TIPO [restrições],
    ...
);
```

### Exemplo do slide: cadastro de clientes
Requisitos: CPF, nome, endereço completo, data de nascimento, idade, sexo, limite de crédito, volume mínimo de compra, se é primeira compra.

```sql
CREATE TABLE tb_clientes (
    cpf            VARCHAR(11),
    nome           VARCHAR(100),
    endereco1      VARCHAR(150),
    endereco2      VARCHAR(150),
    bairro         VARCHAR(50),
    cidade         VARCHAR(50),
    estado         VARCHAR(2),
    cep            VARCHAR(8),
    data_nascimento DATE,
    idade          INT,
    sexo           VARCHAR(1),
    limite_credito DECIMAL(10,2),
    volume_compra  DECIMAL(10,2),
    primeira_compra TINYINT(1)
);
```
> No slide original: `FLOAT` para valores e `NUMBER(1)` para primeira compra (Oracle). Aqui usamos `DECIMAL` e `TINYINT(1)`.

## Comandos úteis
| Comando | O que faz |
|---|---|
| `SHOW TABLES;` | lista as tabelas |
| `DESCRIBE tb_clientes;` | mostra colunas e tipos (metadados) |
| `SHOW CREATE TABLE tb_clientes;` | mostra o script de criação |
| `DROP TABLE tb_clientes;` | apaga a tabela inteira |
| `TRUNCATE TABLE tb_clientes;` | apaga **todos os registros** e zera o AUTO_INCREMENT (mantém a estrutura) |
| `CREATE TABLE copia LIKE original;` | copia a estrutura |
| `CREATE TABLE copia AS SELECT * FROM original;` | copia estrutura + dados |

## AUTO_INCREMENT
Gera automaticamente o número sequencial (típico para chave primária).
```sql
CREATE TABLE produto (
    id   INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL
);
```

## Boas práticas de nomenclatura
- Nomes em `snake_case`, sem acento e sem espaço.
- Tabelas no singular (`cliente`) ou prefixo (`tb_clientes`) — escolha um padrão e mantenha.
- Chave estrangeira: `<tabela>_id` (ex.: `cliente_id`).

---

## 💻 Código completo para testar

### criar-tabelas.sql

```sql
CREATE DATABASE IF NOT EXISTS estudos CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE estudos;

DROP TABLE IF EXISTS tb_clientes;

CREATE TABLE tb_clientes (
    cpf             VARCHAR(11),
    nome            VARCHAR(100),
    endereco1       VARCHAR(150),
    endereco2       VARCHAR(150),
    bairro          VARCHAR(50),
    cidade          VARCHAR(50),
    estado          VARCHAR(2),
    cep             VARCHAR(8),
    data_nascimento DATE,
    idade           INT,
    sexo            VARCHAR(1),
    limite_credito  DECIMAL(10,2),
    volume_compra   DECIMAL(10,2),
    primeira_compra TINYINT(1)
);

DESCRIBE tb_clientes;
SHOW CREATE TABLE tb_clientes;
```
