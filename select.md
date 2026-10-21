# SELECT (consultas)

O `SELECT` é o comando mais usado do SQL: **consulta** dados de uma ou mais tabelas.

## Estrutura geral (ordem obrigatória)
```sql
SELECT   colunas
FROM     tabela
WHERE    condição_de_linha
GROUP BY colunas_de_agrupamento
HAVING   condição_de_grupo
ORDER BY colunas
LIMIT    quantidade;
```

## Básico
```sql
SELECT * FROM tb_clientes;                    -- tudo
SELECT nome, cidade FROM tb_clientes;         -- colunas específicas
SELECT DISTINCT estado FROM tb_clientes;      -- sem repetição
SELECT nome AS cliente FROM tb_clientes;      -- apelido (alias)
```
> `SELECT *` é ótimo para estudar, mas em sistemas reais liste as colunas.

## WHERE — filtrando linhas
### Operadores de comparação
| Operador | Significado |
|---|---|
| `=` | igual |
| `<>` ou `!=` | diferente |
| `>` `<` `>=` `<=` | maior, menor... |

```sql
SELECT * FROM tb_clientes WHERE cpf = '12345678901';
SELECT * FROM tb_clientes WHERE limite_credito > 5000;
SELECT * FROM tb_clientes WHERE estado <> 'SP';
SELECT * FROM tb_clientes WHERE nome >= 'Fernando Pires';  -- ordem alfabética
```

### LIKE — busca por padrão
- `%` = qualquer quantidade de caracteres
- `_` = exatamente um caractere
```sql
SELECT * FROM tb_clientes WHERE nome LIKE 'C%';       -- começa com C
SELECT * FROM tb_clientes WHERE nome LIKE '%Silva';   -- termina com Silva
SELECT * FROM tb_clientes WHERE nome LIKE '%an%';     -- contém "an"
SELECT * FROM tb_clientes WHERE nome LIKE '_aria';    -- Maria, Karia...
```

### Outros filtros úteis
```sql
WHERE idade BETWEEN 20 AND 30             -- inclusive nas pontas
WHERE estado IN ('SP','RJ','MG')          -- lista
WHERE estado NOT IN ('SP','RJ')
WHERE endereco2 IS NULL                   -- nulo (NUNCA use = NULL)
WHERE endereco2 IS NOT NULL
```

### Filtros compostos: AND, OR, NOT
```sql
SELECT * FROM tb_clientes WHERE volume_compra > 5000 AND volume_compra < 20000;
SELECT * FROM tb_clientes WHERE idade > 22 AND sexo = 'F';
SELECT * FROM tb_clientes WHERE sexo = 'F' OR idade > 30;
SELECT * FROM tb_clientes WHERE (sexo = 'F' OR idade > 30) AND estado = 'SP';
```
> **Precedência**: `AND` é avaliado antes de `OR`. Use parênteses para deixar claro.

## ORDER BY — ordenando
```sql
SELECT * FROM tb_clientes ORDER BY nome ASC;    -- crescente (padrão)
SELECT * FROM tb_clientes ORDER BY nome DESC;   -- decrescente
SELECT * FROM tb_clientes ORDER BY estado, nome; -- por estado, depois nome
```

## LIMIT — limitando resultados
```sql
SELECT * FROM tb_clientes ORDER BY limite_credito DESC LIMIT 3;   -- top 3
SELECT * FROM tb_clientes LIMIT 10 OFFSET 20;                     -- paginação
```

## Datas no MySQL (adaptação dos slides Oracle)
| Oracle (slide) | MySQL |
|---|---|
| `TO_DATE('07/10/1995','DD/MM/YYYY')` | `STR_TO_DATE('07/10/1995','%d/%m/%Y')` |
| `TO_CHAR(data,'MM') = 10` | `MONTH(data) = 10` |

```sql
SELECT * FROM tb_clientes WHERE data_nascimento = '1995-10-07';
SELECT * FROM tb_clientes WHERE data_nascimento = STR_TO_DATE('07/10/1995','%d/%m/%Y');
SELECT * FROM tb_clientes WHERE data_nascimento > STR_TO_DATE('07/10/1995','%d/%m/%Y');
SELECT * FROM tb_clientes WHERE MONTH(data_nascimento) = 10;   -- aniversariantes de outubro
SELECT nome, DATE_FORMAT(data_nascimento,'%d/%m/%Y') AS nascimento FROM tb_clientes;
SELECT nome, TIMESTAMPDIFF(YEAR, data_nascimento, CURDATE()) AS idade FROM tb_clientes;
```
Códigos de formato: `%d` dia, `%m` mês, `%Y` ano (4 dígitos), `%y` ano (2), `%H` hora, `%i` minuto, `%s` segundo.

---

## 💻 Código completo para testar

### consultas.sql

```sql
USE estudos;

-- Básico
SELECT * FROM tb_clientes;
SELECT nome, cidade FROM tb_clientes;
SELECT DISTINCT estado FROM tb_clientes;

-- WHERE
SELECT * FROM tb_clientes WHERE cpf = '12345678901';
SELECT * FROM tb_clientes WHERE limite_credito > 5000;
SELECT * FROM tb_clientes WHERE estado <> 'SP';

-- LIKE
SELECT * FROM tb_clientes WHERE nome LIKE 'C%';
SELECT * FROM tb_clientes WHERE nome LIKE '%Silva';

-- Outros filtros
SELECT * FROM tb_clientes WHERE idade BETWEEN 25 AND 35;
SELECT * FROM tb_clientes WHERE estado IN ('SP','RJ');
SELECT * FROM tb_clientes WHERE endereco2 IS NULL;

-- Compostos
SELECT * FROM tb_clientes WHERE volume_compra > 5000 AND volume_compra < 20000;
SELECT * FROM tb_clientes WHERE idade > 22 AND sexo = 'F';
SELECT * FROM tb_clientes WHERE sexo = 'F' OR idade > 30;

-- Ordenação
SELECT * FROM tb_clientes ORDER BY nome ASC;
SELECT * FROM tb_clientes ORDER BY nome DESC;
SELECT * FROM tb_clientes ORDER BY limite_credito DESC LIMIT 3;

-- Datas
SELECT * FROM tb_clientes WHERE MONTH(data_nascimento) = 10;
SELECT nome, DATE_FORMAT(data_nascimento,'%d/%m/%Y') AS nascimento FROM tb_clientes;
SELECT nome, TIMESTAMPDIFF(YEAR, data_nascimento, CURDATE()) AS idade_calculada FROM tb_clientes;
```
