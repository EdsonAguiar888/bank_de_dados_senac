-----
# ☕ Estrutura e Dados da Cafeteria (SQL)
Este arquivo contém o script SQL para a criação e povoamento de um banco de dados simples para uma cafeteria.

📜 Criação do Banco de Dados e Tabelas
O script começa com a criação do banco de dados e das tabelas necessárias: Clientes, Produtos, Pedidos e Itens_Pedido.

### CRIAÇÃO DO BANCO DE DADOS
```sql
CREATE DATABASE Cafeteria;
USE Cafeteria; --> Comando específico para MySQL/SQL Server para selecionar o DB
```

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
