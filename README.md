# Comercio_Eletronico

Você foi designado para criar um sistema de comercio eletrônico. Aqui estão os detalhes adicionais:

### Tabelas:

Produtos: Armazene informações sobre produtos, como nome, descrição, preço e quantidade em estoque.

Pedidos: Registre detalhes de pedidos, incluindo data, cliente e status.

Clientes: Mantenha informações dos clientes, como nome, endereço de entrega e informações de contato.

Itens de Pedido: Registre os produtos incluídos em cada pedido, junto com a quantidade.

### Relacionamentos:

Crie um relacionamento entre "Pedidos" e "Clientes" para rastrear os pedidos de cada cliente.

Estabeleça um relacionamento entre "Itens de Pedido" e "Produtos" para associar produtos a pedidos.

diagrama lógico:

![diagrama-lógico](Diagrama_lógico.png)


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
