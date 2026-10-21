# ALTER TABLE (alterando a estrutura)

`ALTER TABLE` muda a **estrutura** da tabela (não os dados).

 Adicionar coluna
```sql
ALTER TABLE tb_clientes ADD estado_civil VARCHAR(20);
ALTER TABLE tb_clientes ADD email VARCHAR(100) AFTER nome;     -- posição
ALTER TABLE tb_clientes ADD cadastro DATE FIRST;               -- primeira coluna
```

 Modificar tipo/tamanho da coluna
```sql
ALTER TABLE tb_clientes MODIFY nome VARCHAR(150);
ALTER TABLE tb_clientes MODIFY nome VARCHAR(150) NOT NULL;     -- e adiciona NOT NULL
```
 No MySQL você **precisa repetir o tipo** mesmo para só adicionar `NOT NULL`.

 Renomear coluna
```sql
ALTER TABLE tb_clientes RENAME COLUMN estado_civil TO est_civ;       -- MySQL 8+
ALTER TABLE tb_clientes CHANGE estado_civil est_civ VARCHAR(20);     -- qualquer versão
```

Renomear tabela
```sql
ALTER TABLE tb_clientes RENAME TO clientes;
RENAME TABLE clientes TO tb_clientes;   -- forma alternativa
```

 Excluir coluna
```sql
ALTER TABLE tb_clientes DROP COLUMN est_civ;
```

 Chave primária por ALTER
```sql
-- Adicionar PK
ALTER TABLE tb_clientes ADD PRIMARY KEY (cpf);

-- Adicionar coluna de ID auto-incremento como PK
ALTER TABLE tb_clientes ADD codigo INT AUTO_INCREMENT PRIMARY KEY FIRST;

-- Remover PK (no MySQL só existe uma, então não precisa do nome)
ALTER TABLE tb_clientes DROP PRIMARY KEY;
```


 Outras alterações de constraints
```sql
ALTER TABLE pedido ADD CONSTRAINT fk_pedido_cliente
    FOREIGN KEY (cliente_id) REFERENCES cliente(id);
ALTER TABLE pedido DROP FOREIGN KEY fk_pedido_cliente;
ALTER TABLE cliente ADD CONSTRAINT uq_email UNIQUE (email);
ALTER TABLE cliente DROP INDEX uq_email;                 -- remove UNIQUE
ALTER TABLE cliente ADD CONSTRAINT ck_idade CHECK (idade >= 18);
ALTER TABLE cliente DROP CHECK ck_idade;                 -- MySQL 8.0.16+
ALTER TABLE cliente ALTER COLUMN status SET DEFAULT 'Ativo';
ALTER TABLE cliente ALTER COLUMN status DROP DEFAULT;
```

---

## Código completo para testar

### alter.sql

```sql
USE estudos;

ALTER TABLE tb_clientes ADD estado_civil VARCHAR(20);
ALTER TABLE tb_clientes MODIFY nome VARCHAR(150) NOT NULL;
ALTER TABLE tb_clientes RENAME COLUMN estado_civil TO est_civ;
ALTER TABLE tb_clientes DROP COLUMN est_civ;

-- PK
ALTER TABLE tb_clientes ADD PRIMARY KEY (cpf);
DESCRIBE tb_clientes;
ALTER TABLE tb_clientes DROP PRIMARY KEY;
ALTER TABLE tb_clientes ADD PRIMARY KEY (cpf);   -- volta

-- Renomear tabela e voltar
ALTER TABLE tb_clientes RENAME TO clientes;
ALTER TABLE clientes RENAME TO tb_clientes;
```
