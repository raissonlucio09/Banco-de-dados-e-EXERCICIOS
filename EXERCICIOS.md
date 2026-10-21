# Meus Exercícios





### Exercício 1: Sistema Controle de Corrida

## Enunciado
A empresa de Rádio Táxi do Mané localizada na divisa dos Estados de MS, MT
e GO, precisa de uma aplicação que controle:
1. Cadastro de clientes;
2. Cadastro de cooperados;
3. Cadastro das corridas programadas
Dos seus clientes são mantidos os seguintes dados:
 Nome do cliente;
Endereço completo (logradouro, bairro, cidade e estado);
 Telefones de contato;
As pessoas que solicitam um táxi pela primeira vez, são cadastradas como cliente,
para tanto é necessário confirmar se a pessoa já não está cadastrada. Para o
cooperado (taxista), cadastrar:
 Nome;
 Endereço completo (logradouro, bairro, cidade e estado);
Número da placa do veículo que dirige;
 Dados do veículo (modelo, fabricante e cor);
Número da carteira;
Data de validade;
 Categoria profissional da carteira;
Os cooperados são os taxistas que trabalham para cooperativa. Todos são
cadastrados no sistema quando de sua adesão à cooperativa. Cada cooperado pode
ter vários carros. E cada carro é de um cooperado. Quando um cliente solicitar uma
corrida programada (pedidos com antecedência maior que meia hora), cadastra-se
no controle de corrida:
1. Endereço de início e fim da corrida;
2. O endereço de início e/ou fim poderá ser em outro município de outro estado;
3. Registrar a data e hora do início e fim da corrida;
4. Telefone de contato fornecido pelo cliente.
5. Identificação do cooperado;
6. Identificação do veículo;
7. Fornecer ao cliente o nome do cooperado e a placa do veículo que irá atendê-lo.

## Minha solução
```sql
CREATE DATABASE corridadb;
USE corridadb;

CREATE TABLE Endereco_Cliente(
    id INT PRIMARY KEY AUTO_INCREMENT,
    logradouro VARCHAR(150) NOT NULL,
    bairro VARCHAR(50) NOT NULL,
    cidade VARCHAR(50) NOT NULL,
    estado CHAR(2) NOT NULL
);

CREATE TABLE Endereco_Cooperado(
    id INT PRIMARY KEY AUTO_INCREMENT,
    logradouro VARCHAR(150) NOT NULL,
    bairro VARCHAR(50) NOT NULL,
    cidade VARCHAR(50) NOT NULL,
    estado CHAR(2) NOT NULL
);

CREATE TABLE Endereco_Inicio(
    id INT PRIMARY KEY AUTO_INCREMENT,
    logradouro VARCHAR(150) NOT NULL,
    bairro VARCHAR(50) NOT NULL,
    cidade VARCHAR(50) NOT NULL,
    estado CHAR(2) NOT NULL
);

CREATE TABLE Endereco_Fim(
    id INT PRIMARY KEY AUTO_INCREMENT,
    logradouro VARCHAR(150) NOT NULL,
    bairro VARCHAR(50) NOT NULL,
    cidade VARCHAR(50) NOT NULL,
    estado CHAR(2) NOT NULL
);

CREATE TABLE Cliente(
    id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(100) NOT NULL,
    endereco_cliente_id INT NOT NULL UNIQUE,
    CONSTRAINT fk_cliente_endereco 
    FOREIGN KEY (endereco_cliente_id) REFERENCES Endereco_Cliente(id)
);

CREATE TABLE Telefone(
    id INT PRIMARY KEY AUTO_INCREMENT,
    numero VARCHAR(15) NOT NULL,
    cliente_id INT NOT NULL,
    CONSTRAINT fk_telefone_cliente
    FOREIGN KEY (cliente_id) REFERENCES Cliente(id)
);

CREATE TABLE Cooperado(
    id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(100) NOT NULL,
    endereco_cooperado_id INT NOT NULL UNIQUE,
    CONSTRAINT fk_cooperado_endereco
    FOREIGN KEY (endereco_cooperado_id) REFERENCES Endereco_Cooperado(id)
);

CREATE TABLE Carteira(
    id INT PRIMARY KEY AUTO_INCREMENT,
    numero_carteira VARCHAR(20) NOT NULL UNIQUE,
    data_validade DATE NOT NULL,
    categoria VARCHAR(10) NOT NULL,
    cooperado_id INT NOT NULL,
    CONSTRAINT fk_carteira_cooperado 
    FOREIGN KEY (cooperado_id) REFERENCES Cooperado(id)
);


CREATE TABLE Veiculo(
    id INT PRIMARY KEY AUTO_INCREMENT,
    placa VARCHAR(8) NOT NULL UNIQUE,
    modelo VARCHAR(50) NOT NULL,
    fabricante VARCHAR(50) NOT NULL,
    cor VARCHAR(20) NOT NULL,
    cooperado_id INT NOT NULL,
    CONSTRAINT fk_veiculo_cooperado
    FOREIGN KEY (cooperado_id) REFERENCES Cooperado(id)
);


CREATE TABLE Corrida(
    id INT PRIMARY KEY AUTO_INCREMENT,
    endereco_Inicio_id INT NOT NULL UNIQUE,
    endereco_Fim_id INT NOT NULL UNIQUE,
    data_hora_inicio DATETIME NOT NULL,
    data_hora_fim DATETIME DEFAULT NULL,
    telefone_id INT NOT NULL,
    cliente_id INT NOT NULL,
    cooperado_id INT NOT NULL,
    veiculo_id INT NOT NULL,
    CONSTRAINT fk_corrida_endereco_inicio 
    FOREIGN KEY (endereco_Inicio_id) REFERENCES Endereco_Inicio(id),
    CONSTRAINT fk_corrida_endereco_fim 
    FOREIGN KEY (endereco_Fim_id) REFERENCES Endereco_Fim(id),
    CONSTRAINT fk_corrida_telefone 
    FOREIGN KEY (telefone_id) REFERENCES Telefone(id),
    CONSTRAINT fk_corrida_cliente 
    FOREIGN KEY (cliente_id) REFERENCES Cliente(id),
    CONSTRAINT fk_corrida_cooperado 
    FOREIGN KEY (cooperado_id) REFERENCES Cooperado(id),
    CONSTRAINT fk_corrida_veiculo
    FOREIGN KEY (veiculo_id) REFERENCES Veiculo(id)
);


INSERT INTO Endereco_Cliente(logradouro, bairro, cidade, estado) 
VALUES ('Rua Amador Bueno, 139','Centro','Campo Grande','MS'),  
	   ('Av. Afonso Pena, 500','Centro','Cuiabá','MT');  

INSERT INTO Endereco_Cooperado(logradouro, bairro, cidade, estado)
 VALUES ('Rua das Flores, 45','Jardim','Campo Grande','MS'),
	    ('Travessa Ortiz, 28','Centro','Rondonópolis','MT');

INSERT INTO Endereco_Inicio(logradouro, bairro, cidade, estado)
 VALUES ('Rua A, 100','Centro','Campo Grande','MS'),  
	    ('Av. Afonso Pena, 500','Centro','Cuiabá','MT');

INSERT INTO Endereco_Fim(logradouro, bairro, cidade, estado) 
VALUES ('Rua B, 200','Bela Vista','Campo Grande','MS'), 
	   ('Rodovia BR-070, km 12','Zona Rural','Rondonópolis','MT'); 

INSERT INTO Cliente(nome, endereco_cliente_id) 
VALUES ('Maria Silva', 1),
	   ('João Victor Otaviano', 2);

INSERT INTO Telefone(numero, cliente_id)
 VALUES ('67991234567', 1),
	    ('67933334444', 1),
	    ('65988887777', 2);

INSERT INTO Cooperado(nome, endereco_cooperado_id) 
VALUES ('Manoel Costa', 1),
	   ('Carlos Azevedo', 2);

INSERT INTO Carteira(numero_carteira, data_validade, categoria, cooperado_id) 
VALUES ('12345', '2028-05-10', 'B', 1),
	   ('54321', '2027-09-22', 'D', 2);

INSERT INTO Veiculo(placa, modelo, fabricante, cor, cooperado_id)
 VALUES('ABC1D23', 'Onix', 'Chevrolet', 'Branco', 1),
	   ('XYZ9E88', 'Corolla', 'Toyota', 'Prata', 1),
	   ('JJK4F12', 'Voyage', 'Volkswagen', 'Preto', 2);

INSERT INTO Corrida(endereco_inicio_id, endereco_fim_id, data_hora_inicio, data_hora_fim, telefone_id, cliente_id, cooperado_id, veiculo_id)
 VALUES (1, 1, '2026-10-01 08:00:00', '2026-10-01 08:35:00', 1, 1, 1, 1),
		(2, 2, '2026-10-02 14:00:00', NULL, 3, 2, 2, 3);
```



### Exercício 2: Sistema Controle de Corrida

## Enunciado
A firma Sonho Verde vende produtos de limpeza. Cada produto é caracterizado por
um código único, nome do produto, categoria (ex. detergente, sabão em pó,
sabonete, etc), data de fabricação, data de vencimento e seu preço. A firma possui
informações sobre todos seus clientes. Cada cliente é identificado por um código
único (também interno à firma), o nome do cliente, endereço (rua, número, sala,
cidade, cep, UF), telefone, filiação, status do cliente ("bom", "médio", "ruim"), e o seu
limite de crédito. Guarda-se igualmente a informação dos pedidos de produtos feitos
pelos clientes. Cada pedido possui um número (único), a data de elaboração do
pedido, quantidade de produtos pedidos, valor total do pedido. Cada pedido pode
envolver de um a vários produtos. Cada produto vendido pela firma possui o seu
fornecedor. Cada fornecedor possui CNPJ, Nome Fantasia, telefone, endereço, etc.
Um fornecedor pode fornecer vários produtos.

## Minha solução
```sql
CREATE DATABASE limpezadb;
USE limpezadb;

CREATE TABLE Fornecedor(
    id INT PRIMARY KEY AUTO_INCREMENT,
    cnpj VARCHAR(18) NOT NULL UNIQUE,
    nome_fantasia VARCHAR(100) NOT NULL,
    telefone VARCHAR(15) NOT NULL,
    endereco VARCHAR(200) NOT NULL
);

CREATE TABLE Produto(
    codigo INT PRIMARY KEY AUTO_INCREMENT,
    nome_produto VARCHAR(100) NOT NULL,
    categoria VARCHAR(50) NOT NULL,
    data_fabricacao DATE NOT NULL,
    data_vencimento DATE NOT NULL,
    preco FLOAT NOT NULL,
    fornecedor_id INT NOT NULL,
    CONSTRAINT fk_produto_fornecedor 
    FOREIGN KEY (fornecedor_id) REFERENCES Fornecedor(id)
);


CREATE TABLE Status(
    id INT PRIMARY KEY AUTO_INCREMENT,
    condicao_cliente VARCHAR(20) NOT NULL
);


CREATE TABLE Cliente(
    codigo INT PRIMARY KEY AUTO_INCREMENT,
    nome_cliente VARCHAR(100) NOT NULL,
    rua VARCHAR(150) NOT NULL,
    numero VARCHAR(10) NOT NULL,
    sala VARCHAR(10) DEFAULT NULL,
    cidade VARCHAR(50) NOT NULL,
    cep VARCHAR(9) NOT NULL,
    uf CHAR(2) NOT NULL,
    telefone VARCHAR(15) NOT NULL,
    filiacao VARCHAR(150) NOT NULL,
    status_id INT NOT NULL,
    limite_credito FLOAT NOT NULL,
    CONSTRAINT fk_cliente_status
    FOREIGN KEY (status_id) REFERENCES Status(id)
);

CREATE TABLE Pedido(
    numero INT PRIMARY KEY AUTO_INCREMENT,
    data_elaboracao DATE NOT NULL,
    quantidade_produtos INT NOT NULL,
    valor_total FLOAT NOT NULL,
    cliente_codigo INT NOT NULL,
    CONSTRAINT fk_pedido_cliente 
    FOREIGN KEY (cliente_codigo) REFERENCES Cliente(codigo)
);

CREATE TABLE Item_Pedido(
    id INT PRIMARY KEY AUTO_INCREMENT,
    pedido_numero INT NOT NULL,
    produto_codigo INT NOT NULL,
    quantidade INT NOT NULL,
    subtotal FLOAT NOT NULL,
    CONSTRAINT fk_item_pedido
    FOREIGN KEY (pedido_numero) REFERENCES Pedido(numero),
    CONSTRAINT fk_item_produto
    FOREIGN KEY (produto_codigo) REFERENCES Produto(codigo)
);

```

