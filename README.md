### 1. Cenario

Para esse banco de dados foi escolhido um ecossistema de uma papelaria que vende produtos no atacado voltados para a área industrial, o foco é organizar melhor o processo de estoque e venda, com o objetivo de ajudar a conferir os processos que são efetuados no dia a dia,
como a entrada de produtos, ou conferir o histórico de compra de um certo cliente, verificar se seus preços possuem desconto ou não, conferir se algum iten foi vendido por um preço a abaixo da tabela, e ser possível ver as informações de qual funcionário fez essa venda ou quais produtos são fornecidos por algum fornecedor cadastrado no sistema, entre outras pesquisas.

### 2.  Modelagem Conceitual DER
   
![Modelo_Conceitual-DER - Page 1](https://github.com/user-attachments/assets/44259232-fa1b-4af1-b360-0da6d56cb5cf)


O primeiro passo após decidir o cenário foi usar o LucidChart para fazer o modelo conceitual, onde define-se as tabelas principais e suas interações com as cardinalidades para no próximo passo transformá-las em novas tabelas.

### 3. Modelagem Lógica MER
   
 <img width="7563" height="4680" alt="Modelo-Logico-MER - Página 1" src="https://github.com/user-attachments/assets/1e83d60d-fdf2-4734-a1e7-82454b28cfbe" />

O segundo passo é transformar o DER no modelo lógico, que possuem as Primary Keys PK e Foreign Keys FK que são necessárias para dizer quais tabelas interagem entre si, e dessa maneira acaba criando mais tabelas como a de "entrada" que
nasce da relação entre a tabela "funcionario" e "produtos" por exemplo, além do tipo que cada atributo do DER terá na modelagem física (varchar, char, date, entre outros).

### 4. Modelagem Física

4.1 Será utilizado o Modelo Lógico para entender quais vão ser os valores e as estruturas para fazer os comandos que criaram as tabelas no banco de dados. Para criar as tabelas primeiro fazemos as principais, que irão fornecer suas PKs para as outras tabelas, então usa o CREATE schema para criar o ecossistema da papelaria, e depois o CREATE TABLE.

```sql
-- Criando o banco de dados

CREATE SCHEMA papelaria;

-- Criando As tabelas principais

-- Funcionários

  CREATE TABLE papelaria.funcionario (
    id_funcionario SERIAL PRIMARY KEY,
    nome_funcionario varchar(30) NOT NULL,
    salario DECIMAL(10,2) NOT NULL,
    cargo varchar(20) NOT NULL,
    cpf varchar(20) UNIQUE NOT NULL
  );

  -- Fornecedor
  
  CREATE TABLE papelaria.fornecedor (
    id_fornecedor SERIAL PRIMARY KEY,
    nome_fornecedor varchar(30) NOT NULL,
    tipo_documento_fornecedor char(4) NOT NULL,
    documento_fornecedor varchar(20) UNIQUE NOT NULL
  );

  -- Cliente
  
  CREATE TABLE papelaria.cliente (
    id_cliente SERIAL PRIMARY KEY,
    nome_cliente varchar(50) NOT NULL,
    tipo_documento_cliente char(4) NOT NULL,
    documento_cliente varchar(20) UNIQUE NOT NULL
  );

  -- Produto
  
  CREATE TABLE papelaria.produto (
    id_produto SERIAL PRIMARY KEY,
    nome varchar(100),
    descricao varchar(100),
    preco DECIMAL(10,2),
    custo DECIMAL(10,2),
    quantidade INT,
    ultima_data_entrada DATE,
    id_funcionario INT,
    id_fornecedor INT,
    FOREIGN KEY (id_funcionario) REFERENCES papelaria.funcionario(id_funcionario), 
    FOREIGN KEY (id_fornecedor) REFERENCES papelaria.fornecedor(id_fornecedor)
  );
```
Precisa se atendar na ordem das tabelas exatas, por exemplo, mesmo a tabela produto estando entre uma das principais ela ainda depende da fornecedor e funcionário porém nece cenário ainda terá outras tabelas q dependera dela também.

4.2 Após a criação das tabelas que irão fornecer as chaves para fazer a ligação entre elas, as próximas a serem criadas são justamente as que dependem dessas tabelas principais.

```sql

-- Criando as tabelas que dependes das principais

-- Funcionario
CREATE TABLE papelaria.login_usuario (
  id_login_usuario SERIAL PRIMARY KEY,
  usuario varchar(20) UNIQUE NOT NULL,
  senha varchar(40) NOT NULL,
  nivel_acesso varchar(15) NOT NULL,
  id_funcionario INT NOT NULL,
  FOREIGN KEY (id_funcionario) REFERENCES papelaria.funcionario(id_funcionario)
);

CREATE TABLE papelaria.email_funcionario (
  id_email_funcionario SERIAL PRIMARY KEY,
  email varchar(100) NOT NULL,
  id_funcionario INT,
  FOREIGN KEY (id_funcionario) REFERENCES papelaria.funcionario(id_funcionario)
);

CREATE TABLE papelaria.telefone_funcionario (
  id_telefone_funcionario SERIAL PRIMARY KEY,
  telefone varchar(15),
  id_funcionario INT,
  FOREIGN KEY (id_funcionario) REFERENCES papelaria.funcionario(id_funcionario)
);

-- Fornecedor
CREATE TABLE papelaria.email_fornecedor (
  id_email_fornecedor SERIAL PRIMARY KEY,
  email varchar(100) NOT NULL,
  id_fornecedor INT,
  FOREIGN KEY (id_fornecedor) REFERENCES papelaria.fornecedor(id_fornecedor)
);

CREATE TABLE papelaria.telefone_fornecedor (
  id_telefone_fornecedor SERIAL PRIMARY KEY,
  telefone varchar(15),
  id_fornecedor INT,
  FOREIGN KEY (id_fornecedor) REFERENCES papelaria.fornecedor(id_fornecedor)
);

-- Cliente
CREATE TABLE papelaria.email_cliente (
  id_email_cliente SERIAL PRIMARY KEY,
  email varchar(30),
  id_cliente INT,
  FOREIGN KEY (id_cliente) REFERENCES papelaria.cliente(id_cliente)
);

CREATE TABLE papelaria.telefone_cliente (
  id_telefone_cliente SERIAL PRIMARY KEY,
  telefone varchar(15),
  id_cliente INT,
  FOREIGN KEY (id_cliente) REFERENCES papelaria.cliente(id_cliente)
);

CREATE TABLE papelaria.endereco_cliente (
  id_enredeco_cliente SERIAL PRIMARY KEY,
  numero varchar(5),
  rua varchar(20),
  bairro varchar(20),
  cidade varchar(20),
  estado char(2),
  id_cliente INT,
  FOREIGN KEY (id_cliente) REFERENCES papelaria.cliente(id_cliente)
);

-- Entrada e Venda

CREATE TABLE papelaria.entrada (
  id_entrada SERIAL PRIMARY KEY,
  quantidade INT,
  data_entrada date,
  id_funcionario INT,
  id_produto INT,
  FOREIGN KEY (id_funcionario) REFERENCES papelaria.funcionario(id_funcionario),
  FOREIGN KEY (id_produto) REFERENCES papelaria.produto(id_produto)
);

CREATE TABLE papelaria.venda (
  id_venda SERIAL PRIMARY KEY,
  data DATE,
  valor_total_produto DECIMAL(10,2),
  id_cliente INT,
  id_funcionario INT,
  FOREIGN KEY (id_cliente) REFERENCES papelaria.cliente(id_cliente),
  FOREIGN KEY (id_funcionario) REFERENCES papelaria.funcionario(id_funcionario)
);

CREATE TABLE papelaria.item_venda (
  id_item_venda SERIAL PRIMARY KEY,
  quantidade INT,
  preco_unidade DECIMAL(10,2),
  valor_total_item DECIMAL(10,2),
  id_produto INT,
  id_venda INT,
  FOREIGN KEY (id_produto) REFERENCES papelaria.produto(id_produto),
  FOREIGN KEY (id_venda) REFERENCES papelaria.venda(id_venda)
);-- Criando as tabelas que dependes das principais

-- Funcionario
CREATE TABLE papelaria.login_usuario (
  id_login_usuario SERIAL PRIMARY KEY,
  usuario varchar(20) UNIQUE NOT NULL,
  senha varchar(40) NOT NULL,
  nivel_acesso varchar(15) NOT NULL,
  id_funcionario INT NOT NULL,
  FOREIGN KEY (id_funcionario) REFERENCES papelaria.funcionario(id_funcionario)
);

CREATE TABLE papelaria.email_funcionario (
  id_email_funcionario SERIAL PRIMARY KEY,
  email varchar(100) NOT NULL,
  id_funcionario INT,
  FOREIGN KEY (id_funcionario) REFERENCES papelaria.funcionario(id_funcionario)
);

CREATE TABLE papelaria.telefone_funcionario (
  id_telefone_funcionario SERIAL PRIMARY KEY,
  telefone varchar(15),
  id_funcionario INT,
  FOREIGN KEY (id_funcionario) REFERENCES papelaria.funcionario(id_funcionario)
);

-- Fornecedor
CREATE TABLE papelaria.email_fornecedor (
  id_email_fornecedor SERIAL PRIMARY KEY,
  email varchar(100) NOT NULL,
  id_fornecedor INT,
  FOREIGN KEY (id_fornecedor) REFERENCES papelaria.fornecedor(id_fornecedor)
);

CREATE TABLE papelaria.telefone_fornecedor (
  id_telefone_fornecedor SERIAL PRIMARY KEY,
  telefone varchar(15),
  id_fornecedor INT,
  FOREIGN KEY (id_fornecedor) REFERENCES papelaria.fornecedor(id_fornecedor)
);

-- Cliente
CREATE TABLE papelaria.email_cliente (
  id_email_cliente SERIAL PRIMARY KEY,
  email varchar(30),
  id_cliente INT,
  FOREIGN KEY (id_cliente) REFERENCES papelaria.cliente(id_cliente)
);

CREATE TABLE papelaria.telefone_cliente (
  id_telefone_cliente SERIAL PRIMARY KEY,
  telefone varchar(15),
  id_cliente INT,
  FOREIGN KEY (id_cliente) REFERENCES papelaria.cliente(id_cliente)
);

CREATE TABLE papelaria.endereco_cliente (
  id_enredeco_cliente SERIAL PRIMARY KEY,
  numero varchar(5),
  rua varchar(20),
  bairro varchar(20),
  cidade varchar(20),
  estado char(2),
  id_cliente INT,
  FOREIGN KEY (id_cliente) REFERENCES papelaria.cliente(id_cliente)
);

-- Entrada e Venda

CREATE TABLE papelaria.entrada (
  id_entrada SERIAL PRIMARY KEY,
  quantidade INT,
  data_entrada date,
  id_funcionario INT,
  id_produto INT,
  FOREIGN KEY (id_funcionario) REFERENCES papelaria.funcionario(id_funcionario),
  FOREIGN KEY (id_produto) REFERENCES papelaria.produto(id_produto)
);

CREATE TABLE papelaria.venda (
  id_venda SERIAL PRIMARY KEY,
  data DATE,
  valor_total_produto DECIMAL(10,2),
  id_cliente INT,
  id_funcionario INT,
  FOREIGN KEY (id_cliente) REFERENCES papelaria.cliente(id_cliente),
  FOREIGN KEY (id_funcionario) REFERENCES papelaria.funcionario(id_funcionario)
);

CREATE TABLE papelaria.item_venda (
  id_item_venda SERIAL PRIMARY KEY,
  quantidade INT,
  preco_unidade DECIMAL(10,2),
  valor_total_item DECIMAL(10,2),
  id_produto INT,
  id_venda INT,
  FOREIGN KEY (id_produto) REFERENCES papelaria.produto(id_produto),
  FOREIGN KEY (id_venda) REFERENCES papelaria.venda(id_venda)
);
```
4.3 Agora com todas as tabelas criadas corretamente, é executado os inserts para preencher as tabelas com os dados que devem ser armazenados.
Os inserts complestos estão nos arquivos ".txt" ja que são muitas informações.

4.3.1 Funcionários:

```sql
-- Um pouco dos inserts de cada tabela relacionada aos Funcionários

-- Funcionários
INSERT INTO papelaria.funcionario (nome_funcionario, salario, cargo, cpf) VALUES
('Bruno Alencar', 2740.00, 'Atendente', '36594281007'),
('Marina Santos', 3120.00, 'Caixa', '52918476005'),
('Carlos Eduardo', 2980.00, 'Atendente', '84731625090'),
('Fernanda Ribeiro', 3550.00, 'Gerente', '10492368744'),

-- Usuários
INSERT INTO papelaria.login_usuario (usuario, senha, nivel_acesso, id_funcionario) VALUES
('bruno.alencar', 'Gh2@pL9eQw1', 'padrão', 1),
('marina.santos', 'Zp8!Kd3sRt7', 'padrão', 2),
('carlos.eduardo', 'Lm4#Qa9vTy6', 'padrão', 3),
('fernanda.ribeiro', 'Wq7%Br2kUi5', 'admin', 4),

-- Email funcionário
INSERT INTO papelaria.email_funcionario (email, id_funcionario) VALUES
('bruno.alencar@papelariaindustrial.com', 1),
('marina.santos@papelariaindustrial.com', 2),
('carlos.eduardo@papelariaindustrial.com', 3),
('fernanda.ribeiro@papelariaindustrial.com', 4),

```
4.3.2 Fornecedore:

```sql
-- Um pouco dos inserts de cada tabela relacionada aos Fornecedores

-- Fornecedores
INSERT INTO papelaria.fornecedor (nome_fornecedor, tipo_documento_fornecedor, documento_fornecedor) VALUES
('Alfa Papel e Celulose', 'CNPJ', '12345678000190'),
('Beta Distribuidora', 'CNPJ', '23456789000181'),
('Gamma Office Supply', 'CNPJ', '34567890000172'),
('Delta Comercial', 'CNPJ', '45678901000163'),

-- Email fornecedor 
INSERT INTO papelaria.email_fornecedor (email, id_fornecedor) VALUES
('contato@alfapapelcelulose.com.br', 1),
('contato@betadistribuidora.com.br', 2),
('contato@gammaofficesupply.com.br', 3),
('contato@deltacomercial.com.br', 4),

-- Telefone fornecedor 
INSERT INTO papelaria.telefone_fornecedor (telefone, id_fornecedor) VALUES
('(11) 94512-3078', 1),
('(21) 98341-5520', 2),
('(31) 99784-6621', 3),
('(41) 98820-1197', 4),

```
4.3.3 Clientes:

```sql
-- Um pouco dos inserts de cada tabela relacionada aos Clientes

-- Clientes
INSERT INTO papelaria.cliente (nome_cliente, tipo_documento_cliente, documento_cliente) VALUES
('Carlos Henrique Silva', 'CPF', '12345678901'),
('Ana Paula Ribeiro', 'CPF', '23456789012'),
('Marcos Vinicius Souza', 'CPF', '34567890123'),
('Lúcia Helena Duarte', 'CPF', '45678901234'),

-- Email cliente 
INSERT INTO papelaria.email_cliente (email, id_cliente) VALUES
('carlos.h.silva@gmail.com', 1),
('ana.paula.ribeiro@gmail.com', 2),
('marcos.v.souza@gmail.com', 3),
('lucia.h.duarte@gmail.com', 4),

-- Telefone cliente
INSERT INTO papelaria.telefone_cliente (telefone, id_cliente) VALUES
('(11) 98765-1357', 1),
('(11) 98123-9876', 2),
('(11) 99012-3456', 3),
('(11) 97654-2109', 4),

-- Endereço cliente 
INSERT INTO papelaria.endereco_cliente (numero, rua, bairro, cidade, estado, id_cliente) VALUES
('101', 'Rua das Flores', 'Centro', 'São Paulo', 'SP', 1),
('202', 'Av. Brasil', 'Jardins', 'São Paulo', 'SP', 2),
('303', 'Rua Azul', 'Liberdade', 'São Paulo', 'SP', 3),
('404', 'Rua Verde', 'Moema', 'São Paulo', 'SP', 4),

```
4.3.4 Produtos e Vendas

```sql
-- Um pouco dos inserts de cada tabela relacionada aos Produtos e Vendas

-- Produtos
INSERT INTO papelaria.produto (nome, descricao, preco, custo, quantidade, ultima_data_entrada, id_funcionario, id_fornecedor) VALUES
('Bobina Kraft 80g', 'Bobina 90cm x 200m mod.', 180.00, 115.00, 50, '2025-11-20', 1, 1),
('Saco Plástico 60x80cm', 'Pacote c/ 1000 sacos PEBD.', 110.00, 65.00, 250, '2025-11-20', 5, 2),
('Caixa Papel A4 75g', 'Caixa c/ 10 pacotes de Sulfite.', 135.00, 80.00, 150, '2025-11-15', 1, 3),
('Fita Crepe Industrial', 'Fita Crepe 48mm, c/ 24 rolos.', 95.00, 55.00, 180, '2025-11-15', 5, 4),

-- Entrada
INSERT INTO papelaria.entrada (quantidade, data_entrada, id_funcionario, id_produto) VALUES
(50, '2025-11-20', 3, 1),
(250, '2025-11-20', 5, 2),
(150, '2025-11-15', 3, 3),
(180, '2025-11-15', 5, 4),

-- Venda
INSERT INTO papelaria.venda (data, valor_total_produto, id_cliente, id_funcionario) VALUES
('2025-12-01', 0.00, 1, 2),
('2025-12-01', 0.00, 2, 4),
('2025-12-02', 0.00, 3, 2),
('2025-12-02', 0.00, 4, 4),

-- Item venda
INSERT INTO papelaria.item_venda (quantidade, preco_unidade, valor_total_item, id_produto, id_venda) VALUES
(10, 175.00, 1750.00, 1, 1),
(50, 105.00, 5250.00, 2, 2),
(20, 130.00, 2600.00, 3, 3),
(5, 370.00, 1850.00, 5, 3),

```
Os dados da tabela 'venda' depende da soma dos produtos de 'item_venda' então é necessário fazer um UPDATE dps de adicionar os dados vazios, mas em um sistema normal a conta seria feita antes de armazenar as informações.

```sql
UPDATE papelaria.venda v
SET valor_total_produto = sub.total_venda
FROM (
    SELECT id_venda, SUM(valor_total_item) AS total_venda
    FROM papelaria.item_venda
    GROUP BY id_venda
) AS sub
WHERE v.id_venda = sub.id_venda;

```

### 5. Fazendo CRUD

5.1 Insert:

<img width="1919" height="1079" alt="1CRUD_INSERT" src="https://github.com/user-attachments/assets/43860d7f-1e9c-4074-b5d0-abc9271f7f56" />

5.2 Select:

<img width="1917" height="1078" alt="2CRUD_SELECT" src="https://github.com/user-attachments/assets/b52892dc-63db-4c8f-95df-dab58908cdc5" />

5.3 Update:

<img width="1917" height="1076" alt="3CRUD_UPDATE1" src="https://github.com/user-attachments/assets/8dad4c05-8055-4425-9c58-e500afadc0c9" />
<img width="1917" height="1074" alt="4CRUD_UPDATE2_SELECT" src="https://github.com/user-attachments/assets/87f48bd4-b21a-46d0-b8c8-44ffbbb30e34" />

5.4 Delete:

<img width="1917" height="1077" alt="5CRUD_DELETE1" src="https://github.com/user-attachments/assets/d28f3e73-a393-4385-84a2-ca637db5c724" />
<img width="1917" height="1077" alt="6CRUD_DELETE2_SELECT" src="https://github.com/user-attachments/assets/dbc7a833-6247-44e4-845e-f474bddf8eda" />

### 6. Relatório

Agora são feito os testes com consultas para testar se as tabelas estão retornando corretamente seus dados e se suas interações entre si estão coerentes com os modelos DER e MER

6.1: Puxamos o histórico completo de um cliente específico para ver quais produtos, quantidades e valores ele comprou, organizados cronologicamente.

```sql
SELECT
    p.nome AS produto,                          
    iv.quantidade AS quantidade_vendida,         
    iv.preco_unidade AS preco_unitario_venda,     
    iv.valor_total_item AS valor_total_item,    
    c.nome_cliente AS empresa_compradora,        
    v.data AS DataVenda                         
FROM
    papelaria.item_venda iv                     
JOIN
    papelaria.produto p ON iv.id_produto = p.id_produto
JOIN
    papelaria.venda v ON iv.id_venda = v.id_venda       
JOIN
    papelaria.cliente c ON v.id_cliente = c.id_cliente 
  WHERE
    c.nome_cliente = 'Aline Araújo Torres'
ORDER BY
    c.nome_cliente, v.data DESC;
```

6.2: Verificamos quais produtos de um fornecedor específico estão com estoque abaixo do limite de 100 unidades, para não deixar o estoque zerar e faltar produto.

```sql
SELECT
    f.nome_fornecedor AS fornecedor,
    p.nome AS produto,
    p.quantidade AS estoque_atual,
    p.ultima_data_entrada
FROM
    papelaria.produto p
JOIN
    papelaria.fornecedor f ON p.id_fornecedor = f.id_fornecedor 
WHERE
    f.nome_fornecedor = 'Alfa Papel e Celulose'
    AND p.quantidade < 100 
ORDER BY
    p.quantidade ASC;
```

6.3: Essa consulta confere quais produtos e quantidades um funcionário específico registrou como entrada no estoque ao longo do ano. Facilitando o controle do estoque.

```sql
SELECT
    e.data_entrada AS data_entrada,
    p.nome AS Produto,
    e.quantidade AS qtd_recebida,
    f.nome_funcionario as funcionario
FROM
    papelaria.entrada e
JOIN
    papelaria.produto p ON e.id_produto = p.id_produto 
JOIN
    papelaria.funcionario f ON e.id_funcionario = f.id_funcionario 
WHERE
    f.nome_funcionario = 'Carlos Eduardo'
    AND e.data_entrada BETWEEN '2025-01-01' AND '2025-12-31' 
ORDER BY
    e.data_entrada ASC;
```

6.4: Essa consulta nos ajuda a achar produtos com custo baixo e calculando a margem de lucro de cada um, para verificar se será necessário alguma mudança de valores.

```sql
SELECT
    p.nome AS produto,
    f.nome_fornecedor AS fornecedor,
    p.custo AS custo,
    p.preco AS preco_venda,
    (p.preco - p.custo) AS margem_lucro 
FROM
    papelaria.produto p
JOIN
    papelaria.fornecedor f ON p.id_fornecedor = f.id_fornecedor 
WHERE
    p.custo < 50.00 
ORDER BY
    margem_lucro DESC;
```

6.5: Puxamos o extrato de todas as vendas que o funcionário fechou no mês de Dezembro por exemplo, vendo quem comprou e qual foi o valor total de cada negócio, auxiliando caso haja alguma meta a cumprir.

```sql
SELECT
    v.data AS data_venda,
    v.valor_total_produto AS valor_total,
    c.nome_cliente AS cliente,
    f.nome_funcionario AS vendedor
FROM
    papelaria.venda v
JOIN
    papelaria.funcionario f ON v.id_funcionario = f.id_funcionario
JOIN
    papelaria.cliente c ON v.id_cliente = c.id_cliente 
WHERE
    f.nome_funcionario = 'Marina Santos'
    AND v.data BETWEEN '2025-12-01' AND '2025-12-31' 
ORDER BY
    v.valor_total_produto DESC;
```

6.6: Puxamos o catálogo completo de produtos vindos de fornecedores, junto com o CNPJ, para sabermos exatamente o que compramos deles e facilitar pedidos futuros para repor o estoque.

```sql
SELECT
    p.nome AS produto,
    f.nome_fornecedor AS fornecedor,
    f.documento_fornecedor AS cnpj
FROM
    papelaria.produto p
JOIN
    papelaria.fornecedor f ON p.id_fornecedor = f.id_fornecedor
WHERE
    f.nome_fornecedor = 'Sigma Express'
ORDER BY
    p.nome ASC;
```

6.7: Consultamos os produtos com preço acima de R$ 300,00 que entraram no estoque antes de 30 de Novembro como exemplo. O foco dessa pesquisa é ajudar a identificar itens caros que estão parados há mais tempo no inventário.

```sql
SELECT
    p.nome AS produto,
    p.preco,
    p.ultima_data_entrada,
    f.nome_fornecedor AS fornecedor
FROM
    papelaria.produto p
JOIN
    papelaria.fornecedor f ON p.id_fornecedor = f.id_fornecedor
WHERE
    p.preco > 300.00
    AND p.ultima_data_entrada < '2025-11-30' 
ORDER BY
    p.ultima_data_entrada ASC;
```

6.8: Usamos essa consulta para localizar itens vendidos a um preço abaixo do valor de tabela mas ainda acima do custo, para ajudar a catalogar alguma promoção que está ocorrendo ou algum prerço especial de um cliente.

```sql
SELECT
    p.nome AS produto,
    p.preco AS preco_tabela,
    iv.preco_unidade AS preco_venda,
    (p.preco - iv.preco_unidade) AS diferenca,
    c.nome_cliente AS cliente
FROM
    papelaria.item_venda iv
JOIN
    papelaria.produto p ON iv.id_produto = p.id_produto
JOIN
    papelaria.venda v ON iv.id_venda = v.id_venda
JOIN
    papelaria.cliente c ON v.id_cliente = c.id_cliente 
WHERE
    iv.preco_unidade < p.preco 
ORDER BY
    Diferenca DESC
LIMIT 5; 
```

6.9: Esta consulta crítica encontra todas as transações onde o item foi vendido por um preço abaixo do custo. Calculamos o prejuízo por unidade e identificamos o vendedor responsável. O ideal é essa consulta retornar "nenhum dado", é considerado como uma pesquisa de segurança.

```sql
SELECT
    p.nome AS produto,
    p.custo AS custo_unitario,
    iv.preco_unidade AS preco_vendido,
    (p.custo - iv.preco_unidade) AS prejuizo_por_unidade,
    f.nome_funcionario AS vendedor_responsavel,
    v.data AS DataVenda
FROM
    papelaria.item_venda iv
JOIN
    papelaria.produto p ON iv.id_produto = p.id_produto       
JOIN
    papelaria.venda v ON iv.id_venda = v.id_venda              
JOIN
    papelaria.funcionario f ON v.id_funcionario = f.id_funcionario 
WHERE
    iv.preco_unidade< p.custo
ORDER BY
    prejuizo_por_unidade DESC, p.nome ASC; 
```

6.10: astreia os produtos que entraram recentemente no estoque e já foram vendidos. Isso mostra a velocidade da demanda e para quem o item está indo.

```sql
SELECT
    p.nome AS produto,
    p.ultima_data_entrada AS data_entrada_estoque,
    v.data AS DataVenda,
    c.nome_cliente AS cliente_comprador
FROM
    papelaria.produto p
JOIN
    papelaria.item_venda iv ON p.id_produto = iv.id_produto 
JOIN
    papelaria.venda v ON iv.id_venda = v.id_venda           
JOIN
    papelaria.cliente c ON v.id_cliente = c.id_cliente     
WHERE
    p.ultima_data_entrada > '2025-11-20' 
ORDER BY
    p.ultima_data_entrada DESC, v.data ASC;
```

### Conclusão

Este sistema foi pensado para uso no dia a dia, usando de base uma papelaria e focando nas necessidades reais de armazenamento e pesquisa de dados. Foi definido o que guardar usando a Modelagem inicial, validando a integridade das tabelas e suas relações, garantindo que tudo se conecta. Além disso, os testes CRUD e as consultas complexas de foram usadas para testar na prática o funcionamento do banco e garantir que entrege os relatórios essenciais das pesquisas, simplificando de vez o processo de venda, rastreio de estoque e controle de prejuízo.
