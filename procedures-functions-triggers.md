# Stored Procedures, Functions e Triggers

Rotinas guardadas **dentro do banco**.

> Em clientes de linha de comando, troque o delimitador (`DELIMITER //`) para o `;` interno não encerrar o bloco.

## Stored Procedure
Bloco reutilizável executado com `CALL`.
```sql
DELIMITER //
CREATE PROCEDURE sp_clientes_por_estado(IN p_estado VARCHAR(2))
BEGIN
    SELECT * FROM tb_clientes WHERE estado = p_estado;
END //
DELIMITER ;

CALL sp_clientes_por_estado('SP');
```
Parâmetros: `IN` (entrada), `OUT` (saída), `INOUT` (ambos).
```sql
DELIMITER //
CREATE PROCEDURE sp_total_clientes(OUT p_total INT)
BEGIN
    SELECT COUNT(*) INTO p_total FROM tb_clientes;
END //
DELIMITER ;

CALL sp_total_clientes(@qtd);
SELECT @qtd;
```

## Function
Retorna **um valor** e pode ser usada dentro do SELECT.
```sql
DELIMITER //
CREATE FUNCTION fn_idade(p_nasc DATE) RETURNS INT
DETERMINISTIC
BEGIN
    RETURN TIMESTAMPDIFF(YEAR, p_nasc, CURDATE());
END //
DELIMITER ;

SELECT nome, fn_idade(data_nascimento) AS idade FROM tb_clientes;
```

## Estruturas de controle
```sql
IF x > 10 THEN ... ELSEIF ... ELSE ... END IF;
WHILE i < 5 DO ... SET i = i + 1; END WHILE;
DECLARE v INT DEFAULT 0;
```

## Trigger (gatilho)
Executa **automaticamente** antes/depois de `INSERT`, `UPDATE` ou `DELETE`.
`NEW` = linha nova; `OLD` = linha antiga.

### Auditoria: registrar alterações
```sql
CREATE TABLE log_preco (
    id INT AUTO_INCREMENT PRIMARY KEY,
    produto_id INT, preco_antigo DECIMAL(10,2), preco_novo DECIMAL(10,2),
    alterado_em DATETIME DEFAULT CURRENT_TIMESTAMP
);

DELIMITER //
CREATE TRIGGER trg_produto_preco
AFTER UPDATE ON produto
FOR EACH ROW
BEGIN
    IF OLD.preco <> NEW.preco THEN
        INSERT INTO log_preco (produto_id, preco_antigo, preco_novo)
        VALUES (OLD.id, OLD.preco, NEW.preco);
    END IF;
END //
DELIMITER ;
```

### Validação: BEFORE INSERT
```sql
DELIMITER //
CREATE TRIGGER trg_valida_idade
BEFORE INSERT ON funcionarios
FOR EACH ROW
BEGIN
    IF NEW.idade < 18 THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Funcionário deve ter 18+ anos';
    END IF;
END //
DELIMITER ;
```

## Gerenciar
```sql
SHOW PROCEDURE STATUS WHERE Db = 'estudos';
SHOW TRIGGERS;
DROP PROCEDURE IF EXISTS sp_clientes_por_estado;
DROP FUNCTION IF EXISTS fn_idade;
DROP TRIGGER IF EXISTS trg_produto_preco;
```

## Cuidados
Triggers "escondem" lógica e dificultam debug; use com moderação. Regras de negócio complexas geralmente ficam na aplicação, deixando o banco para integridade e auditoria.

---

## 💻 Código completo para testar

### rotinas.sql

```sql
CREATE DATABASE IF NOT EXISTS rotinas_demo CHARACTER SET utf8mb4;
USE rotinas_demo;

DROP TABLE IF EXISTS log_preco; DROP TABLE IF EXISTS produto;
CREATE TABLE produto (id INT PRIMARY KEY AUTO_INCREMENT, nome VARCHAR(50), preco DECIMAL(10,2), nascimento DATE);
CREATE TABLE log_preco (
    id INT AUTO_INCREMENT PRIMARY KEY, produto_id INT,
    preco_antigo DECIMAL(10,2), preco_novo DECIMAL(10,2),
    alterado_em DATETIME DEFAULT CURRENT_TIMESTAMP
);
INSERT INTO produto (nome, preco) VALUES ('Mouse', 50), ('Teclado', 120);

DROP PROCEDURE IF EXISTS sp_produtos_acima;
DROP FUNCTION IF EXISTS fn_idade;
DROP TRIGGER IF EXISTS trg_produto_preco;

DELIMITER //
CREATE PROCEDURE sp_produtos_acima(IN p_preco DECIMAL(10,2))
BEGIN
    SELECT * FROM produto WHERE preco > p_preco;
END //

CREATE FUNCTION fn_idade(p_nasc DATE) RETURNS INT DETERMINISTIC
BEGIN
    RETURN TIMESTAMPDIFF(YEAR, p_nasc, CURDATE());
END //

CREATE TRIGGER trg_produto_preco AFTER UPDATE ON produto FOR EACH ROW
BEGIN
    IF OLD.preco <> NEW.preco THEN
        INSERT INTO log_preco (produto_id, preco_antigo, preco_novo) VALUES (OLD.id, OLD.preco, NEW.preco);
    END IF;
END //
DELIMITER ;

CALL sp_produtos_acima(60);
SELECT fn_idade('1995-10-07') AS idade;
UPDATE produto SET preco = 65 WHERE id = 1;
SELECT * FROM log_preco;
```
