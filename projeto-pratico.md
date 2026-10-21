# Projeto Prático: Loja Virtual

Junta **tudo** que foi visto: modelagem, constraints, relacionamentos, inserts, joins, agrupamentos, views, índices, transações, triggers.

## Modelo (DER simplificado)
```
categoria (1) ──< produto (N) >── (N) pedido  ── via item_pedido
cliente   (1) ──< pedido (N)
cliente   (1) ──< telefone (N)
```

## Passo a passo
Execute na ordem:

## Consultas que você vai praticar
- Pedidos com nome do cliente (JOIN)
- Faturamento por mês e por categoria (GROUP BY)
- Top 3 clientes que mais compraram
- Clientes sem pedidos (LEFT JOIN + IS NULL)
- Produtos mais vendidos
- Produtos com estoque baixo
- Ticket médio

---

## 💻 Código completo para testar

### 01-schema.sql

```sql
DROP DATABASE IF EXISTS loja;
CREATE DATABASE loja CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE loja;

CREATE TABLE categoria (
    id   INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE produto (
    id           INT AUTO_INCREMENT PRIMARY KEY,
    nome         VARCHAR(100) NOT NULL,
    preco        DECIMAL(10,2) NOT NULL CHECK (preco > 0),
    estoque      INT NOT NULL DEFAULT 0 CHECK (estoque >= 0),
    categoria_id INT NOT NULL,
    ativo        BOOLEAN NOT NULL DEFAULT TRUE,
    CONSTRAINT fk_produto_categoria FOREIGN KEY (categoria_id) REFERENCES categoria(id)
);

CREATE TABLE cliente (
    id              INT AUTO_INCREMENT PRIMARY KEY,
    cpf             CHAR(11) NOT NULL UNIQUE,
    nome            VARCHAR(100) NOT NULL,
    email           VARCHAR(100) UNIQUE,
    data_nascimento DATE,
    cidade          VARCHAR(50),
    estado          CHAR(2),
    criado_em       DATETIME DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE telefone (
    id         INT AUTO_INCREMENT PRIMARY KEY,
    cliente_id INT NOT NULL,
    numero     VARCHAR(15) NOT NULL,
    CONSTRAINT fk_tel_cliente FOREIGN KEY (cliente_id) REFERENCES cliente(id) ON DELETE CASCADE
);

CREATE TABLE pedido (
    id           INT AUTO_INCREMENT PRIMARY KEY,
    cliente_id   INT NOT NULL,
    data_pedido  DATE NOT NULL,
    status       VARCHAR(20) NOT NULL DEFAULT 'Pendente'
                 CHECK (status IN ('Pendente','Pago','Enviado','Entregue','Cancelado')),
    valor_frete  DECIMAL(10,2) NOT NULL DEFAULT 0,
    CONSTRAINT fk_pedido_cliente FOREIGN KEY (cliente_id) REFERENCES cliente(id)
);

CREATE TABLE item_pedido (
    pedido_id      INT,
    produto_id     INT,
    quantidade     INT NOT NULL CHECK (quantidade > 0),
    preco_unitario DECIMAL(10,2) NOT NULL,
    PRIMARY KEY (pedido_id, produto_id),
    CONSTRAINT fk_item_pedido  FOREIGN KEY (pedido_id)  REFERENCES pedido(id) ON DELETE CASCADE,
    CONSTRAINT fk_item_produto FOREIGN KEY (produto_id) REFERENCES produto(id)
);
```

### 02-dados.sql

```sql
USE loja;

INSERT INTO categoria (nome) VALUES ('Periféricos'),('Monitores'),('Acessórios');

INSERT INTO produto (nome, preco, estoque, categoria_id) VALUES
 ('Mouse Gamer',      89.90, 50, 1),
 ('Teclado Mecânico',249.90, 30, 1),
 ('Monitor 24"',     799.00, 10, 2),
 ('Monitor 27"',    1299.00,  4, 2),
 ('Cabo HDMI',        29.90,100, 3),
 ('Mousepad',         39.90,  3, 3);

INSERT INTO cliente (cpf, nome, email, data_nascimento, cidade, estado) VALUES
 ('11111111111','João Silva',  'joao@email.com', '1990-10-05','São Paulo','SP'),
 ('22222222222','Maria Santos','maria@email.com','1995-03-20','Campinas','SP'),
 ('33333333333','Carlos Lima', 'carlos@email.com','1985-10-30','Rio de Janeiro','RJ'),
 ('44444444444','Ana Pereira', 'ana@email.com',  '2000-07-14','Belo Horizonte','MG');

INSERT INTO telefone (cliente_id, numero) VALUES (1,'11999990001'),(1,'1133330001'),(2,'19999990002');

INSERT INTO pedido (cliente_id, data_pedido, status, valor_frete) VALUES
 (1,'2025-01-10','Entregue',20),
 (1,'2025-02-15','Pago',15),
 (2,'2025-02-20','Enviado',25),
 (3,'2025-03-05','Cancelado',0),
 (3,'2025-03-18','Entregue',30);

INSERT INTO item_pedido (pedido_id, produto_id, quantidade, preco_unitario) VALUES
 (1,1,2, 89.90),(1,5,3,29.90),
 (2,3,1,799.00),
 (3,2,1,249.90),(3,6,2,39.90),
 (4,4,1,1299.00),
 (5,3,2,799.00),(5,1,1,89.90);
```

### 03-consultas.sql

```sql
USE loja;

-- 1) Pedidos com cliente e total (itens + frete)
SELECT p.id, c.nome AS cliente, p.data_pedido, p.status,
       SUM(i.quantidade * i.preco_unitario) AS subtotal,
       p.valor_frete,
       SUM(i.quantidade * i.preco_unitario) + p.valor_frete AS total
FROM pedido p
JOIN cliente c     ON c.id = p.cliente_id
JOIN item_pedido i ON i.pedido_id = p.id
GROUP BY p.id, c.nome, p.data_pedido, p.status, p.valor_frete
ORDER BY p.data_pedido;

-- 2) Faturamento por mês (ignorando cancelados)
SELECT DATE_FORMAT(p.data_pedido,'%Y-%m') AS mes,
       COUNT(DISTINCT p.id) AS qtd_pedidos,
       SUM(i.quantidade * i.preco_unitario) AS faturamento
FROM pedido p JOIN item_pedido i ON i.pedido_id = p.id
WHERE p.status <> 'Cancelado'
GROUP BY mes ORDER BY mes;

-- 3) Faturamento por categoria
SELECT cat.nome AS categoria, SUM(i.quantidade * i.preco_unitario) AS total
FROM item_pedido i
JOIN produto pr   ON pr.id = i.produto_id
JOIN categoria cat ON cat.id = pr.categoria_id
JOIN pedido p     ON p.id = i.pedido_id AND p.status <> 'Cancelado'
GROUP BY cat.nome ORDER BY total DESC;

-- 4) Top 3 clientes que mais gastaram
SELECT c.nome, SUM(i.quantidade * i.preco_unitario) AS gasto
FROM cliente c
JOIN pedido p ON p.cliente_id = c.id AND p.status <> 'Cancelado'
JOIN item_pedido i ON i.pedido_id = p.id
GROUP BY c.id, c.nome ORDER BY gasto DESC LIMIT 3;

-- 5) Clientes sem pedidos
SELECT c.nome FROM cliente c LEFT JOIN pedido p ON p.cliente_id = c.id WHERE p.id IS NULL;

-- 6) Produtos mais vendidos
SELECT pr.nome, SUM(i.quantidade) AS qtd_vendida
FROM produto pr JOIN item_pedido i ON i.produto_id = pr.id
GROUP BY pr.id, pr.nome ORDER BY qtd_vendida DESC;

-- 7) Produtos com estoque baixo
SELECT nome, estoque FROM produto WHERE estoque <= 5 ORDER BY estoque;

-- 8) Ticket médio
SELECT AVG(total) AS ticket_medio FROM (
    SELECT p.id, SUM(i.quantidade * i.preco_unitario) AS total
    FROM pedido p JOIN item_pedido i ON i.pedido_id = p.id
    WHERE p.status <> 'Cancelado' GROUP BY p.id
) t;

-- 9) Aniversariantes do mês
SELECT nome, DATE_FORMAT(data_nascimento,'%d/%m') AS aniversario
FROM cliente WHERE MONTH(data_nascimento) = MONTH(CURDATE());

-- 10) Clientes e seus telefones numa linha só
SELECT c.nome, GROUP_CONCAT(t.numero SEPARATOR ', ') AS telefones
FROM cliente c LEFT JOIN telefone t ON t.cliente_id = c.id
GROUP BY c.id, c.nome;
```

### 04-extras.sql

```sql
USE loja;

-- VIEW: resumo dos pedidos
CREATE OR REPLACE VIEW vw_resumo_pedidos AS
SELECT p.id AS pedido, c.nome AS cliente, p.data_pedido, p.status,
       SUM(i.quantidade * i.preco_unitario) + p.valor_frete AS total
FROM pedido p
JOIN cliente c ON c.id = p.cliente_id
JOIN item_pedido i ON i.pedido_id = p.id
GROUP BY p.id, c.nome, p.data_pedido, p.status, p.valor_frete;

SELECT * FROM vw_resumo_pedidos WHERE status = 'Entregue';

-- ÍNDICES para consultas frequentes
CREATE INDEX idx_pedido_data ON pedido (data_pedido);
CREATE INDEX idx_cliente_nome ON cliente (nome);

-- TRIGGER: baixa de estoque ao inserir item
DROP TRIGGER IF EXISTS trg_baixa_estoque;
DELIMITER //
CREATE TRIGGER trg_baixa_estoque AFTER INSERT ON item_pedido FOR EACH ROW
BEGIN
    UPDATE produto SET estoque = estoque - NEW.quantidade WHERE id = NEW.produto_id;
END //
DELIMITER ;

-- TRANSAÇÃO: novo pedido completo (tudo ou nada)
START TRANSACTION;
INSERT INTO pedido (cliente_id, data_pedido, valor_frete) VALUES (4, CURDATE(), 10);
SET @novo = LAST_INSERT_ID();
INSERT INTO item_pedido (pedido_id, produto_id, quantidade, preco_unitario) VALUES (@novo, 1, 1, 89.90);
INSERT INTO item_pedido (pedido_id, produto_id, quantidade, preco_unitario) VALUES (@novo, 5, 2, 29.90);
COMMIT;

SELECT nome, estoque FROM produto;  -- estoque foi baixado pelo trigger
```
