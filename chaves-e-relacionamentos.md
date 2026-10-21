# Chaves e Relacionamentos entre Tabelas

 Chave primária — por que usar ID?
Para identificar um registro precisamos de uma coluna **única**. Nome, e-mail e data de nascimento **podem repetir** (dois "João Silva", dois e-mails iguais por erro). A solução é criar um **código/ID**:

| codigo (PK) | nome | email | nascimento |
|---|---|---|---|
| 1 | João Silva | joao@joao.com | 1985-07-10 |
| 2 | Maria Santos | maria@maria.com | 1988-10-02 |
| 3 | João Silva | joaos@joaos.com | 1982-08-02 |

```sql
CREATE TABLE cliente (
    codigo INT AUTO_INCREMENT PRIMARY KEY,
    nome   VARCHAR(50),
    estado VARCHAR(2)
);
```

 Como relacionar duas tabelas? Chave estrangeira
Como saber quais pedidos são do cliente João? Colocando **`cliente_id` na tabela `pedido`**. Essa coluna "não faz parte" do pedido, mas indica **a quem pertence**. É chamada **chave estrangeira (FK)** porque o dado vem de **outra tabela**.

```
cliente                      pedido
┌────┬──────┐               ┌────┬────────────┬────────────┐
│ id │ nome │ ◄─────────────│ id │ data       │ cliente_id │
│ 1  │ João │               │ 1  │ 2014-08-10 │ 1          │
└────┴──────┘               │ 2  │ 2014-08-12 │ 1          │
                            └────┴────────────┴────────────┘
```

O erro clássico
Colocar `pedido_id` dentro de `cliente`: para cada novo pedido teríamos que **repetir todos os dados do cliente** (redundância, inconsistência). A FK deve ficar **no lado "muitos"**.

 **Regra de ouro:** a FK fica na tabela do lado **N** (muitos).

 Relacionamento Muitos-para-Um (Many to One)
Olhando de `pedido` para `cliente`: **muitos pedidos** apontam para **um** cliente. Visto de `cliente`: **um cliente** tem **muitos pedidos** (**1:N**).

```sql
CREATE TABLE cliente (
    codigo INT PRIMARY KEY,
    nome   VARCHAR(50),
    estado VARCHAR(2)
);

CREATE TABLE pedido (
    codigo         INT PRIMARY KEY,
    data           DATE,
    codigo_cliente INT,
    CONSTRAINT fk_pedido_cliente
        FOREIGN KEY (codigo_cliente) REFERENCES cliente(codigo)
);
```

 Tipos de relacionamento

 1:1 (um para um)
Ex.: `pessoa` ↔ `passaporte`. FK com `UNIQUE` (ou PK compartilhada).
```sql
CREATE TABLE pessoa (id INT PRIMARY KEY, nome VARCHAR(100));
CREATE TABLE passaporte (
    id        INT PRIMARY KEY,
    numero    VARCHAR(20) NOT NULL,
    pessoa_id INT NOT NULL UNIQUE,                -- UNIQUE garante o 1:1
    FOREIGN KEY (pessoa_id) REFERENCES pessoa(id)
);
```

1:N (um para muitos) — o mais comum
Ex.: `departamento` → `empregado`. FK no lado N.

 N:N (muitos para muitos)
Ex.: `aluno` ↔ `disciplina` (um aluno cursa várias disciplinas; uma disciplina tem vários alunos). Precisa de uma **tabela associativa** com duas FKs (que geralmente formam a PK composta):
```sql
CREATE TABLE aluno      (id INT PRIMARY KEY, nome VARCHAR(100));
CREATE TABLE disciplina (id INT PRIMARY KEY, nome VARCHAR(100));

CREATE TABLE matricula (
    aluno_id      INT,
    disciplina_id INT,
    data_matricula DATE NOT NULL,
    PRIMARY KEY (aluno_id, disciplina_id),
    FOREIGN KEY (aluno_id)      REFERENCES aluno(id),
    FOREIGN KEY (disciplina_id) REFERENCES disciplina(id)
);
```

 Ações referenciais: ON DELETE / ON UPDATE
O que acontece com os filhos quando o pai é apagado/alterado?

| Opção | Efeito |
|---|---|
| `RESTRICT` / `NO ACTION` | **bloqueia** a operação (padrão) |
| `CASCADE` | apaga/atualiza os filhos junto |
| `SET NULL` | coloca NULL na FK dos filhos (a coluna deve aceitar NULL) |
| `SET DEFAULT` | (não suportado pelo InnoDB) |

```sql
CREATE TABLE pedido (
    id         INT PRIMARY KEY,
    cliente_id INT,
    FOREIGN KEY (cliente_id) REFERENCES cliente(id)
        ON DELETE CASCADE
        ON UPDATE CASCADE
);
```
Exemplo de uso: `item_pedido` com `ON DELETE CASCADE` (apagou o pedido, somem os itens).
Cuidado: `CASCADE` em cliente→pedido pode apagar histórico financeiro sem querer.

 Exemplo do slide:
```sql
CREATE TABLE departamento (
    num_dept  INT PRIMARY KEY,
    nome_dept VARCHAR(50) NOT NULL,
    ramal     INT
);

CREATE TABLE empregado (
    num_emp  INT PRIMARY KEY,
    nome_emp VARCHAR(50) NOT NULL,
    salario  DECIMAL(10,2),
    dept     INT,
    FOREIGN KEY (dept) REFERENCES departamento(num_dept)
);
```

---



 relacionamentos.sql

```sql
CREATE DATABASE IF NOT EXISTS empresa CHARACTER SET utf8mb4;
USE empresa;

DROP TABLE IF EXISTS empregado;
DROP TABLE IF EXISTS departamento;

CREATE TABLE departamento (
    num_dept  INT PRIMARY KEY,
    nome_dept VARCHAR(50) NOT NULL,
    ramal     INT
);

CREATE TABLE empregado (
    num_emp  INT PRIMARY KEY,
    nome_emp VARCHAR(50) NOT NULL,
    salario  DECIMAL(10,2),
    dept     INT,
    CONSTRAINT fk_emp_dept FOREIGN KEY (dept) REFERENCES departamento(num_dept)
        ON UPDATE CASCADE
        ON DELETE SET NULL
);

INSERT INTO departamento VALUES (21,'Pessoal',142),(25,'Financeiro',143),(28,'Técnico',144);

INSERT INTO empregado VALUES
 (32,'J Silva',380,21),(74,'M Reis',400,25),(89,'C Melo',520,28),
 (92,'R Silva',480,25),(112,'R Pinto',390,21),(121,'V Simão',905,28),(130,'J Neves',640,28);

-- Quem trabalha em cada departamento?
SELECT e.nome_emp, d.nome_dept
FROM empregado e
JOIN departamento d ON d.num_dept = e.dept;

-- Apagar o departamento 21 -> empregados dele ficam com dept NULL
DELETE FROM departamento WHERE num_dept = 21;
SELECT * FROM empregado;
```
