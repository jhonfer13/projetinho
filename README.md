-- Criar banco de dados
CREATE DATABASE IF NOT EXISTS ecommerce;
USE ecommerce;

-- =========================
-- Tabela de Clientes
-- =========================
CREATE TABLE clients (
    idClient INT AUTO_INCREMENT PRIMARY KEY,
    Fname VARCHAR(10),
    Minit CHAR(3),
    Lname VARCHAR(20),
    CPF CHAR(11),
    CNPJ CHAR(15),
    Address VARCHAR(30),
    CONSTRAINT unique_CPF_client UNIQUE (CPF),
    CONSTRAINT unique_CNPJ_client UNIQUE (CNPJ),
    CONSTRAINT chk_client_type CHECK (
        (CPF IS NOT NULL AND CNPJ IS NULL) OR 
        (CPF IS NULL AND CNPJ IS NOT NULL)
    )
);

-- =========================
-- Tabela de Produtos
-- =========================
CREATE TABLE product (
    idProduct INT AUTO_INCREMENT PRIMARY KEY,
    Pname VARCHAR(10) NOT NULL,
    Classification_Kids BOOLEAN DEFAULT FALSE,
    Category ENUM('Eletrônico', 'Vestimenta', 'Brinquedos', 'Alimentos', 'Móveis') NOT NULL,
    Avaliacao FLOAT DEFAULT 0,
    Size VARCHAR(10)
);

-- =========================
-- Tabela de Pedidos
-- =========================
CREATE TABLE orders (
    idOrder INT AUTO_INCREMENT PRIMARY KEY,
    idOrderClient INT NOT NULL,
    orderStatus ENUM('Cancelado', 'Confirmado', 'Em processamento') DEFAULT 'Em processamento',
    orderDescription VARCHAR(255),
    sendValue FLOAT DEFAULT 10,
    CONSTRAINT fk_orders_client FOREIGN KEY(idOrderClient) REFERENCES clients(idClient)
);

-- =========================
-- Tabela de Armazenamento de Produtos
-- =========================
CREATE TABLE productStorage (
    idProdStorage INT AUTO_INCREMENT PRIMARY KEY,
    storageLocation VARCHAR(255),
    quantity INT DEFAULT 0
);

-- =========================
-- Tabela de Fornecedores
-- =========================
CREATE TABLE supplier (
    idSupplier INT AUTO_INCREMENT PRIMARY KEY,
    socialName VARCHAR(255) NOT NULL,
    CNPJ CHAR(15) NOT NULL,
    contact CHAR(11) NOT NULL,    
    CONSTRAINT unique_supplier UNIQUE (CNPJ)
);

-- =========================
-- Tabela de Vendedores
-- =========================
CREATE TABLE seller (
    idSeller INT AUTO_INCREMENT PRIMARY KEY,
    socialName VARCHAR(255) NOT NULL,
    abstName VARCHAR(255),
    CNPJ CHAR(15),
    CPF CHAR(11),
    location VARCHAR(255),
    contact CHAR(11) NOT NULL,
    CONSTRAINT unique_cnpj_seller UNIQUE (CNPJ),
    CONSTRAINT unique_cpf_seller UNIQUE (CPF)
);

-- =========================
-- Tabela Produto-Vendedor
-- =========================
CREATE TABLE productSeller (
    idPseller INT,
    idPproduct INT,
    prodQuantity INT DEFAULT 1,
    PRIMARY KEY (idPseller, idPproduct),
    CONSTRAINT fk_product_seller FOREIGN KEY (idPseller) REFERENCES seller(idSeller),
    CONSTRAINT fk_product_product FOREIGN KEY (idPproduct) REFERENCES product(idProduct)
);

-- =========================
-- Tabela Produto-Pedido
-- =========================
CREATE TABLE productOrder (
    idPOproduct INT,
    idPOorder INT,
    poQuantity INT DEFAULT 1,
    poStatus ENUM('Disponível', 'Sem estoque') DEFAULT 'Disponível',
    PRIMARY KEY (idPOproduct, idPOorder),
    CONSTRAINT fk_productorder_product FOREIGN KEY (idPOproduct) REFERENCES product(idProduct),
    CONSTRAINT fk_productorder_order FOREIGN KEY (idPOorder) REFERENCES orders(idOrder)
);

-- =========================
-- Tabela Localização do Produto
-- =========================
CREATE TABLE storageLocation (
    idLproduct INT,
    idLstorage INT,
    location VARCHAR(255) NOT NULL,
    PRIMARY KEY (idLproduct, idLstorage),
    CONSTRAINT fk_storage_location_product FOREIGN KEY (idLproduct) REFERENCES product(idProduct),
    CONSTRAINT fk_storage_location_storage FOREIGN KEY(idLstorage) REFERENCES productStorage(idProdStorage)
);

-- =========================
-- Tabela Produto-Fornecedor
-- =========================
CREATE TABLE productSupplier (
    idPsSupplier INT,
    idPsProduct INT,
    quantity INT NOT NULL,
    PRIMARY KEY (idPsSupplier, idPsProduct),
    CONSTRAINT fk_product_supplier_supplier FOREIGN KEY (idPsSupplier) REFERENCES supplier(idSupplier),
    CONSTRAINT fk_product_supplier_product FOREIGN KEY (idPsProduct) REFERENCES product(idProduct)
);

-- =========================
-- Tabela Métodos de Pagamento
-- =========================
CREATE TABLE paymentMethod (
    idPayment INT AUTO_INCREMENT PRIMARY KEY,
    paymentType ENUM('Dinheiro', 'Cartão', 'PIX', 'Boleto') NOT NULL
);

-- =========================
-- Tabela Pedido-Pagamento (Muitos para Muitos)
-- =========================
CREATE TABLE orderPayment (
    idOrder INT,
    idPayment INT,
    amount FLOAT NOT NULL,
    PRIMARY KEY(idOrder, idPayment),
    CONSTRAINT fk_orderpayment_order FOREIGN KEY(idOrder) REFERENCES orders(idOrder),
    CONSTRAINT fk_orderpayment_payment FOREIGN KEY(idPayment) REFERENCES paymentMethod(idPayment)
);

-- =========================
-- Tabela Entrega
-- =========================
CREATE TABLE delivery (
    idDelivery INT AUTO_INCREMENT PRIMARY KEY,
    idOrder INT NOT NULL,
    deliveryStatus ENUM('Pendente', 'Em trânsito', 'Entregue', 'Cancelado') DEFAULT 'Pendente',
    trackingCode VARCHAR(50),
    estimatedDeliveryDate DATE,
    CONSTRAINT fk_delivery_order FOREIGN KEY(idOrder) REFERENCES orders(idOrder)
);

-- =========================
-- Exibir todas as tabelas
-- =========================
SHOW TABLES;
