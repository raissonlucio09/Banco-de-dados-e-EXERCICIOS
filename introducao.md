# Introdução a Banco de Dados

## O que é um Banco de Dados?
Uma **coleção organizada de dados** que se relacionam de forma a criar sentido (**informação**) e tornar as pesquisas mais eficientes. Pode armazenar dados sobre lugares, pessoas ou coisas.

Exemplo — tabela **Pessoa**:

| nome | email | telefone | data_nascimento |
|---|---|---|---|
| João Silva | joao@joao.com | 3232322 | 1997-08-10 |
| Maria Santos | maria@maria.com | 3222112 | 1999-05-22 |

## Metadados
São os **dados que descrevem os dados**. Na tabela acima, os metadados são os nomes das colunas (`nome`, `email`, `telefone`, `data_nascimento`) e seus tipos. Os valores (João, Maria...) são os **dados**.

## SGBD (DBMS)
**Sistema Gerenciador de Banco de Dados**: software que cria, organiza e controla o acesso aos bancos de dados.

Exemplos: **MySQL**, Oracle, PostgreSQL, SQL Server, MariaDB, Firebird, DB2, Microsoft Access.

## Hierarquia
```
SGBD (servidor MySQL)
 └── Banco de Dados (database / schema)
      └── Tabelas
           └── Registros (linhas) e Colunas (campos)
```

## Modelo relacional
As tabelas se **relacionam** entre si. Exemplo: um **Departamento** tem **vários Empregados** (1 : N).

```
Departamento (1) ─────────── (N) Empregado
```

## SQL
**Structured Query Language** — linguagem padrão para conversar com bancos relacionais. Serve para inserir, consultar, alterar, apagar, criar estruturas, controlar usuários e transações.

### Grupos de comandos SQL
| Grupo | Nome | Comandos | Para quê |
|---|---|---|---|
| **DDL** | Data Definition Language | `CREATE`, `ALTER`, `DROP`, `TRUNCATE` | Definir a estrutura |
| **DML** | Data Manipulation Language | `INSERT`, `UPDATE`, `DELETE` | Manipular dados |
| **DQL** | Data Query Language | `SELECT` | Consultar dados |
| **DCL** | Data Control Language | `GRANT`, `REVOKE` | Permissões |
| **TCL** | Transaction Control Language | `COMMIT`, `ROLLBACK`, `SAVEPOINT` | Transações |

## CRUD
Quatro operações básicas de qualquer sistema:

| Letra | Significado | SQL |
|---|---|---|
| **C** | Create | `INSERT` |
| **R** | Read | `SELECT` |
| **U** | Update | `UPDATE` |
| **D** | Delete | `DELETE` |

---

## 💻 Código completo para testar

### crud-basico.sql

```sql
-- Primeiro contato com SQL: um CRUD completo

CREATE DATABASE IF NOT EXISTS estudos;
USE estudos;

CREATE TABLE pessoa (
    nome            VARCHAR(100),
    email           VARCHAR(100),
    telefone        VARCHAR(15),
    data_nascimento DATE
);

-- C (Create)
INSERT INTO pessoa VALUES ('João Silva',   'joao@joao.com',   '3232322', '1997-08-10');
INSERT INTO pessoa VALUES ('Maria Santos', 'maria@maria.com', '3222112', '1999-05-22');

-- R (Read)
SELECT nome FROM pessoa;          -- "Busque o nome da tabela pessoa"
SELECT * FROM pessoa;             -- todas as colunas

-- U (Update)
UPDATE pessoa SET telefone = '3999999' WHERE nome = 'João Silva';

-- D (Delete)
DELETE FROM pessoa WHERE nome = 'Maria Santos';
```
