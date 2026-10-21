# INSERT (inserindo dados)

 Sintaxe
```sql
INSERT INTO nome_tabela (lista_de_campos)
VALUES (lista_de_dados);
```

 Exemplo do slide
```sql
INSERT INTO tb_clientes
  (cpf, nome, endereco1, endereco2, bairro, cidade, estado, cep,
   data_nascimento, idade, sexo, limite_credito, volume_compra, primeira_compra)
VALUES
  ('12345678901', 'Pedro Silva', 'Rua Belquior, 222', 'Avenida Moura Prado, 1876',
   'Morumbi', 'São Paulo', 'SP', '98765243', '1988-05-12', 37, 'M', 5600.35, 3780.60, 0);
```

 Regras
- A **ordem** dos valores deve bater com a ordem das colunas listadas.
- **Texto e datas** entre aspas simples `'...'`; **números** sem aspas.
- Data no formato `'AAAA-MM-DD'`.
- Colunas omitidas recebem `NULL` ou o valor `DEFAULT`.
- Colunas `AUTO_INCREMENT` podem ser omitidas.

 Variações
```sql
-- Várias linhas de uma vez (mais rápido)
INSERT INTO produto (nome, preco) VALUES
  ('Mouse', 59.90),
  ('Teclado', 129.90),
  ('Monitor', 899.00);

-- Inserir a partir de um SELECT
INSERT INTO clientes_vip (cpf, nome)
SELECT cpf, nome FROM tb_clientes WHERE limite_credito > 5000;

-- Sem listar colunas (precisa passar TODAS, na ordem) — evite!
INSERT INTO produto VALUES (NULL, 'Cabo USB', 19.90);

-- Ignorar erro de duplicidade
INSERT IGNORE INTO produto (id, nome, preco) VALUES (1, 'Mouse', 59.90);

-- "Upsert": insere ou atualiza se a chave já existir
INSERT INTO produto (id, nome, preco) VALUES (1, 'Mouse', 64.90)
ON DUPLICATE KEY UPDATE preco = VALUES(preco);
```

 Erros comuns
| Erro | Causa |
|---|---|
| `Column count doesn't match value count` | Número de colunas ≠ número de valores |
| `Duplicate entry '1' for key 'PRIMARY'` | PK repetida |
| `Data too long for column` | Texto maior que o `VARCHAR` |
| `Cannot add or update a child row` | FK apontando para registro que não existe |

---



 inserts.sql

```sql
USE estudos;

INSERT INTO tb_clientes
 (cpf, nome, endereco1, endereco2, bairro, cidade, estado, cep, data_nascimento, idade, sexo, limite_credito, volume_compra, primeira_compra)
VALUES
 ('12345678901','Pedro Silva','Rua Belquior, 222','Avenida Moura Prado, 1876','Morumbi','São Paulo','SP','98765243','1988-05-12',37,'M',5600.35,3780.60,0),
 ('23456789012','Carla Souza','Rua das Flores, 10',NULL,'Centro','Campinas','SP','13010000','1995-10-07',30,'F',3000.00,1200.00,1),
 ('34567890123','Fernando Pires','Av. Brasil, 500',NULL,'Jardins','Rio de Janeiro','RJ','20000000','1980-10-25',45,'M',12000.00,15000.00,0),
 ('45678901234','Ana Silva','Rua Sete, 77',NULL,'Savassi','Belo Horizonte','MG','30100000','1999-01-30',26,'F',8000.00,6000.00,0),
 ('56789012345','Cláudio Lima','Rua Verde, 9',NULL,'Boa Vista','Recife','PE','50000000','1992-12-02',32,'M',2500.00,800.00,1);

SELECT * FROM tb_clientes;
```
