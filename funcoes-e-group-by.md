# Funções, GROUP BY e HAVING

## Funções de agregação
Resumem várias linhas em **um valor**.

| Função | Faz |
|---|---|
| `COUNT(*)` | conta linhas |
| `COUNT(col)` | conta valores **não nulos** |
| `SUM(col)` | soma |
| `AVG(col)` | média |
| `MIN(col)` / `MAX(col)` | menor / maior |

```sql
SELECT COUNT(*) AS total_clientes FROM tb_clientes;
SELECT SUM(volume_compra) AS total_comprado FROM tb_clientes;
SELECT AVG(idade) AS media_idade FROM tb_clientes;
SELECT MIN(limite_credito), MAX(limite_credito) FROM tb_clientes;
SELECT COUNT(DISTINCT estado) AS qtd_estados FROM tb_clientes;
```

## GROUP BY
Agrupa linhas com o mesmo valor para aplicar agregações **por grupo**.
```sql
SELECT estado, COUNT(*) AS qtd, AVG(limite_credito) AS media_limite
FROM tb_clientes
GROUP BY estado;
```
> Toda coluna no SELECT que **não** é agregação deve estar no GROUP BY.

## HAVING
Filtra **grupos** (depois do agrupamento). `WHERE` filtra **linhas** (antes).
```sql
SELECT estado, COUNT(*) AS qtd
FROM tb_clientes
GROUP BY estado
HAVING COUNT(*) >= 2;
```

### WHERE × HAVING
| | WHERE | HAVING |
|---|---|---|
| Filtra | linhas | grupos |
| Momento | antes do GROUP BY | depois |
| Usa agregação? | não | sim |

### Ordem de execução
`FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT`

## Funções de texto
```sql
SELECT UPPER(nome), LOWER(nome), LENGTH(nome) FROM tb_clientes;
SELECT CONCAT(nome, ' - ', cidade) AS ficha FROM tb_clientes;
SELECT SUBSTRING(cpf, 1, 3) AS inicio_cpf FROM tb_clientes;
SELECT TRIM('  oi  '), REPLACE('a-b-c','-','/');
```

## Funções numéricas
```sql
SELECT ROUND(3.14159, 2), CEIL(2.1), FLOOR(2.9), ABS(-5), MOD(10,3);
```

## Funções de data
```sql
SELECT NOW(), CURDATE(), CURTIME();
SELECT YEAR(data_nascimento), MONTH(data_nascimento), DAY(data_nascimento) FROM tb_clientes;
SELECT DATE_ADD(CURDATE(), INTERVAL 30 DAY);
SELECT DATEDIFF('2025-12-25', CURDATE()) AS dias_pro_natal;
SELECT TIMESTAMPDIFF(YEAR, data_nascimento, CURDATE()) AS idade FROM tb_clientes;
SELECT DATE_FORMAT(NOW(), '%d/%m/%Y %H:%i');
```

## Tratamento de NULL
```sql
SELECT IFNULL(endereco2, 'Não informado') FROM tb_clientes;
SELECT COALESCE(endereco2, endereco1, 'Sem endereço') FROM tb_clientes;
```

## CASE (if/else dentro do SELECT)
```sql
SELECT nome, limite_credito,
  CASE
    WHEN limite_credito >= 10000 THEN 'Premium'
    WHEN limite_credito >= 5000  THEN 'Gold'
    ELSE 'Standard'
  END AS categoria
FROM tb_clientes;
```

## Exemplo no trabalho: relatório de vendas por mês
```sql
SELECT DATE_FORMAT(data_criacao, '%Y-%m') AS mes,
       COUNT(*)          AS qtd_pedidos,
       SUM(valor_total)  AS faturamento
FROM pedido
GROUP BY mes
ORDER BY mes;
```

---

## 💻 Código completo para testar

### funcoes.sql

```sql
USE estudos;

SELECT COUNT(*) AS total_clientes FROM tb_clientes;
SELECT SUM(volume_compra) AS total, AVG(idade) AS media_idade, MIN(limite_credito) AS menor, MAX(limite_credito) AS maior FROM tb_clientes;

SELECT estado, COUNT(*) AS qtd, AVG(limite_credito) AS media_limite
FROM tb_clientes GROUP BY estado;

SELECT estado, COUNT(*) AS qtd FROM tb_clientes GROUP BY estado HAVING COUNT(*) >= 2;

SELECT UPPER(nome), CONCAT(nome,' - ',cidade) AS ficha FROM tb_clientes;
SELECT nome, IFNULL(endereco2,'Não informado') AS end2 FROM tb_clientes;

SELECT nome, limite_credito,
  CASE WHEN limite_credito >= 10000 THEN 'Premium'
       WHEN limite_credito >= 5000  THEN 'Gold'
       ELSE 'Standard' END AS categoria
FROM tb_clientes;
```
