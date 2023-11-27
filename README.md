# Comercio_Eletronico

Você foi designado para criar um sistema de comercio eletrônico. Aqui estão os detalhes adicionais:

### Tabelas:

Produtos: Armazene informações sobre produtos, como nome, descrição, preço e quantidade em estoque.

```mysql
CREATE TABLE Produtos (
    id_Produto INT NOT NULL AUTO_INCREMENT,
    Nome VARCHAR(200) NOT NULL,
    Descricao TEXT,
    Preco DECIMAL(10, 2) NOT NULL,
    QuantidadeEstoque INT NOT NULL,
    PRIMARY KEY (id_Produto)
);
```

Pedidos: Registre detalhes de pedidos, incluindo data, cliente e status.

```mysql
CREATE TABLE Pedidos (
    Id_Pedido INT NOT NULL AUTO_INCREMENT,
    Data_Pedido DATE NOT NULL,
    Cliente_id INT NOT NULL,
    Status VARCHAR(200) NOT NULL,
    PRIMARY KEY (Id_Pedido),
    FOREIGN KEY (Cliente_id) REFERENCES Clientes(Cliente_id)
);
```
Clientes: Mantenha informações dos clientes, como nome, endereço de entrega e informações de contato.

```mysql
CREATE TABLE Clientes (
    Cliente_id INT NOT NULL AUTO_INCREMENT,
    Nome VARCHAR(200) NOT NULL,
    EnderecoEntrega VARCHAR(255),
    Telefone VARCHAR(20) NOT NULL,
    Email VARCHAR(200) NOT NULL,
    PRIMARY KEY (Cliente_id)
);
```
Itens de Pedido: Registre os produtos incluídos em cada pedido, junto com a quantidade.

```mysql
CREATE TABLE Itens_Pedido (
    id_Item INT NOT NULL AUTO_INCREMENT,
    Id_Pedido INT NOT NULL,
    id_Produto INT NOT NULL,
    Quantidade INT NOT NULL,
    PRIMARY KEY (id_Item),
    FOREIGN KEY (Id_Pedido) REFERENCES Pedidos(Id_Pedido),
    FOREIGN KEY (id_Produto) REFERENCES Produtos(id_Produto)
);
```

### Relacionamentos:

Crie um relacionamento entre "Pedidos" e "Clientes" para rastrear os pedidos de cada cliente.

Estabeleça um relacionamento entre "Itens de Pedido" e "Produtos" para associar produtos a pedidos.
```mysql
-- Na tabela Pedidos, a coluna Cliente_id é uma chave estrangeira que faz referência à coluna Cliente_id na tabela Clientes.
FOREIGN KEY (Cliente_id) REFERENCES Clientes(Cliente_id)

-- Na tabela Itens_Pedido, as colunas Id_Pedido e id_Produto são chaves estrangeiras que fazem referência às colunas correspondentes nas tabelas Pedidos e Produtos
FOREIGN KEY (Id_Pedido) REFERENCES Pedidos(Id_Pedido),
FOREIGN KEY (id_Produto) REFERENCES Produtos(id_Produto)
```

diagrama lógico:




### Stored Procedures:

Implemente uma stored procedure para permitir que os clientes adicionem produtos ao carrinho de compras.

```sql
delimiter $
create procedure adicionar_produtos_carrinho(
  IN id_produto INT,
  IN quantidade INT
)
BEGIN

  INSERT INTO itens_de_pedido ( produtos_idProdutos, quantidadePedido)
  VALUES (id_produto, quantidade);
  INSERT INTO Produtos_has_Pedidos (Produtos_idProdutos)
SELECT idProdutos
FROM Itens_de_pedido;


END$
delimiter ;
```

Crie uma stored procedure para processar pedidos, atualizando o estoque de produtos e registrando os detalhes do pedido.

```sql
DELIMITER $
CREATE PROCEDURE ProcessarPedido(
IN PedidoID INT,
IN id_cliente INT,
IN data DATETIME
)
BEGIN
 DECLARE id_pedido INT;

  -- Incrementar o id do pedido
  SET id_pedido = (SELECT MAX(idPedidos) + 1 FROM pedidos);

  -- Inserir os detalhes do pedido
  INSERT INTO pedidos (idPedidos, data, Clientes_idClientes, status)
  VALUES (id_pedido, data, id_cliente, 'Processado');

  -- Atualizar o estoque dos produtos
  UPDATE produtos
  SET Quantidade_estoque = Quantidade_estoque - 
    (SELECT quantidadePedido FROM itens_de_pedido WHERE Produtos_idProdutos = PedidoID);

END$
DELIMITER ;
```
Desenvolva uma stored procedure para calcular o total de um pedido com base nos produtos incluídos.

```sql

DELIMITER $
CREATE PROCEDURE CalcularTotalDoPedido(IN PedidoID INT, OUT Total DECIMAL(10, 2))
BEGIN
  SELECT SUM(Produtos.preco * Itens_de_pedido.quantidadePedido) INTO Total
  FROM Itens_de_pedido
  JOIN Produtos ON Itens_de_pedido.Produtos_idProdutos = Produtos.idProdutos
  WHERE Itens_de_pedido.idItens_de_pedido = PedidoID;
END;$
DELIMITER ;

```

### Views:

Crie uma view que mostre o histórico de pedidos de um cliente específico, incluindo os produtos incluídos em cada pedido.

```sql
CREATE VIEW HistoricoDePedidos AS
SELECT Pedidos.idPedidos, Pedidos.data, Pedidos.status, Clientes.nomeCliente
FROM Pedidos
JOIN Clientes ON Pedidos.Clientes_idClientes = Clientes.idClientes;
```

Implemente uma view que forneça uma lista de todos os produtos disponíveis, excluindo aqueles que estão esgotados.
```sql
CREATE VIEW ProdutosDisponiveis AS
SELECT idProdutos, nome, descricao, preco, Quantidade_estoque
FROM Produtos
WHERE Quantidade_estoque > 0;
```

### inserções:

Produtos:
```sql
INSERT INTO Produtos (nome, descricao, preco, Quantidade_estoque)
VALUES
  ('Smartphone iPhone 13', 'Tela de 6.1", 128GB, Azul', 999.99, 50),
  ('Notebook Dell XPS 15', '15.6" 4K UHD, i7, 16GB RAM', 1499.99, 30),
  ('Televisão Samsung 4K', '55 polegadas QLED Smart TV', 799.99, 20),
  ('Console de Jogos PlayStation 5', 'SSD 825GB, 4K Ultra HD', 499.99, 10),
  ('Fone de Ouvido Bose QC 35', 'Cancelamento de Ruído', 199.99, 60),
  ('Câmera Canon EOS 5D Mark IV', '30.4 MP, 4K Video', 2499.99, 15),
  ('Impressora HP LaserJet Pro', 'Wireless, Impressão Duplex', 149.99, 25),
  ('Tablet Samsung Galaxy Tab S7', '11" AMOLED, 128GB', 399.99, 40),
  ('Monitor LG UltraWide', '34" Curved 21:9, QHD', 299.99, 12),
  ('Refrigerador LG French Door', '26.2 cu. ft., Aço inoxidável', 1499.99, 8),
  ('Máquina de Lavar Samsung', 'Front Load, 4.5 cu. ft.', 599.99, 18),
  ('Aspirador de Pó Dyson V11', 'Bateria de 60 minutos', 299.99, 30),
  ('Batedeira KitchenAid', '5 Quart, 10 velocidades', 129.99, 20),
  ('Panela Instant Pot Duo', '7-em-1, 6 Quart', 89.99, 25),
  ('Micro-ondas Panasonic', '1100W, 1.2 cu. ft.', 79.99, 15),
  ('Sofá de Couro Natuzzi', '3 Lugares, Marrom', 899.99, 10),
  ('Mesa de Jantar Ashley', 'Madeira maciça, 6 Cadeiras', 699.99, 10),
  ('Cama King Size Sleep Number', 'Ajustável, Memory Foam', 1499.99, 5),
  ('Máquina de Exercícios Peloton', 'Tela sensível ao toque', 1999.99, 10);
  
```

Clientes:
```sql
 INSERT INTO Clientes (nomeCliente, endereco, telefone, email)
VALUES
  ('Maria Silva', '123 Rua A, Cidade X', '(12) 9456-7890', 'maria@email.com'),
  ('João Santos', '456 Rua B, Cidade Y', '(32) 9654-0987', 'joao@email.com'),
  ('Ana Pereira', '789 Rua C, Cidade Z', '(98) 9321-6540', 'ana@email.com'),
  ('Carlos Rodrigues', '234 Avenida A, Cidade W', '(65) 9987-3210', 'carlos@email.com'),
  ('Isabela Gomes', '567 Avenida B, Cidade V', '(23) 9567-8901', 'isabela@email.com'),
  ('Lucas Ferreira', '890 Estrada A, Cidade U', '(80) 9234-5670', 'lucas@email.com'),
  ('Sofia Oliveira', '123 Estrada B, Cidade T', '(15) 9678-9012', 'sofia@email.com'),
  ('Gustavo Sousa', '456 Avenida C, Cidade S', '(12) 9901-2345', 'gustavo@email.com'),
  ('Larissa Costa', '789 Rua D, Cidade R', '(11) 9123-4567', 'larissa@email.com'),
  ('Pedro Alves', '234 Rua E, Cidade Q', '(11) 9109-8765', 'pedro@email.com'),
  ('Mariana Santos', '567 Estrada C, Cidade P', '(11) 9210-9876', 'mariana@email.com'),
  ('Rafael Lima', '890 Avenida D, Cidade O', '(11) 9321-0987', 'rafael@email.com'),
  ('Camila Pereira', '123 Rua F, Cidade N', '(15) 9432-1098', 'camila@email.com'),
  ('Enzo Carvalho', '456 Estrada D, Cidade M', '(15) 9543-2109', 'enzo@email.com'),
  ('Lívia Fernandes', '789 Estrada E, Cidade L', '(15) 9654-3210', 'livia@email.com'),
  ('Thiago Rocha', '234 Avenida E, Cidade K', '(15) 9765-4321', 'thiago@email.com'),
  ('Amanda Martins', '567 Rua G, Cidade J', '(21) 9876-5432', 'amanda@email.com'),
  ('Diego Santos', '890 Avenida F, Cidade I', '(32) 9987-6543', 'diego@email.com'),
  ('Laura Costa', '123 Estrada F, Cidade H', '(43) 9210-8765', 'laura@email.com'),
  ('Raul Lima', '456 Avenida G, Cidade G', '(54) 9109-7654', 'raul@email.com');
```
Adicionar no carinho: 
```sql
call adicionar_produtos_carrinho(1,2);
call adicionar_produtos_carrinho(2,2);
call adicionar_produtos_carrinho(3,3);
call adicionar_produtos_carrinho(4,4);
call adicionar_produtos_carrinho(5,6);
call adicionar_produtos_carrinho(6,7);
call adicionar_produtos_carrinho(7,5);
call adicionar_produtos_carrinho(8,8);
call adicionar_produtos_carrinho(9,9);
call adicionar_produtos_carrinho(10,10);
call adicionar_produtos_carrinho(11,12);
call adicionar_produtos_carrinho(12,11);
call adicionar_produtos_carrinho(13,13);
call adicionar_produtos_carrinho(14,1);
call adicionar_produtos_carrinho(15,5);
call adicionar_produtos_carrinho(16,14);
call adicionar_produtos_carrinho(17,15);
call adicionar_produtos_carrinho(18,19);
call adicionar_produtos_carrinho(19,5);
```
Processar Pedido:
```sql
call ProcessarPedido(1,1,'2023-10-13');
call ProcessarPedido(2,2,'2023-10-15');
call ProcessarPedido(3,3,'2023-10-30');
call ProcessarPedido(4,4,'2023-09-25');
call ProcessarPedido(5,5,'2023-07-12');
call ProcessarPedido(6,6,'2023-02-10');
call ProcessarPedido(7,7,'2023-01-11');
call ProcessarPedido(8,8,'2023-12-12');
call ProcessarPedido(9,9,'2023-11-23');
call ProcessarPedido(10,10,'2023-10-10');
call ProcessarPedido(11,11,'2023-11-01');
call ProcessarPedido(12,12,'2023-04-18');
call ProcessarPedido(13,13,'2023-03-18');
call ProcessarPedido(14,14,'2023-02-08');
call ProcessarPedido(15,15,'2023-12-09');
call ProcessarPedido(16,16,'2023-02-08');
call ProcessarPedido(17,17,'2023-12-01');
call ProcessarPedido(18,18,'2023-11-11');
call ProcessarPedido(19,19,'2023-09-01');
call ProcessarPedido(20,20,'2023-09-12');

```
