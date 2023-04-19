create database escola;
use escola;

create table professor(
codigo_professor int primary key not null auto_increment, /*Questão 3 Chaves Primarias*/
nome_professor varchar(50),
nome_disciplina varchar (50),
CPF varchar (14),
unique key (CPF) /*Questão 8 índice que não permite que o CPF se repita*/
);

create table aluno(
codigo_aluno int primary key not null auto_increment,
nome_aluno varchar(50),
CPF varchar (14) unique key
);

create table logies(
codlog int not null auto_increment primary key,
datacadastro date,
descricao varchar(255)
);

create table aula(
codigo_professor int,
codigo_aluno int,
media_bimestre double,

foreign key(codigo_aluno) references aluno(codigo_aluno), /*Questão 4 Chaves Estrangeiras*/
foreign key(codigo_professor) references professor(codigo_professor)
);


/* inserção no banco */

INSERT INTO `escola`.`aluno` (`nome_aluno`, `CPF`) VALUES ('aluno 1', '1111');
INSERT INTO `escola`.`aluno` (`nome_aluno`, `CPF`) VALUES ('aluno 2', '2222');
INSERT INTO `escola`.`aluno` (`nome_aluno`, `CPF`) VALUES ('aluno 3', '3333');
INSERT INTO `escola`.`aluno` (`nome_aluno`, `CPF`) VALUES ('aluno 4', '4444');

INSERT INTO `escola`.`professor` (`nome_professor`) VALUES ('professor 1');
INSERT INTO `escola`.`professor` (`nome_professor`) VALUES ('professor 2');
INSERT INTO `escola`.`professor` (`nome_professor`) VALUES ('professor 3');
INSERT INTO `escola`.`professor` (`nome_professor`) VALUES ('professor 4');

INSERT INTO `escola`.`aula` (`codigo_professor`, `codigo_aluno`, `media_bimestre`) VALUES ('1', '1', '7');
INSERT INTO `escola`.`aula` (`codigo_professor`, `codigo_aluno`, `media_bimestre`) VALUES ('1', '2', '7');



/* Questão 1 - Desenvolver um trecho de código que permita apagar um conjunto de registros e queretorneaoestado anterior a exclusão*/

start transaction;
	Delete from aula where codigo_aluno= 2;
	Delete from aluno where codigo_aluno = 2;
ROLLBACK;



/* Questão 2 - Simular um dos problemas clássicos relacionados ao Nível Isolamento /Concorrência; */

/* A transação 1 faz uma consulta e recupera o nome do aluno de id = 2, enquanto isso uma outra transação ( transação 1) faz uma alteração neste campo
e em um momento futuro a transação 1 acaba recuperando novamente o nome do aluno com id = 2, mas desta vez com um valor diferente ao da primeira vez.
 */

start transaction; --  <- transação 1
	select nome_aluno from aluno where codigo_aluno = 2;
    -- bla bla bla
    select nome_aluno from aluno where codigo_aluno = 2;
ROLLBACK;

start transaction; -- <- transação 2
	UPDATE aluno SET `nome_aluno` = 'aluno 5' WHERE (`codigo_aluno` = '2');
ROLLBACK;





/* Questão 5  - Como abordado nas diretrizes sobre indexação, deve-se justificar/criar ao menos umíndiceparacada uma das estruturas indicadas no quadro 01 */

/* Não é necessário a criação do indice na tabela professor (quadro 1), pois esta tabela faz uso de uma chave primária (codigo_professor) e de uma chave unica (CPF), por isso, o próprio
mysql cria os indices de forma automática, mas caso fosse necessário utilizariamos o comando abaixo: 
        CREATE INDEX 'nome_do_index' ON 'nome_da_tabela'('nome_da_coluna');
E para mostrar os indices da tabela utilizamos o comando abaixo: 
*/

SHOW INDEX FROM professor FROM escola;


/* Questão 6 - Indicar os índices Clustered*/
/* Na tabela professor temos o codigo_professor como indice clustered. */


/* Questão 7 - Indicar os índices Nonclustered*/
/* Na tabela professor temos o CPF como indice Nonclustered. */


/* Questão 8 - Criar um índice que não permita que o CPF possa se repetir*/
/* Foi utilizado o ' unique key ' no campo CPF que além de garantir a não repetição também se enquandra como um indice. */


/* Questão 9 - Apresentar o plano de execução para uma consulta que retorne o nome do Alunocomamaiormédia do bimestre*/
explain select nome_aluno from aula, aluno where media_bimestre = (select max(media_bimestre) from aula) and aula.codigo_aluno = aluno.codigo_aluno;

/* Questão 10 - . Após apresentar o plano de execução na questão 09, deve-se explicar o resultadodosseguintescampos: Type, select_type, possible_keys e key.*/
/* 
   Type -> Indica qual o tipo de busca foi utilizado para consulta, no caso temos o valor all que indica que uma leitura completa foi feita, e o valor eq_ref que 
indica que apenas uma linha da tabela aluno será lida para cada uma da tabela aula. 
   
   select_type -> Mostra qual o tipo de consulta, temos o valor Primary e o valor subQuery, o primeiro indica que é uma consulta a mais externa da consulta, e 
o subquery que é uma consulta aninhada
    
    possible_keys -> Indica quais os indices sao possiveis/disponiveis para utilização do comando, no caso temos null indicando que não há nenhum, codigo_aluno indicando
a chave primária.
	
    key -> Mostra qual indice está sendo usado para leitura, no aso está sendo usando a chave primaria (Primary)
    */

