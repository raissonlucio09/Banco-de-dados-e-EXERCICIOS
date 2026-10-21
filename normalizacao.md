# Normalização

Técnica para **organizar as tabelas** eliminando **redundância** e **anomalias** (inserção, atualização, exclusão). O erro mostrado nos slides (repetir os dados do cliente a cada pedido) é exatamente um problema de normalização.

Tabela não normalizada
| pedido | cliente | telefones | produtos |
|---|---|---|---|
| 1 | João | 9999, 8888 | Mouse, Teclado |

Problemas: campos com vários valores, dados do cliente repetidos...

1ª Forma Normal (1FN)
- Cada coluna guarda **um único valor** (atômico).
- Sem grupos repetidos.
- Existe chave primária.

❌ `telefones = '9999, 8888'` → ✅ tabela `telefone` separada (1 cliente : N telefones).

2ª Forma Normal (2FN)
- Estar na 1FN **e**
- Nenhum atributo depende de **parte** da chave composta (**dependência parcial**).

❌ `item_pedido(pedido_id, produto_id, qtd, nome_produto)`: `nome_produto` depende só de `produto_id`.
✅ Mover `nome_produto` para a tabela `produto`.

 3ª Forma Normal (3FN)
- Estar na 2FN **e**
- Nenhum atributo não-chave depende de outro não-chave (**dependência transitiva**).

❌ `cliente(id, nome, cep, cidade, estado)`: `cidade` e `estado` dependem do `cep`.
✅ Criar `cidade`/`endereco` separado (ou aceitar a desnormalização consciente).

Exemplo completo
Antes (tudo numa tabela)
```sql
CREATE TABLE pedido_ruim (
    pedido_id INT, data DATE,
    cliente_nome VARCHAR(50), cliente_email VARCHAR(100), cliente_telefones VARCHAR(100),
    produto_nome VARCHAR(50), produto_preco DECIMAL(10,2), quantidade INT
);
```
 Depois (3FN)
```sql
CREATE TABLE cliente  (id INT PRIMARY KEY AUTO_INCREMENT, nome VARCHAR(50) NOT NULL, email VARCHAR(100) UNIQUE);
CREATE TABLE telefone (id INT PRIMARY KEY AUTO_INCREMENT, cliente_id INT NOT NULL, numero VARCHAR(15) NOT NULL,
                       FOREIGN KEY (cliente_id) REFERENCES cliente(id));
CREATE TABLE produto  (id INT PRIMARY KEY AUTO_INCREMENT, nome VARCHAR(50) NOT NULL, preco DECIMAL(10,2) NOT NULL);
CREATE TABLE pedido   (id INT PRIMARY KEY AUTO_INCREMENT, data DATE NOT NULL, cliente_id INT NOT NULL,
                       FOREIGN KEY (cliente_id) REFERENCES cliente(id));
CREATE TABLE item_pedido (
    pedido_id INT, produto_id INT, quantidade INT NOT NULL,
    preco_unitario DECIMAL(10,2) NOT NULL,   -- preço NA ÉPOCA da compra (propositalmente copiado)
    PRIMARY KEY (pedido_id, produto_id),
    FOREIGN KEY (pedido_id) REFERENCES pedido(id),
    FOREIGN KEY (produto_id) REFERENCES produto(id)
);
```
Note o `preco_unitario`: copiar o preço no item **não é erro** — o preço do produto muda com o tempo e o pedido deve guardar o valor histórico.

 Desnormalização
Às vezes quebra-se a regra **de propósito** para ganhar performance em consultas pesadas (ex.: `valor_total` já guardado no pedido). Faça com consciência.
