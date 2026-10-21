# Views (Visões)

Uma **view** é uma consulta salva com nome; funciona como uma "tabela virtual". Não guarda dados, executa o SELECT toda vez.

 Para que serve
- **Simplificar** consultas complexas (JOINs enormes) — escreve uma vez, usa sempre.
- **Segurança**: expõe só certas colunas (ex.: esconder salário).
- **Padronizar** relatórios.

 Criar
```sql
CREATE VIEW vw_pedidos_clientes AS
SELECT p.id AS pedido, c.nome AS cliente, p.data_criacao, p.valor_total
FROM pedido p
JOIN cliente c ON c.id = p.cliente_id;
```

Usar
```sql
SELECT * FROM vw_pedidos_clientes;
SELECT * FROM vw_pedidos_clientes WHERE valor_total > 200 ORDER BY data_criacao;
```

 Alterar / apagar
```sql
CREATE OR REPLACE VIEW vw_pedidos_clientes AS SELECT ...;
ALTER VIEW vw_pedidos_clientes AS SELECT ...;
DROP VIEW IF EXISTS vw_pedidos_clientes;
SHOW FULL TABLES WHERE Table_type = 'VIEW';   -- lista views
```

View para esconder dados sensíveis
```sql
CREATE VIEW vw_funcionario_publico AS
SELECT id, nome, cargo FROM funcionario;      -- sem salário
```

Atualizando por view
Views simples (1 tabela, sem GROUP BY/DISTINCT/agregação) permitem `INSERT/UPDATE/DELETE`. Com `WITH CHECK OPTION` impede-se gravar linhas que a view não enxergaria:
```sql
CREATE VIEW vw_clientes_sp AS
SELECT * FROM tb_clientes WHERE estado = 'SP'
WITH CHECK OPTION;
```

---



views.sql

```sql
USE loja_joins;

CREATE OR REPLACE VIEW vw_pedidos_clientes AS
SELECT p.id AS pedido, c.nome AS cliente, p.data_criacao, p.valor_total
FROM pedido p JOIN cliente c ON c.id = p.cliente_id;

SELECT * FROM vw_pedidos_clientes;
SELECT * FROM vw_pedidos_clientes WHERE valor_total > 100 ORDER BY data_criacao;

CREATE OR REPLACE VIEW vw_resumo_cliente AS
SELECT c.id, c.nome, COUNT(p.id) AS qtd_pedidos, IFNULL(SUM(p.valor_total),0) AS total_gasto
FROM cliente c LEFT JOIN pedido p ON p.cliente_id = c.id
GROUP BY c.id, c.nome;

SELECT * FROM vw_resumo_cliente;
-- DROP VIEW vw_resumo_cliente;
```
