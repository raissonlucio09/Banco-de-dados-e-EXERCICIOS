# Modelagem de Dados

A **modelagem de dados** é a técnica usada para especificar as **regras de negócio** e as **estruturas de dados** de um banco. Faz parte do ciclo de desenvolvimento de um sistema e é vital para o bom resultado do projeto. Consiste em desenhar o sistema de informações, focando nas **entidades** e nas **dependências** entre elas.

 Os 3 níveis

```
Modelo Conceitual  →  Modelo Lógico  →  Modelo Físico
 (negócio)            (estrutura)        (SGBD real)
```

Modelo Conceitual
- Mais alto nível; usado para **conversar com o cliente**.
- Foco no **negócio**, não na tecnologia.
- Diagrama: **DER — Diagrama Entidade-Relacionamento**.
- Identifica **entidades**, **atributos** e **relacionamentos** (com cardinalidades).

Exemplo: `Funcionário (1,n) — é alocado — Projeto (1,n)`, com atributos como `codigo`, `nome`, `data_inicio`.

Modelo Lógico
- Leva em conta algumas limitações e implementa:
  - padrão e nomenclatura;
  - **chaves primárias e estrangeiras**;
  - **normalização**;
  - **integridade referencial**.
- Ainda não depende de um SGBD específico.

Exemplo dos slides (locadora): `Cliente — Aluga — Carro`, `Telefone` (1 cliente, N telefones), `Esportivo` e `Sedã` como especializações de `Carro`.

Modelo Físico
- Considera as **limitações do SGBD escolhido** (MySQL, Oracle...).
- É o que vira o script `CREATE TABLE`.
- Exemplo dos slides: sistema de biblioteca (`livro`, `autor`, `editora`, `exemplar`, `usuario`, `emprestimo`...).

 Cardinalidades
| Notação | Significado |
|---|---|
| (0,1) | zero ou um |
| (1,1) | exatamente um |
| (0,n) | zero ou muitos |
| (1,n) | um ou muitos |

 Conceitos do DER
- **Entidade**: "coisa" do mundo real (Cliente, Produto). Vira **tabela**.
- **Atributo**: característica da entidade (nome, preço). Vira **coluna**.
- **Relacionamento**: ligação entre entidades. Vira **chave estrangeira** (ou tabela associativa no N:N).
- **Chave primária**: identifica unicamente cada registro.

 Exemplo: do conceitual ao físico

 Ferramentas
- **MySQL Workbench** (modelagem + engenharia reversa)
- brModelo (conceitual/lógico, muito usado no Brasil)
- dbdiagram.io, draw.io

---



 exemplo-locadora.sql

```sql
-- Modelo físico da locadora (Cliente - Aluga - Carro)
CREATE DATABASE locadora;
USE locadora;

CREATE TABLE cliente (
    cod_cli  INT AUTO_INCREMENT PRIMARY KEY,
    nome     VARCHAR(50)  NOT NULL,
    cnh      VARCHAR(11)  NOT NULL UNIQUE,
    endereco VARCHAR(100)
);

-- 1 cliente tem N telefones
CREATE TABLE telefone (
    telefone_pk INT AUTO_INCREMENT PRIMARY KEY,
    telefone    VARCHAR(15) NOT NULL,
    cod_cli     INT NOT NULL,
    FOREIGN KEY (cod_cli) REFERENCES cliente(cod_cli)
);

CREATE TABLE carro (
    cod_car INT AUTO_INCREMENT PRIMARY KEY,
    placa   VARCHAR(8)  NOT NULL UNIQUE,
    cor     VARCHAR(15),
    modelo  VARCHAR(20),
    ano     INT,
    diaria  DECIMAL(10,2) NOT NULL
);

-- Especializações de carro (1:1 com carro)
CREATE TABLE esportivo (
    cod_car    INT PRIMARY KEY,
    velocidade DECIMAL(6,2),
    FOREIGN KEY (cod_car) REFERENCES carro(cod_car)
);

CREATE TABLE seda (
    cod_car     INT PRIMARY KEY,
    passageiros INT,
    FOREIGN KEY (cod_car) REFERENCES carro(cod_car)
);

-- Relacionamento N:N entre cliente e carro (tabela associativa)
CREATE TABLE aluga (
    cod_alu      INT AUTO_INCREMENT PRIMARY KEY,
    data_inicial DATE NOT NULL,
    data_final   DATE,
    cod_car      INT NOT NULL,
    cod_cli      INT NOT NULL,
    FOREIGN KEY (cod_car) REFERENCES carro(cod_car),
    FOREIGN KEY (cod_cli) REFERENCES cliente(cod_cli)
);
```
