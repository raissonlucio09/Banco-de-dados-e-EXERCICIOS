# UPDATE e DELETE

## UPDATE — alterar registros
Atualiza um dado por outro **do mesmo tipo**.

```sql
UPDATE nome_tabela
SET coluna = novo_valor [, coluna2 = valor2]
WHERE condição;
```
Exemplo:
```sql
UPDATE tb_clientes SET nome = 'João Silva' WHERE cpf = '12345678901';
UPDATE tb_clientes SET limite_credito = limite_credito * 1.10 WHERE estado = 'SP';  -- aumento de 10%
```

## DELETE — apagar registros
```sql
DELETE FROM nome_tabela WHERE condição;
```
Exemplo:
```sql
DELETE FROM tb_clientes WHERE nome = 'Robson Matos';
```

## ⚠️ SEM WHERE = TABELA INTEIRA AFETADA!
- `UPDATE` sem `WHERE` altera **todas** as linhas.
- `DELETE` sem `WHERE` apaga **todas** as linhas.

### Segurança: o Safe Updates do Workbench
O MySQL Workbench bloqueia UPDATE/DELETE sem `WHERE` usando chave (erro 1175). Para desativar (só se tiver certeza):
```sql
SET SQL_SAFE_UPDATES = 0;
```

### Dicas de segurança (use no trabalho!)
1. **Primeiro rode um SELECT** com o mesmo WHERE para ver o que será afetado.
2. Use **transações** (`START TRANSACTION` ... `ROLLBACK`) para testar — veja o arquivo transacoes.md.
3. Faça backup antes de operações grandes.
4. Prefira **exclusão lógica** (coluna `ativo`) em dados importantes.

## DELETE x TRUNCATE x DROP
| Comando | O que remove | Pode ter WHERE? | Reversível (rollback)? |
|---|---|---|---|
| `DELETE` | linhas | sim | sim (em transação) |
| `TRUNCATE` | todas as linhas (zera AUTO_INCREMENT) | não | não |
| `DROP TABLE` | a tabela inteira | não | não |

## Exclusão lógica (soft delete)
```sql
ALTER TABLE tb_clientes ADD ativo BOOLEAN DEFAULT TRUE;
UPDATE tb_clientes SET ativo = FALSE WHERE cpf = '12345678901';   -- "apagou"
SELECT * FROM tb_clientes WHERE ativo = TRUE;                      -- só os ativos
```

## Integridade referencial
Não dá para apagar um cliente que tem pedidos (FK) — a menos que a FK use `ON DELETE CASCADE`. Veja o arquivo chaves-e-relacionamentos.md.

---

## 💻 Código completo para testar

### update-delete.sql

```sql
USE estudos;

-- Sempre teste com SELECT antes
SELECT * FROM tb_clientes WHERE cpf = '12345678901';

UPDATE tb_clientes SET nome = 'João Silva' WHERE cpf = '12345678901';
UPDATE tb_clientes SET limite_credito = limite_credito * 1.10 WHERE estado = 'SP';
UPDATE tb_clientes SET cidade = 'Santos', estado = 'SP' WHERE cpf = '56789012345';

-- Testando com segurança dentro de transação
START TRANSACTION;
DELETE FROM tb_clientes WHERE estado = 'RJ';
SELECT * FROM tb_clientes;     -- conferiu?
ROLLBACK;                      -- desfaz (use COMMIT para confirmar)

DELETE FROM tb_clientes WHERE nome = 'Robson Matos';
```
