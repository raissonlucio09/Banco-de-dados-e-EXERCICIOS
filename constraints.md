# Constraints (Restrições)

**Constraints** são regras aplicadas a colunas ou tabelas para garantir a **integridade dos dados**.

| Constraint | Garante |
|---|---|
| `PRIMARY KEY` | valor único e não nulo; identifica o registro |
| `FOREIGN KEY` | integridade referencial entre tabelas |
| `UNIQUE` | valores únicos (permite NULL) |
| `NOT NULL` | proíbe NULL |
| `CHECK` | restringe valores aceitos (MySQL 8.0.16+) |
| `DEFAULT` | valor padrão quando nada é informado |

PRIMARY KEY (chave primária)
Valores **únicos e não nulos**. Só pode haver **uma PK por tabela** (mas ela pode ser composta).

```sql
-- Simples
CREATE TABLE clientes (
    id   INT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL
);

-- Composta (duas ou mais colunas juntas formam a chave)
CREATE TABLE matriculas (
    aluno_id       INT,
    disciplina_id  INT,
    data_matricula DATE NOT NULL,
    PRIMARY KEY (aluno_id, disciplina_id)
);
```

 FOREIGN KEY (chave estrangeira)
Mantém a **integridade referencial**: só aceita valores que **existem** na tabela referenciada.

```sql
CREATE TABLE pedidos (
    id         INT PRIMARY KEY,
    cliente_id INT,
    FOREIGN KEY (cliente_id) REFERENCES clientes(id)
);

-- FK composta
CREATE TABLE notas (
    aluno_id      INT,
    disciplina_id INT,
    nota          DECIMAL(5,2) NOT NULL,
    PRIMARY KEY (aluno_id, disciplina_id),
    FOREIGN KEY (aluno_id, disciplina_id) REFERENCES matriculas(aluno_id, disciplina_id)
);
```


 UNIQUE
Valores **não repetidos**, mas **permite NULL** (e vários NULLs).

```sql
CREATE TABLE funcionarios (
    id    INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE
);

-- UNIQUE composto: a COMBINAÇÃO não pode repetir (ex.: evitar reserva dupla)
CREATE TABLE reservas (
    id             INT PRIMARY KEY AUTO_INCREMENT,
    sala_id        INT NOT NULL,
    data_reserva   DATE NOT NULL,
    horario_inicio TIME NOT NULL,
    horario_fim    TIME NOT NULL,
    usuario_id     INT NOT NULL,
    UNIQUE (sala_id, data_reserva, horario_inicio)
);
```

 PRIMARY KEY × UNIQUE
| | PRIMARY KEY | UNIQUE |
|---|---|---|
| Quantas por tabela | 1 | várias |
| Aceita NULL | não | sim |
| Objetivo | identificar o registro | evitar duplicidade em outra coluna |

 NOT NULL
```sql
CREATE TABLE produtos (
    id   INT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL
);
```

 CHECK
Restringe valores aceitos.
```sql
CREATE TABLE funcionarios_chk (
    id    INT PRIMARY KEY,
    idade INT CHECK (idade >= 18),
    salario DECIMAL(10,2),
    CONSTRAINT ck_salario CHECK (salario > 0)
);
```
 Antes do MySQL 8.0.16 o CHECK era **aceito mas ignorado**. Confira sua versão com `SELECT VERSION();`.

 DEFAULT
Valor padrão se nada for informado.
```sql
CREATE TABLE pedidos_status (
    id     INT PRIMARY KEY,
    status VARCHAR(20) DEFAULT 'Pendente',
    criado_em DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

 DEFAULT + CHECK juntos
Exemplo do slide (Oracle usa `VARCHAR2`; no MySQL é `VARCHAR`):
```sql
CREATE TABLE usuarios (
    id    INT PRIMARY KEY,
    nome  VARCHAR(100) NOT NULL,
    ativo VARCHAR(3) DEFAULT 'Sim' CHECK (ativo IN ('Sim', 'Não'))
);
```

 Nomeando constraints (boa prática)
Dar nome facilita remover depois.
```sql
CREATE TABLE exemplo (
    id INT,
    email VARCHAR(100),
    idade INT,
    CONSTRAINT pk_exemplo PRIMARY KEY (id),
    CONSTRAINT uq_exemplo_email UNIQUE (email),
    CONSTRAINT ck_exemplo_idade CHECK (idade >= 18)
);
```
Ver constraints existentes:
```sql
SELECT constraint_name, constraint_type
FROM information_schema.table_constraints
WHERE table_schema = 'estudos' AND table_name = 'exemplo';
```

---

 Código completo

### constraints.sql

```sql
CREATE DATABASE estudos;
USE estudos;

CREATE TABLE clientes (
    id   INT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL
);

CREATE TABLE pedidos (
    id         INT PRIMARY KEY,
    cliente_id INT,
    status     VARCHAR(20) DEFAULT 'Pendente',
    FOREIGN KEY (cliente_id) REFERENCES clientes(id)
);

CREATE TABLE matriculas (
    aluno_id       INT,
    disciplina_id  INT,
    data_matricula DATE NOT NULL,
    PRIMARY KEY (aluno_id, disciplina_id)
);

CREATE TABLE notas (
    aluno_id      INT,
    disciplina_id INT,
    nota          DECIMAL(5,2) NOT NULL,
    PRIMARY KEY (aluno_id, disciplina_id),
    FOREIGN KEY (aluno_id, disciplina_id) REFERENCES matriculas(aluno_id, disciplina_id)
);

CREATE TABLE funcionarios (
    id    INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE,
    idade INT CHECK (idade >= 18)
);

CREATE TABLE usuarios (
    id    INT PRIMARY KEY,
    nome  VARCHAR(100) NOT NULL,
    ativo VARCHAR(3) DEFAULT 'Sim' CHECK (ativo IN ('Sim','Não'))
);

-- Testes (os que dão erro são de propósito!)
INSERT INTO clientes VALUES (1, 'João');
INSERT INTO pedidos (id, cliente_id) VALUES (1, 1);       -- OK
-- INSERT INTO pedidos (id, cliente_id) VALUES (2, 99);   -- ERRO: cliente 99 não existe
-- INSERT INTO clientes VALUES (1, 'Outro');              -- ERRO: PK duplicada
-- INSERT INTO clientes VALUES (2, NULL);                 -- ERRO: NOT NULL
-- INSERT INTO funcionarios VALUES (1,'a@a.com',16);      -- ERRO: CHECK idade
INSERT INTO usuarios (id, nome) VALUES (1, 'Maria');      -- ativo = 'Sim' (DEFAULT)
SELECT * FROM usuarios;
```
