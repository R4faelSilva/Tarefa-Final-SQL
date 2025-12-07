1. Cenario

     Para esse banco de dados foi escolhido um ecossistema de uma papelaria que vende produtos no atacado voltados para a área industrial, o foco é organizar melhor o processo de estoque e venda, com o objetivo de ajudar a conferir os processos que são efetuados no dia a dia,
   como a entrada de produtos, ou conferir o histórico de compra de um certo cliente, verificar se seus preços possuem desconto ou não, conferir se algum iten foi vendido por um preço a abaixo da tabela, e ser possível ver as informações de qual funcionário fez essa venda ou
   quais produtos são fornecidos por algum fornecedor cadastrado no sistema, entre outras pesquisas.

2.  Modelagem Conceitual DER
   
   ![Modelo_Conceitual-DER - Page 1](https://github.com/user-attachments/assets/ab13c323-8b22-41cb-89a0-08ab538d8977)

O primeiro passo após decidir o cenário foi usar o LucidChart para fazer o modelo conceitual, onde define-se as tabelas principais e suas interações com as cardinalidades para no próximo passo transformá-las em novas tabelas.

3. Modelagem Lógica MER
   
   <img width="7563" height="4680" alt="Modelo-Logico-MER - Página 1" src="https://github.com/user-attachments/assets/1e83d60d-fdf2-4734-a1e7-82454b28cfbe" />

     O segundo passo é transformar o DER no modelo lógico, que possuem as Primary Keys PK e Foreign Keys FK que são necessárias para dizer quais tabelas interagem entre si, e dessa maneira acaba criando mais tabelas como a de "entrada" que
   nasce da relação entre a tabela "funcionario" e "produtos" por exemplo, além do tipo que cada atributo do DER terá na modelagem física (varchar, char, date, entre outros).

5. Modelagem Física

     Será utilizado o Modelo Lógico para entender quais vão ser os valores e as estruturas para fazer os comandos que criaram as tabelas no banco de dados.

     Para criar as tabelas primeiro fazemos as principais, que irão fornecer suas PKs para as outras tabelas, então usa o CREATE schema para criar o ecossistema da papelaria, e depois o CREATE TABLE.

   .Criando o banco de dados

  CREATE SCHEMA papelaria;
  
  .Criando As tabelas principais

  .Funcionários
  '''
  CREATE TABLE papelaria.funcionario (
    id_funcionario SERIAL PRIMARY KEY,
    nome_funcionario varchar(30) NOT NULL,
    salario DECIMAL(10,2) NOT NULL,
    cargo varchar(20) NOT NULL,
    cpf varchar(20) UNIQUE NOT NULL
  );

  .Fornecedor
  
  CREATE TABLE papelaria.fornecedor (
    id_fornecedor SERIAL PRIMARY KEY,
    nome_fornecedor varchar(30) NOT NULL,
    tipo_documento_fornecedor char(4) NOT NULL,
    documento_fornecedor varchar(20) UNIQUE NOT NULL
  );

  .Cliente
  
  CREATE TABLE papelaria.cliente (
    id_cliente SERIAL PRIMARY KEY,
    nome_cliente varchar(50) NOT NULL,
    tipo_documento_cliente char(4) NOT NULL,
    documento_cliente varchar(20) UNIQUE NOT NULL
  );

  .Produto
  
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
'''



