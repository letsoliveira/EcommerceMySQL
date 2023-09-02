# EcommerceMySQL
Desafio Projeto lógico de banco de dados: Ecommerce MySQL

-- criar banco de dados para o cenário de E-commerce
-- drop database ecommerce;
create database ecommerce;
use ecommerce;

-- criar tabela cliente
create table clients(
		idClient int auto_increment primary key,
        Fname varchar(10),
        Minit char(3),
        Lname varchar(20),
        CPF char(11) not null,
        Address varchar(30),
        constraint unique_cpf_client unique (CPF)
);
alter table clients auto_increment = 1;
-- desc clients;

-- criar tabela produto
create table product(
		idProduct int auto_increment primary key,
        Pname varchar(10) not null,
        classificationKids bool default false,
        Category enum('Eletrônico','Moda','Brinquedos','Decoração','Alimentos','Móveis') not null,
        Evaluation float default 0,
        Size varchar(10)
);
-- ALTER TABLE product MODIFY Pname varchar(60);
-- desc product;

-- criar tabela pagamento
create table payments(
		idClient int,
        idPayment int,
        typePayment enum('Boleto','Cartão de crédito', 'Cartão de débito', 'Dois cartões', 'PIX'),
        limitAvailable float,
        primary key(idClient, idPayment)
);

-- criar tabela pedido
-- drop table orders;
create table orders(
		idOrder int auto_increment primary key,
        idOrdersClient int,
        ordersStatus enum('Cancelado','Confirmado','Em processamento') default 'Em processamento',
        ordersDescription varchar(255),
        Shipping float default 10,
        paymentCash boolean default false,
        constraint fk_orders_client foreign key (idOrdersClient) references clients(idClient)
			on update cascade
);
-- desc orders;

-- criar tabela estoque
create table productStorage(
		idProdStorage int auto_increment primary key,
        storageLocation varchar(255),
        Quantity int default 0
);

-- criar tabela fornecedor
create table supplier(
		idSupplier int auto_increment primary key,
        companyName varchar(50) not null,
        CNPJ char(15) not null,
        Contact char(11) not null,
        constraint unique_supplier unique (CNPJ)
);
-- desc supplier;

-- criar tabela vendedor
create table seller(
		idSeller int auto_increment primary key,
        companyName varchar(255) not null,
        abstName varchar(255),
        CNPJ char(15),
		CPF char(9),
        Contact char(11) not null,
        sellerLocation varchar(255),
        constraint unique_cnpj_seller unique (CNPJ),
        constraint unique_cpf_seller unique (CPF)		
);

-- criar tabela N-N produtos - vendedor
create table productSeller(
		idPseller int,		
        idPproduct int,
        prodQuantity int default 1,
        primary key (idPseller, idPproduct),
		constraint fk_product_seller foreign key (idPseller) references seller (idSeller),
		constraint fk_product_product foreign key (idPproduct) references product (idProduct)       
);
-- desc productSeller;

-- criar tabela N-N produtos - pedidos
-- drop table productOrder;
create table productOrder(
		idPOproduct int,		
        idPOorder int,
        poQuantity int default 1,
        poStatus enum ('Disponível', 'Sem estoque') default 'Disponível',
        primary key (idPOproduct, idPOorder),
		constraint fk_po_orders foreign key (idPOorder) references orders (idOrder),
		constraint fk_po_product foreign key (idPOproduct) references product (idProduct)       
);

-- criar tabela N-N produtos - estoque
create table storageLocation(
		idLproduct int,		
        idLstorage int,
        location varchar(255) not null,
        primary key (idLproduct, idLstorage),
		constraint fk_ls_product foreign key (idLproduct) references product (idProduct),
		constraint fk_ls_storage foreign key (idLstorage) references productStorage (idProdStorage)       
);

-- criar tabela N-N produtos - fornecedor
create table productSupplier(
		idPSsupplier int,		
        idPSproduct int,
        PSQuantity int not null,
        primary key (idPSsupplier, idPSproduct),
		constraint fk_ps_supplier foreign key (idPSsupplier) references supplier (idSupplier),
		constraint fk_ps_product foreign key (idPSproduct) references product (idProduct)       
);
show tables;
-- show databases;
-- use information_schema;
-- desc referential_constraints;
-- select * from referential_constraints where constraint_schema = 'ecommerce';

INSERT INTO clients (Fname, Minit, Lname, CPF, Address)
	values
		('Ana','C','Silva',12346789,'Rua um, 12, Numero - Numeral'),
		('Cristina','S','Souza',62385789,'Rua dois, 24, Dia - Diario'),
		('Marcos','D','Cavalcante',57385659,'Rua tres, 32, Mes - Mesario');      

INSERT INTO product (Pname, classificationKids, Category, Evaluation, Size)
	values
		('Mouse', false, 'Eletrônico', '4', '3x3x5'),
		('Max steel', true, 'Brinquedos', '5', '20x5x5'),
        ('Par de meias', true, 'Moda', '3', null),
        ('Poltrona', false, 'Móveis', '2', '100x100x50');

-- delete from orders where idOrdersClient in (1,2,3,4);
INSERT INTO orders (idOrdersClient, ordersStatus, ordersDescription, Shipping, paymentCash)
	values
		(1, 'Em processamento', 'Compra via aplicativo', 10, 1),
		(1, 'Confirmado', 'Compra via web site', 20, 0),
        (2, 'Em processamento', 'Compra via aplicativo', 20, 0),
        (3, 'Confirmado', 'Compra via web site', 0, 0),
        (3, 'Confirmado', 'Compra via web site', 20, 1);    
 
 INSERT INTO productOrder (idPOproduct, idPOorder, poQuantity, poStatus)
	values
		(5, 20, 2, 'Disponível'),
        (6, 21, 3, 'Disponível'),
        (7, 22, 0, 'Sem estoque'),
        (8, 23, 1, 'Disponível'); 
        
INSERT INTO productStorage (storageLocation, Quantity)
	values
		('RJ',1000),
        ('SP',500),
        ('RJ',10),
        ('SP',200),
        ('BA',1000),
        ('MG',500);

INSERT INTO storageLocation (idLproduct, idLstorage, location)
	values
		(5, 1, 'RJ'),
        (6, 2, 'SP'),
        (7, 4, 'SP'),
        (8, 6, 'MG');
        
INSERT INTO supplier (companyName, CNPJ, Contact)
	values
		('Maçã do amor', 123456789123456, '11999995858'),
        ('Suave na nave', 789456789123123, '15999994747'),
        ('Saúde e bem estar', 456123789123789, '31999991212');

INSERT INTO productSupplier (idPSsupplier, idPSproduct, PSQuantity)
	values
		(1, 5, 500),
        (1, 6, 100),
        (2, 7, 1000),
        (3, 8, 5);
        
INSERT INTO seller (companyName, abstName, CNPJ, CPF, Contact, sellerLocation)
	values
		('Vendedora um máximo', 'Max', 451233789123789, 12123789, '11999997777', 'Rua A, 5, Letra - Letrario'),
		('Vendedora um gênio', 'Gen', 451233789124123, 12123123, '11999998888', 'Rua B, 8, Abecedario - Alfabeto');
        
INSERT INTO seller (companyName, abstName, CNPJ, CPF, Contact, sellerLocation)
	values
		('Maçã do amor', 'Love', 451231239124123, 12587123, '11999992222', 'Rua C, 56, Casa Branca - Washington');
        
INSERT INTO productSeller (idPseller, idPproduct, prodQuantity)
	values
		(1, 5, 500),
        (1, 6, 100),
        (2, 7, 1000),
        (2, 8, 5);
        
## -- QUANTOS PEDIDOS FORAM REALIZADOS POR CADA CLIENTE?
SELECT 
    CONCAT(c.Fname, ' ', c.Lname) AS clientes,
    COUNT(*) AS total_pedidos
FROM clients c
JOIN orders o ON c.idClient = o.idOrdersClient
GROUP BY c.idClient;

## -- RECUPERAÇÃO DE DADOS DE PEDIDO COM STATUS CONFIRMADO
SELECT * 
FROM orders o
WHERE o.ordersStatus = 'Confirmado';

## -- RELAÇÃO DE NOMES DOS FORNECEDORES E NOMES DOS PRODUTOS
SELECT s.companyName AS Fornecedor, p.Pname AS Produto
FROM supplier s, productSupplier sp
JOIN product p ON sp.idPSproduct = p.idProduct
ORDER BY s.companyName;

## -- RELAÇÃO DE NOMES DOS FORNECEDORES QUE VENDEM POLTRONA
SELECT s.companyName AS Fornecedor, p.Pname AS Produto
FROM supplier s, productSupplier sp
JOIN product p ON sp.idPSproduct = p.idProduct
HAVING p.Pname = 'Poltrona'
ORDER BY s.companyName;

## -- ALGUM VENDEDOR TAMBÉM É FORNECEDOR?
SELECT s.companyName AS vendedor, su.companyName AS fornecedor
FROM seller s
JOIN supplier su ON s.companyName = su.companyName;
