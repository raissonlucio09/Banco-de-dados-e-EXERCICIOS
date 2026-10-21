# Índices (Indexes) e Performance

Um **índice** é uma estrutura auxiliar (como o índice de um livro) que acelera buscas, sem precisar ler a tabela inteira (*full table scan*).

> Chaves primárias e UNIQUE **já criam índice automaticamente** (nos slides, o "Index Name" da PK `SYS_C0037279139` no Oracle).

## Criar
```sql
CREATE INDEX idx_cliente_nome ON tb_clientes (nome);
CREATE UNIQUE INDEX idx_cliente_cpf ON tb_clientes (cpf);
CREATE INDEX idx_estado_cidade ON tb_clientes (estado, cidade);   -- composto
```

## Ver e apagar
```sql
SHOW INDEX FROM tb_clientes;
DROP INDEX idx_cliente_nome ON tb_clientes;
```

## EXPLAIN — veja se o índice foi usado
```sql
EXPLAIN SELECT * FROM tb_clientes WHERE nome = 'Pedro Silva';
```
Olhe as colunas `type` (`ALL` = varre tudo ❌; `ref`/`const` = usou índice ✅), `key` (índice usado) e `rows` (linhas estimadas).

## Quando criar
✅ Colunas muito usadas em `WHERE`, `JOIN` (FKs), `ORDER BY`, `GROUP BY`.
✅ Colunas com muitos valores distintos.

## Quando evitar
❌ Tabelas pequenas.
❌ Colunas com poucos valores distintos (ex.: sexo M/F).
❌ Tabelas com muitas escritas: cada índice deixa `INSERT/UPDATE/DELETE` **mais lento** e ocupa espaço.

## Armadilhas que "matam" o índice
```sql
WHERE nome LIKE '%Silva'            -- % no início: não usa índice
WHERE YEAR(data_nascimento) = 1995  -- função na coluna: não usa índice
-- Melhor:
WHERE data_nascimento >= '1995-01-01' AND data_nascimento < '1996-01-01'
```

## Índice composto: ordem importa
`INDEX (estado, cidade)` serve para filtrar por `estado` ou `estado + cidade`, mas **não** só por `cidade` (regra do prefixo mais à esquerda).
