# 📚 Banco de Dados com MySQL — Guia de Estudos

Repositório de estudos de **Modelagem de Banco de Dados** e **SQL (MySQL)**, baseado nas aulas do Prof. Anderson Barros (UNINOVE) e complementado com tópicos extras.

> Cada arquivo explica um assunto e já traz os códigos de exemplo no final (seção "Código completo para testar").

## 🗂️ Índice

| Arquivo | Assunto |
|---|---|
| [introducao](./introducao.md) | O que é banco de dados, SGBD, metadados, CRUD, SQL |
| [modelagem-de-dados](./modelagem-de-dados.md) | Modelo conceitual, lógico e físico, DER |
| [tipos-de-dados](./tipos-de-dados.md) | INT, VARCHAR, DECIMAL, DATE... |
| [create-table](./create-table.md) | Criando bancos e tabelas (DDL) |
| [insert](./insert.md) | Inserindo dados |
| [select](./select.md) | Consultas, WHERE, LIKE, ORDER BY, datas |
| [update-e-delete](./update-e-delete.md) | Alterando e apagando registros |
| [alter-table](./alter-table.md) | Alterando a estrutura das tabelas |
| [constraints](./constraints.md) | PK, FK, UNIQUE, NOT NULL, CHECK, DEFAULT |
| [chaves-e-relacionamentos](./chaves-e-relacionamentos.md) | 1:1, 1:N, N:N, ON DELETE/UPDATE |
| [joins](./joins.md) | INNER, LEFT, RIGHT, CROSS, SELF JOIN |
| [funcoes-e-group-by](./funcoes-e-group-by.md) | COUNT, SUM, AVG, GROUP BY, HAVING, funções |
| [subqueries](./subqueries.md) | Consultas dentro de consultas |
| [views](./views.md) | Visões |
| [indices](./indices.md) | Índices e performance (EXPLAIN) |
| [transacoes](./transacoes.md) | COMMIT, ROLLBACK, ACID |
| [normalizacao](./normalizacao.md) | 1FN, 2FN, 3FN |
| [procedures-functions-triggers](./procedures-functions-triggers.md) | Rotinas programáveis |
| [usuarios-e-permissoes](./usuarios-e-permissoes.md) | DCL: GRANT e REVOKE |
| [projeto-pratico](./projeto-pratico.md) | Projeto completo (loja) usando tudo |
| [meus-exercicios](./meus-exercicios.md) | Seus exercícios resolvidos |

## ▶️ Como praticar

- Instale o **MySQL Server + MySQL Workbench**, ou use o **XAMPP/Docker**.
- Alternativa online: [db-fiddle.com](https://www.db-fiddle.com) ou [sqliteonline.com](https://sqliteonline.com) (escolha MySQL).
- Copie os códigos de cada arquivo e rode na ordem da tabela.

## ⚠️ Observação importante: Oracle x MySQL

Parte dos slides usa **Oracle** (Live SQL). Aqui tudo foi adaptado para **MySQL**. Principais diferenças:

| Oracle (slides) | MySQL (neste repositório) |
|---|---|
| `VARCHAR2(100)` | `VARCHAR(100)` |
| `NUMBER(10,2)` | `DECIMAL(10,2)` |
| `NUMBER(1)` | `TINYINT(1)` / `BOOLEAN` |
| `TO_DATE('07/10/1995','DD/MM/YYYY')` | `STR_TO_DATE('07/10/1995','%d/%m/%Y')` |
| `TO_CHAR(data,'MM')` | `DATE_FORMAT(data,'%m')` ou `MONTH(data)` |
| `ALTER TABLE t MODIFY col NOT NULL` | `ALTER TABLE t MODIFY col tipo NOT NULL` (tem que repetir o tipo) |
| `DROP CONSTRAINT` (PK) | `DROP PRIMARY KEY` |
| `DROP CONSTRAINT` (FK) | `DROP FOREIGN KEY nome` |
| Sequence / IDENTITY | `AUTO_INCREMENT` |
