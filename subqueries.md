# Subqueries (subconsultas)

Uma **consulta dentro de outra**, entre parênteses.

## No WHERE
```sql
-- Clientes com limite acima da média
SELECT nome, limite_credito
FROM tb_clientes
WHERE limite_credito > (SELECT AVG(limite_credito) FROM tb_clientes);
```

## Com IN / NOT IN
```sql
-- Clientes que fizeram pedidos
SELECT nome FROM cliente
WHERE id IN (SELECT cliente_id FROM pedido);

-- Clientes sem pedidos
SELECT nome FROM cliente
WHERE id NOT IN (SELECT cliente_id FROM pedido WHERE cliente_id IS NOT NULL);
```
> `NOT IN` com NULL na subquery retorna **vazio**! Filtre os NULLs ou use `NOT EXISTS`.

## Com EXISTS
```sql
SELECT c.nome FROM cliente c
WHERE EXISTS (SELECT 1 FROM pedido p WHERE p.cliente_id = c.id);
```

## No FROM (tabela derivada — precisa de alias)
```sql
SELECT estado, media
FROM (SELECT estado, AVG(limite_credito) AS media
      FROM tb_clientes GROUP BY estado) AS t
WHERE media > 4000;
```

## No SELECT (subconsulta escalar)
```sql
SELECT nome,
       (SELECT COUNT(*) FROM pedido p WHERE p.cliente_id = c.id) AS qtd_pedidos
FROM cliente c;
```

## ANY / ALL
```sql
SELECT * FROM produto WHERE preco > ALL (SELECT preco FROM produto WHERE nome LIKE 'Cabo%');
```

## CTE (WITH) — MySQL 8+
Deixa consultas complexas mais legíveis.
```sql
WITH total_por_cliente AS (
    SELECT cliente_id, SUM(valor_total) AS total
    FROM pedido GROUP BY cliente_id
)
SELECT c.nome, t.total
FROM cliente c JOIN total_por_cliente t ON t.cliente_id = c.id
WHERE t.total > 200;
```

## UNION
Junta resultados de dois SELECTs (mesmo nº de colunas).
```sql
SELECT nome FROM cliente UNION SELECT nome FROM funcionario;      -- sem repetidos
SELECT nome FROM cliente UNION ALL SELECT nome FROM funcionario;  -- com repetidos
```

---

## 💻 Código completo para testar

### subqueries.sql

```sql
USE loja_joins;

SELECT nome FROM cliente WHERE id IN (SELECT cliente_id FROM pedido);
SELECT nome FROM cliente WHERE id NOT IN (SELECT cliente_id FROM pedido WHERE cliente_id IS NOT NULL);
SELECT c.nome FROM cliente c WHERE EXISTS (SELECT 1 FROM pedido p WHERE p.cliente_id = c.id);

SELECT nome, (SELECT COUNT(*) FROM pedido p WHERE p.cliente_id = c.id) AS qtd_pedidos FROM cliente c;

SELECT * FROM pedido WHERE valor_total > (SELECT AVG(valor_total) FROM pedido);

WITH total_por_cliente AS (
    SELECT cliente_id, SUM(valor_total) AS total FROM pedido GROUP BY cliente_id
)
SELECT c.nome, t.total FROM cliente c JOIN total_por_cliente t ON t.cliente_id = c.id;
```
