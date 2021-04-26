# Exemplo PostgreSQL

### Índice

1. [Sobre](#sobre)
2. [Criando o banco de dados](#l1)
3. [Criando tabela de usuários](#l2)
4. [Inserindo dados na tabela usuário](#l3)
5. [Criando uma view](#l4)
6. [SELECTs de exemplo](#l5)
7. [Criando a tabela contas](#l6)
8. [Criando view saldo usuário](#l7)
9. [Criando funções](#l8)

   


### Sobre

Este é um exemplo de um projeto de banco de dados [PostgreSQL](https://www.postgresql.org), neste projeto é exemplificado algumas das ações mais comuns ao se usar um banco de dados Postgres, ações como **criar tabelas, inserir dados em tabelas, criar e utilizar views, assim como criar e utilizar funções plpgsql**.



##### É necessário ter o PostgreSQL instalado em seu computador, [neste link](https://www.postgresql.org/download/) você pode encontrar o passo a passo para instala-lo em sua maquina.

<div id='l1'/>

### 1. Criando o banco de dados (e schema)

Cria o banco de dados de nome DataBaseDeTeste e atribui ao usuário postgres. Depois cria um novo schema de nome empresadb, que irá conter nossas tabelas.

```
CREATE DATABASE "DataBaseDeTeste"
    WITH 
    OWNER = postgres
    ENCODING = 'UTF8'
    CONNECTION LIMIT = -1;
```

```
CREATE SCHEMA "empresadb"
    AUTHORIZATION postgres;
```



<div id='l2'/>

###  2. Criando tabela de usuários

Cria a primeira tabela, que irá conter os dados sobre o usuário. Para usar como exemplo esta tabela terá as colunas: **id, username,rg,cpf,nascimento e sexo**. Onde apenas a coluna "sexo" não é obrigatória, e as colunas rg e cpf precisam ser únicas, ou seja, o banco não irá aceitar um INSERT INTO onde o rg ou cpf já constem no banco de dados, prevenindo o cadastro de dois usuários com o mesmo rg/cpf.

```
CREATE TABLE empresadb."users"
(
    id serial NOT NULL,
    username character varying(100) COLLATE pg_catalog."default" NOT NULL,
    rg character varying(30) COLLATE pg_catalog."default" NOT NULL UNIQUE,
    cpf character varying(30) COLLATE pg_catalog."default" NOT NULL UNIQUE,
    nascimento date NOT NULL,
    sexo character varying(30) COLLATE pg_catalog."default",
    CONSTRAINT "User_pkey" PRIMARY KEY (id)
);
```



<div id='l3'/>

### 3. Inserindo dados na tabela usuário

Agora inserimos alguns cadastros de usuário na tabela, repare que a coluna users.id é omitida, já que é um campo SERIAL e não é obrigatório a passagem de um valor para ela.

```
INSERT INTO empresadb.users (username, rg, cpf , nascimento , sexo)
VALUES ('Helder','23.345.785-36','238.245.456-24','2001-10-11','Masculino');
INSERT INTO empresadb.users (username, rg, cpf , nascimento , sexo)
VALUES ('Cintia','41.212.335-65','548.224.416-53','2013-01-14','Feminino');
INSERT INTO empresadb.users (username, rg, cpf , nascimento , sexo)
VALUES ('Roger','1.562.531-32','218.264.156-94','1993-03-23','Masculino');
INSERT INTO empresadb.users (username, rg, cpf , nascimento , sexo)
VALUES ('Marcela','25.289.689-02','168.284.396-29','1997-11-10','Feminino');
INSERT INTO empresadb.users (username, rg, cpf , nascimento , sexo)
VALUES ('Britanny','11.222.345-66','568.234.456-54','1953-11-10',null );
INSERT INTO empresadb.users (username, rg, cpf , nascimento , sexo)
VALUES ('Soraia','19.074.158-44','333.333.333-22','1983-11-10','Masculino');
INSERT INTO empresadb.users (username, rg, cpf , nascimento , sexo)
VALUES ('Denis','31.777.545-99','568.234.777-54','1995-03-10','Masculino');
INSERT INTO empresadb.users (username, rg, cpf , nascimento , sexo)
VALUES ('Carlos','98.224.335-46','668.111.222-54','2000-04-10','Masculino');
INSERT INTO empresadb.users (username, rg, cpf , nascimento , sexo)
VALUES ('Jonas','78.789.744-88','877.866.666-52','1999-09-10','Masculino');
INSERT INTO empresadb.users (username, rg, cpf , nascimento , sexo)
VALUES ('Jon','77.777.747-87','877.867.677-57','2004-09-10','Masculino');


```



<div id='l4'/>

### 4. Criando uma view

Aqui um exemplo da criação de uma view simples, que faz um SELECT do banco de dados que acabamos de criar, retornando o id, o username e o nascimento registrados na tabela users.
As views são uma ótima ferramenta de segurança, impedindo que a pessoa que executa a query acesse os dados diretamente do banco de dados, servindo como uma intercessora entre a requisição e o banco. 

```
CREATE OR REPLACE VIEW empresadb."idadeusers"
 AS
SELECT id,username,nascimento FROM empresadb.users order by nascimento desc;

ALTER TABLE empresadb."idadeusers"
    OWNER TO postgres;
```



<div id='l5'/>

### 5. SELECTs de exemplo

Agora podemos fazer alguns SELECTs nessa tabela, para observarmos os resultados.

```
--Ordenando usuários por idade
--Mais velhos primeiro.
SELECT id,username,nascimento FROM empresadb.users order by nascimento;
--Mais novos primeiro.
SELECT id,username,nascimento FROM empresadb.users order by nascimento desc;
--Seleciona usuários nascidos de 1993 a 2000.
SELECT id,username,nascimento from empresadb.users where
nascimento between '1993-01-01' and '2000-12-30' order by id;
--Seleciona usuários nascidos antes de 2001.
SELECT id,username,nascimento from empresadb.users where
nascimento <= '2000-12-30' order by id;
--Selecioona usuários nascidos depois de 2001.
SELECT id,username,nascimento from empresadb.users where
nascimento >= '2002-01-01' order by id;

--Realiza a seleção de usuários, do mais novo para o mais velho,
--através do uso de uma View.
SELECT * FROM empresadb.idadeusers;
```



## Tabela contas

<div id='l6'/>

### 6. Criando a tabela contas

Agora vamos criar uma segunda tabela e inserir alguns cadastros nela, para que possamos realizar um JOIN.



	CREATE TABLE empresadb."contas"
	(
	    id serial NOT NULL,
	    userdoc character varying(100) COLLATE pg_catalog."default" NOT NULL,
	    numconta character varying(30) COLLATE pg_catalog."default" NOT NULL UNIQUE,
	    saldo character varying(30) COLLATE pg_catalog."default" NOT NULL UNIQUE,
	    criacao date NOT NULL,
	    CONSTRAINT "conta_pkey" PRIMARY KEY (id),
		CONSTRAINT "user_fk" foreign key (userdoc) references empresadb.users (cpf)
	);
	
	insert into empresadb.contas (userdoc,numconta,saldo,criacao)
	values ('238.245.456-24','1111-5555-8888',20.50, current_date );
	insert into empresadb.contas (userdoc,numconta,saldo,criacao)
	values ('548.224.416-53','5555-9999-8888',230.43, current_date);
	insert into empresadb.contas (userdoc,numconta,saldo,criacao)
	values ('218.264.156-94','1111-5545-8888',578.60, current_date);
	insert into empresadb.contas (userdoc,numconta,saldo,criacao)
	values ('168.284.396-29','5456-3452-6784',20880.00, current_date);
	insert into empresadb.contas (userdoc,numconta,saldo,criacao)
	values ('568.234.456-54','2345-7573-3782',1240.00, current_date);
	insert into empresadb.contas (userdoc,numconta,saldo,criacao)
	values ('333.333.333-22','5327-5784-2874',5670.65, current_date);
	insert into empresadb.contas (userdoc,numconta,saldo,criacao)
	values ('568.234.777-54','2467-5555-7352',1000.00, current_date);
	insert into empresadb.contas (userdoc,numconta,saldo,criacao)
	values ('668.111.222-54','1463-6261-7824',430.20, current_date);
	insert into empresadb.contas (userdoc,numconta,saldo,criacao)
	values ('877.866.666-52','5163-4131-1335',432.54, current_date);
	insert into empresadb.contas (userdoc,numconta,saldo,criacao)
	values ('877.867.677-57','1566-1255-4135',6776.32, current_date);


​	


Execute o comando abaixo para checar o resultado:

```
select * from empresadb.contas;
```

<div id='l7'/>

### 7. Criando view saldo usuário

Cria uma view que através de um INNER JOIN ou somente JOIN, retorna os dados das tabelas **users** e **contas**, especificamente seu users.id, users.username,users.cpf,contas.numconta e contas.saldo.
Os resultados são organizados com base no saldo da conta, do maior valor para o menor graças a opção **desc** de decrescente. Repare que é usado o alias **u** para se referia a tabela **users**, e **c** para se referir a tabela **contas**.

```
--Cria view saldousers.

CREATE OR REPLACE view empresadb."saldousers"
AS
SELECT u.id,u.username,u.cpf,c.numconta,c.saldo from empresadb.users u LEFT JOIN
empresadb.contas c on c.userdoc =  u.cpf order by c.saldo desc;

```

Testa view criada:

```
select * from empresadb.saldousers;
```

<div id='l8'/>

### 8. Criando funções

Agora vamos para uma parte um pouco mais avançada, vamos criar funções, é importante lembrar que o postgresql aceita funções feitas em várias linguagens, como Java, Python, Javascript e etc. Mas neste exemplo iremos utilizar a linguagem PLPGSQL.  Nesta função, iremos verificar qual o usuário/cliente com o maior saldo em sua conta, e daremos um retorno no formato TEXT, que será composto pela String **"O cliente com o maior saldo é:: "**+ o nome do usuário, repare também na variável **nameuser** do tipo TEXT que armazena o resultado de um SELECT que retorna o nome do usuário que, com base na view saldousers, tem o maior saldo registrado em sua conta. Ainda especificamos a linguagem usada na função, neste caso **plpgsql**, e com **security definer**, dizemos que esta função quando executada (por qualquer usuário), terá as permissões e credenciais do usuário que a criou, diferente de **security invoker** que atribui a função, as permissões e credenciais de quem a invoca/executa.

**Repare que nesta função é usada a view saldousers que acabamos de criar.**

```
--Cria uma função que informa qual o cliente com maior saldo.
create or replace function empresadb.usermaisrico() returns text
as $$
declare nameuser text;
	begin
		select username from empresadb.saldousers 
		where saldo = (select max(saldo) from empresadb.saldousers) into nameuser;
		return 'O cliente com maior saldo é:: '|| nameuser;
	end;
$$ language plpgsql
security definer;
```

Confere resultado da função:

```
select empresadb.usermaisrico();
```