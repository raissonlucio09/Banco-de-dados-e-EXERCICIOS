# Tipos de Dados no MySQL

Tipos de dados **classificam** as informações de cada coluna. Escolher o tipo certo deixa o banco **mais eficiente** e **mais seguro**.

## Texto
| Tipo | Descrição | Exemplo |
|---|---|---|
| `CHAR(n)` | Tamanho **fixo** (até 255). Completa com espaços. | `CHAR(2)` para UF, `CHAR(11)` para CPF |
| `VARCHAR(n)` | Tamanho **variável** (até 65.535 bytes). Ocupa só o necessário. | `VARCHAR(100)` para nome |
| `TEXT` | Textos longos | descrição, observações |
| `ENUM('a','b')` | Um valor de uma lista | `ENUM('M','F')` |

> 📌 No Oracle (slides) usa-se `VARCHAR2`. No MySQL é apenas `VARCHAR`.
> `CHAR(10)` com texto de 5 letras ocupa 10; `VARCHAR(10)` ocupa só 5 (+1 de controle).
> Se o valor for maior que o limite, o MySQL (modo estrito) retorna **erro**.

## Numéricos inteiros
| Tipo | Faixa (com sinal) |
|---|---|
| `TINYINT` | -128 a 127 |
| `SMALLINT` | -32.768 a 32.767 |
| `INT` | ≈ ±2,1 bilhões |
| `BIGINT` | ≈ ±9,2 quintilhões |

## Numéricos com casas decimais
| Tipo | Uso |
|---|---|
| `DECIMAL(p,s)` | **Exato**. Use para **dinheiro**. `DECIMAL(10,2)` = até 10 dígitos, 2 depois da vírgula (equivalente ao `NUMBER(10,2)` do Oracle) |
| `FLOAT` / `DOUBLE` | Aproximados; podem ter erros de arredondamento. Evite para dinheiro |

## Data e hora
| Tipo | Formato | Exemplo |
|---|---|---|
| `DATE` | `YYYY-MM-DD` | `2025-10-21` |
| `TIME` | `HH:MM:SS` | `14:30:00` |
| `DATETIME` | data + hora | `2025-10-21 14:30:00` |
| `TIMESTAMP` | data + hora (com fuso, atualizável automático) | |
| `YEAR` | ano | `2025` |

> 📌 O MySQL sempre **armazena** data como `YYYY-MM-DD`. A formatação `DD/MM/YYYY` é feita na consulta com `DATE_FORMAT`.

## Booleano
`BOOLEAN` (é um apelido de `TINYINT(1)`): `0` = falso, `1` = verdadeiro. Substitui o `NUMBER(1)` usado na coluna `PRIMEIRA_COMPRA` dos slides.

## Boas práticas
- CPF, CEP, telefone → **texto** (`VARCHAR`/`CHAR`), não número (zeros à esquerda importam e você não faz conta com eles).
- Dinheiro → `DECIMAL`.
- Não use `VARCHAR(255)` em tudo "por garantia".
- `IDADE` pode ser **calculada** a partir da data de nascimento — guardar a data é melhor que guardar a idade (idade fica desatualizada).

---

## 💻 Código completo para testar

### tipos.sql

```sql
USE estudos;

CREATE TABLE exemplo_tipos (
    id           INT AUTO_INCREMENT PRIMARY KEY,
    uf           CHAR(2),              -- tamanho fixo
    nome         VARCHAR(100),         -- tamanho variável
    descricao    TEXT,
    quantidade   SMALLINT,
    preco        DECIMAL(10,2),        -- dinheiro
    nascimento   DATE,
    criado_em    DATETIME DEFAULT CURRENT_TIMESTAMP,
    ativo        BOOLEAN DEFAULT TRUE,
    sexo         ENUM('M','F','O')
);

INSERT INTO exemplo_tipos (uf, nome, descricao, quantidade, preco, nascimento, sexo)
VALUES ('SP', 'Pedro', 'Texto longo...', 10, 1999.90, '1990-03-15', 'M');

-- Ver estrutura da tabela (metadados!)
DESCRIBE exemplo_tipos;

-- Datas: o MySQL guarda como YYYY-MM-DD, mas você exibe como quiser
SELECT nome, DATE_FORMAT(nascimento, '%d/%m/%Y') AS nascimento_br FROM exemplo_tipos;
```
