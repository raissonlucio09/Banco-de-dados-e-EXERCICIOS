# Transações (TCL) e ACID

Uma **transação** é um conjunto de operações tratado como **uma unidade**: ou **tudo** acontece, ou **nada**.

Exemplo clássico: transferência bancária — debitar de A **e** creditar em B. Se uma falhar, a outra deve ser desfeita.

## Propriedades ACID
| Letra | Propriedade | Significado |
|---|---|---|
| A | Atomicidade | tudo ou nada |
| C | Consistência | respeita as regras/constraints |
| I | Isolamento | transações simultâneas não se atrapalham |
| D | Durabilidade | depois do COMMIT, os dados persistem |

> Requer engine **InnoDB** (padrão no MySQL moderno). MyISAM não tem transações.

## Comandos
```sql
START TRANSACTION;     -- (ou BEGIN) inicia
-- ... comandos ...
COMMIT;                -- confirma
ROLLBACK;              -- desfaz tudo desde o START
SAVEPOINT ponto1;      -- marca um ponto
ROLLBACK TO ponto1;    -- volta só até o ponto
```

## Autocommit
Por padrão cada comando já é confirmado sozinho (`autocommit = 1`). `START TRANSACTION` suspende isso até o COMMIT/ROLLBACK.
```sql
SELECT @@autocommit;
SET autocommit = 0;
```

## Exemplo: transferência
```sql
START TRANSACTION;
UPDATE conta SET saldo = saldo - 100 WHERE id = 1;
UPDATE conta SET saldo = saldo + 100 WHERE id = 2;
-- conferiu que está tudo certo?
COMMIT;
-- se algo deu errado: ROLLBACK;
```

## Usando como rede de segurança
Antes de um `DELETE`/`UPDATE` arriscado: `START TRANSACTION;` → rode → confira com `SELECT` → `ROLLBACK;` ou `COMMIT;`.

## Níveis de isolamento
`READ UNCOMMITTED` < `READ COMMITTED` < `REPEATABLE READ` (padrão do MySQL) < `SERIALIZABLE`.
```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;
```
Problemas evitados: *dirty read*, *non-repeatable read*, *phantom read*.

## Atenção
Comandos **DDL** (`CREATE`, `ALTER`, `DROP`, `TRUNCATE`) fazem **commit implícito** no MySQL — não dá para dar rollback neles.

---

## 💻 Código completo para testar

### transacoes.sql

```sql
CREATE DATABASE IF NOT EXISTS banco_demo;
USE banco_demo;

DROP TABLE IF EXISTS conta;
CREATE TABLE conta (
    id    INT PRIMARY KEY,
    dono  VARCHAR(50) NOT NULL,
    saldo DECIMAL(10,2) NOT NULL CHECK (saldo >= 0)
) ENGINE=InnoDB;

INSERT INTO conta VALUES (1,'Ana',500),(2,'Bruno',200);

-- Transferência com sucesso
START TRANSACTION;
UPDATE conta SET saldo = saldo - 100 WHERE id = 1;
UPDATE conta SET saldo = saldo + 100 WHERE id = 2;
COMMIT;
SELECT * FROM conta;

-- Desfazendo
START TRANSACTION;
UPDATE conta SET saldo = 0;
SELECT * FROM conta;   -- ops!
ROLLBACK;
SELECT * FROM conta;   -- voltou

-- SAVEPOINT
START TRANSACTION;
UPDATE conta SET saldo = saldo + 10 WHERE id = 1;
SAVEPOINT s1;
UPDATE conta SET saldo = saldo + 10 WHERE id = 2;
ROLLBACK TO s1;        -- desfaz só o segundo
COMMIT;
```
