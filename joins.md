# JOINs (juntando tabelas)

Os dados ficam **separados** em tabelas (normalização). O `JOIN` **junta** as linhas relacionadas numa única consulta, normalmente combinando **PK = FK**.


## INNER JOIN
Só traz linhas que **têm correspondência nas duas tabelas**.
```sql
SELECT c.nome, p.id AS pedido, p.valor_total
FROM cliente c
INNER JOIN pedido p ON p.cliente_id = c.id;
```
Clientes **sem pedido** não aparecem.

## LEFT JOIN
Todas as linhas da tabela **da esquerda** + correspondentes da direita (NULL quando não há).
```sql
SELECT c.nome, p.id AS pedido
FROM cliente c
LEFT JOIN pedido p ON p.cliente_id = c.id;
```
Encontrar clientes **sem nenhum pedido**:
```sql
SELECT c.nome
FROM cliente c
LEFT JOIN pedido p ON p.cliente_id = c.id
WHERE p.id IS NULL;
```

## RIGHT JOIN
Inverso do LEFT: todas da **direita**.
```sql
SELECT c.nome, p.id
FROM cliente c
RIGHT JOIN pedido p ON p.cliente_id = c.id;
```
(Na prática, use LEFT trocando a ordem das tabelas.)

## FULL OUTER JOIN
O MySQL **não tem** `FULL JOIN`; simula-se com `UNION`:
```sql
SELECT c.nome, p.id FROM cliente c LEFT JOIN pedido p ON p.cliente_id = c.id
UNION
SELECT c.nome, p.id FROM cliente c RIGHT JOIN pedido p ON p.cliente_id = c.id;
```

## CROSS JOIN
Produto cartesiano: **toda combinação** (N × M linhas).
```sql
SELECT c.nome, pr.nome FROM cliente c CROSS JOIN produto pr;
```

## SELF JOIN
A tabela se junta **com ela mesma** (ex.: funcionário e seu gerente).
```sql
SELECT f.nome AS funcionario, g.nome AS gerente
FROM funcionario f
LEFT JOIN funcionario g ON g.id = f.gerente_id;
```

## Vários JOINs
```sql
SELECT c.nome, p.id, pr.nome AS produto, ip.quantidade
FROM cliente c
JOIN pedido p       ON p.cliente_id = c.id
JOIN item_pedido ip ON ip.pedido_id = p.id
JOIN produto pr     ON pr.id = ip.produto_id;
```

## Resumo visual
```
INNER:  A ∩ B
LEFT:   A inteira + (A ∩ B)
RIGHT:  B inteira + (A ∩ B)
FULL:   A ∪ B
CROSS:  A × B
```

## Dicas
- Use **aliases** (`c`, `p`) para escrever menos.
- Sempre escreva a condição `ON`; sem ela vira produto cartesiano.
- Colunas com mesmo nome em ambas as tabelas precisam do prefixo: `c.id`, `p.id`.
- `JOIN` sozinho = `INNER JOIN`.

---

## 💻 Código completo para testar

### joins.sql

```sql
CREATE DATABASE IF NOT EXISTS loja_joins CHARACTER SET utf8mb4;
USE loja_joins;

DROP TABLE IF EXISTS item_pedido; DROP TABLE IF EXISTS pedido;
DROP TABLE IF EXISTS produto; DROP TABLE IF EXISTS cliente; DROP TABLE IF EXISTS funcionario;

CREATE TABLE cliente (id INT PRIMARY KEY AUTO_INCREMENT, nome VARCHAR(50) NOT NULL);
CREATE TABLE produto (id INT PRIMARY KEY AUTO_INCREMENT, nome VARCHAR(50) NOT NULL, preco DECIMAL(10,2));
CREATE TABLE pedido (
    id INT PRIMARY KEY AUTO_INCREMENT,
    data_criacao DATE,
    valor_total DECIMAL(10,2),
    cliente_id INT NULL,
    FOREIGN KEY (cliente_id) REFERENCES cliente(id)
);
CREATE TABLE item_pedido (
    pedido_id INT, produto_id INT, quantidade INT,
    PRIMARY KEY (pedido_id, produto_id),
    FOREIGN KEY (pedido_id) REFERENCES pedido(id),
    FOREIGN KEY (produto_id) REFERENCES produto(id)
);
CREATE TABLE funcionario (id INT PRIMARY KEY AUTO_INCREMENT, nome VARCHAR(50), gerente_id INT NULL,
    FOREIGN KEY (gerente_id) REFERENCES funcionario(id));

INSERT INTO cliente (nome) VALUES ('João'),('Maria'),('Ana');          -- Ana não tem pedido
INSERT INTO produto (nome, preco) VALUES ('Mouse',50),('Teclado',120),('Monitor',900);
INSERT INTO pedido (data_criacao, valor_total, cliente_id) VALUES
 ('2014-08-10',343.23,1),('2014-08-12',180.22,1),('2014-08-15',99.90,2);
INSERT INTO item_pedido VALUES (1,1,2),(1,3,1),(2,2,1),(3,1,1);
INSERT INTO funcionario (nome, gerente_id) VALUES ('Diretor',NULL);
INSERT INTO funcionario (nome, gerente_id) VALUES ('Gerente A',1),('Analista X',2);

-- INNER
SELECT c.nome, p.id AS pedido, p.valor_total FROM cliente c INNER JOIN pedido p ON p.cliente_id = c.id;
-- LEFT (Ana aparece com NULL)
SELECT c.nome, p.id AS pedido FROM cliente c LEFT JOIN pedido p ON p.cliente_id = c.id;
-- Clientes sem pedido
SELECT c.nome FROM cliente c LEFT JOIN pedido p ON p.cliente_id = c.id WHERE p.id IS NULL;
-- RIGHT
SELECT c.nome, p.id FROM cliente c RIGHT JOIN pedido p ON p.cliente_id = c.id;
-- FULL (simulado)
SELECT c.nome, p.id FROM cliente c LEFT JOIN pedido p ON p.cliente_id = c.id
UNION
SELECT c.nome, p.id FROM cliente c RIGHT JOIN pedido p ON p.cliente_id = c.id;
-- CROSS
SELECT c.nome, pr.nome FROM cliente c CROSS JOIN produto pr;
-- SELF
SELECT f.nome AS funcionario, g.nome AS gerente FROM funcionario f LEFT JOIN funcionario g ON g.id = f.gerente_id;
-- Vários joins
SELECT c.nome AS cliente, p.id AS pedido, pr.nome AS produto, ip.quantidade
FROM cliente c
JOIN pedido p ON p.cliente_id = c.id
JOIN item_pedido ip ON ip.pedido_id = p.id
JOIN produto pr ON pr.id = ip.produto_id;
```
