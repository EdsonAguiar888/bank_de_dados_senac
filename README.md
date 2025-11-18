-----
# ☕ Documentação do Banco de Dados: Cafeteria

Este documento traz exemplos de comandos para criar, excluir, inserir, e atualizar informações do banco de dados, colocados em destaque no início do documento. para o banco de dados `Cafeteria`.

## 🔎 Consultas SQL de Exemplo

### Consultas Básicas (Seleção de todas as colunas)

```sql
select * from clientes;
select * from pedidos;
select * from produtos;
```

-----

### 1\. Listar Produtos na Categoria 'Café'

Consulta todos os produtos na categoria 'Café' que estão marcados como ativos.

```sql
SELECT nome_produto, preco 
FROM Produtos 
WHERE categoria = 'Café' 
AND ativo = TRUE;
```

-----

### 2\. Consultar Pedidos com Nome do Cliente (JOIN)

Retorna o ID do pedido, o nome do cliente, a data/hora, o valor total e o status, unindo as tabelas `Pedidos` e `Clientes`.

```sql
SELECT
    P.id_pedido,
    C.nome_cliente,
    P.data_hora_pedido,
    P.valor_total,
    P.status
FROM Pedidos P
INNER JOIN Clientes C ON P.id_cliente = C.id_cliente;
```
-----

## ✏️ Alterar Dados (UPDATE)
### O comando UPDATE é usado para modificar os valores de um ou mais campos em uma tabela.

 Exemplo 1: Alterar o status de um Pedido
Vamos alterar o status do Pedido 2 de 'Em Preparo' para 'Finalizado'.
```sql

UPDATE Pedidos
SET status = 'Finalizado'
WHERE id_pedido = 2;
```

 Exemplo 2: Alterar o Preço de um Produto
Vamos aumentar o preço do 'Espresso' para R$ 6,00.
```sql

UPDATE Produtos
SET preco = 6.00
WHERE nome_produto = 'Espresso';
```

 Exemplo 3: Adicionar Email a um Cliente (preenchendo um campo nulo)
O 'Carlos Lima' estava sem email. Vamos adicioná-lo.
```sql

UPDATE Clientes
SET email = 'carlos.l@exemplo.com'
WHERE nome_cliente = 'Carlos Lima';
```


## ❌ Excluir Dados (DELETE)
### O comando DELETE é usado para remover linhas de uma tabela.

Exemplo 1: Excluir um Produto
Vamos excluir o produto 'Cheesecake' (id_produto 4).

-- Primeiro, é crucial garantir que nenhum item de pedido dependa deste produto,
-- ou a exclusão falhará devido à Chave Estrangeira (FOREIGN KEY).
-- Como a cheesecake não está nos pedidos de exemplo, podemos excluir diretamente:
```sql
    
DELETE FROM Produtos
WHERE nome_produto = 'Cheesecake';
```

Exemplo 2: Excluir um Cliente e Seus Pedidos (Necessidade de Cuidado)
Se você tentar excluir um cliente que tenha pedidos registrados, o banco de dados geralmente bloqueará a operação (se as restrições de FOREIGN KEY estiverem ativas) para evitar pedidos "órfãos". Você precisa excluir os dados dependentes primeiro.

1. Excluir Itens do Pedido
Primeiro, exclua os itens de pedido que pertencem aos pedidos do cliente.
-- Exclui os itens do Pedido 1 (pertencente à Ana - id_cliente 1)
```sql

DELETE FROM Itens_Pedido
WHERE id_pedido IN (SELECT id_pedido FROM Pedidos WHERE id_cliente = 1);
```

2. Excluir o Pedido
Em seguida, exclua o pedido principal.
-- Exclui o Pedido 1
```sql
DELETE FROM Pedidos
WHERE id_cliente = 1;
```

3. Excluir o Cliente
Finalmente, você pode excluir o cliente.

-- Exclui a cliente Ana Silva (id_cliente 1)
```sql
DELETE FROM Clientes
WHERE id_cliente = 1;
```
-----

## 🛠️ Estrutura do Banco de Dados (Schema)

O banco de dados `Cafeteria` possui as tabelas `Clientes`, `Produtos`, `Pedidos` e `Itens_Pedido`.

### 1\. Clientes

```sql
CREATE TABLE Clientes (
    id_cliente INT PRIMARY KEY AUTO_INCREMENT,
    nome_cliente VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE,
    telefone VARCHAR(20),
    data_cadastro DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

### 2\. Produtos

```sql
CREATE TABLE Produtos (
    id_produto INT PRIMARY KEY AUTO_INCREMENT,
    nome_produto VARCHAR(100) NOT NULL,
    descricao TEXT,
    preco DECIMAL(10, 2) NOT NULL,
    categoria VARCHAR(50),
    ativo BOOLEAN DEFAULT TRUE
);
```

### 3\. Pedidos

```sql
CREATE TABLE Pedidos (
    id_pedido INT PRIMARY KEY AUTO_INCREMENT,
    id_cliente INT,
    data_hora_pedido DATETIME DEFAULT CURRENT_TIMESTAMP NOT NULL,
    valor_total DECIMAL(10, 2),
    status VARCHAR(50) NOT NULL, 
    FOREIGN KEY (id_cliente) REFERENCES Clientes(id_cliente)
);
```

### 4\. Itens\_Pedido

```sql
CREATE TABLE Itens_Pedido (
    id_item_pedido INT PRIMARY KEY AUTO_INCREMENT,
    id_pedido INT NOT NULL,
    id_produto INT NOT NULL,
    quantidade INT NOT NULL,
    preco_unitario DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (id_pedido) REFERENCES Pedidos(id_pedido),
    FOREIGN KEY (id_produto) REFERENCES Produtos(id_produto)
);
```

-----

## 💾 Dados de Exemplo Inseridos

Os seguintes dados foram inseridos nas tabelas para testes.

### 📝 Clientes

```sql
INSERT INTO Clientes (nome_cliente, email, telefone) VALUES
('Ana Silva', 'ana.s@exemplo.com', '11987654321'),
('Bruno Costa', 'bruno.c@exemplo.com', '21991234567'),
('Carlos Lima', NULL, '31988887777');
```

### 🍔 Produtos

```sql
INSERT INTO Produtos (nome_produto, descricao, preco, categoria) VALUES
('Espresso', 'Café puro e concentrado', 5.50, 'Café'),
('Cappuccino', 'Café, leite e espuma de leite', 8.00, 'Café'),
('Pão de Queijo', 'Porção com 3 unidades', 6.00, 'Lanche'),
('Cheesecake', 'Fatia de bolo de queijo com calda de frutas vermelhas', 15.00, 'Sobremesa');
```

### 🛒 Pedidos

```sql
-- Pedido 1: Ana (id_cliente 1)
INSERT INTO Pedidos (id_cliente, valor_total, status) VALUES
(1, 19.50, 'Finalizado'); 

-- Pedido 2: Bruno (id_cliente 2)
INSERT INTO Pedidos (id_cliente, valor_total, status) VALUES
(2, 14.00, 'Em Preparo');
```

### 🛍️ Itens\_Pedido

```sql
-- Itens para o Pedido 1 (id_pedido 1)
INSERT INTO Itens_Pedido (id_pedido, id_produto, quantidade, preco_unitario) VALUES
(1, 1, 1, 5.50), -- Espresso
(1, 2, 1, 8.00), -- Cappuccino
(1, 3, 1, 6.00); -- Pão de Queijo

-- Itens para o Pedido 2 (id_pedido 2)
INSERT INTO Itens_Pedido (id_pedido, id_produto, quantidade, preco_unitario) VALUES
(2, 2, 1, 8.00), -- Cappuccino
(2, 3, 1, 6.00); -- Pão de Queijo
```

-----


