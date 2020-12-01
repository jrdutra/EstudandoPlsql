# Notas de aula do curso de PlSql

Curso ministrado pelo professor MSc Grimaldo Lopes de Oliveira na plataforma Udemy

[Link do curso](https://www.udemy.com/course/como-aprender-plsql-em-tempo-recorde-praticofacil)

## Ferramentas gratuitas

- Oracle 10g XE
- SQL Developer

## Passos para trabalhar com o banco de dados Oracle

1. Criar um usuário/esquema
2. Conceder privilégios de DBA
3. Analisar e Executar Scripts SQL
4. Visualizar o ambiente

## O Usuário

Quando o ucuário é criado, ele é apenas um usuário, mas quado você dá permissão a esse usuário para criar e manipular tabela, ele passa a ser um esquema de banco.

## Grant e Revoke

- O comando *Grant* permite dar privilégios ao usuário.
- O comando *Revoke* permite tirar os privilégios de um usuário.

#### Exemplos de comandos:

- Index = Permite a criação de index para as tabelas
- Insert = Permite a inclusão de linhas nas tabelas
- Update = Permite modificação nas linhas da tabela
- Delete = Permite que seja deletada linhas da tabela
- Select = Permite a consulta na tabela

Por exeplo:

```plsql
GRANT Index, Insert, Update, Delete, Select ON tabela1 to usuario1;
```

## Criando um usuário

O banco tem por padrão o usuário SYS e o usuário SYSTEM. Uninicialmente são esses usuários utilizados para a criação dos demais usuários.

**Comando para criar usuário:**

```plsql
CREATE USER aluno
identified by aluno
DEFAULT TABLESPACE Users
TEMPORARY TABLESPACE Temp;
```

**Comando para alterar a senha do usuário**

```plsql
ALTER USER aluno IDENTIFIED BY treinamento;
```

**Concedendo privilégios ao usuário**
(*Concedendo privilégio de DBA para o aluno*)
```plsql
GRANT DBA TO aluno;
```

## Trabalhando com o PLSQL

#### Bloco anônimo:

- DECLARE -- declaração de variáveis locais
- BEGIN -- Comandos PL/SQL -- Lógica
- EXCEPTION -- Tratamento de exceções
- END;

#### Exemplo de código:

```
SET SERVER ON
DECLARE

	vsalario number;
    vpercaumento number;
    vtotalsal number;
    
BEGIN

	vsalario := 2500.00;
    vpercaumento := 30/100;
    vtotalsal := vsalario + (vsalario*vpercaumento);
    DBMS_OUTPUT.PUT_line (' O novo salario é de: ' || vtotalsal);
    
END;
```

#### Tipos de dados:

1. Escalares
	- Numérico (Number)
    	- INTEGER
        - SMALLINT
        - DECIMAL
        - NUMERIC
        - REAL
        - DOUBLE PRECISION
        - Outros
    - Alfanuméricos (String)
    	- CHAR
    	- NCHAR
    	- VARCHAR
    	- NVARCHAR
    	- VARCHAR2
    	- ROWID
    	- UROWID
    	- Outros
    - Booleano
    	- BOOLEAN
    	- (OBS: Não é permitido a criação de atributos em banco de dados do tipo boolean. Só é aceito em variáveis e constantes no PL/SQL)
    - Data e Hora
    	- DATE
    	- TIMESTAMP
    	- Outros
2. Cursores
	- Cursores explicitos são espaços em memória onde você pode guardar o resultado de um consulta e iterar sobre esse resultado.

### Estruturas de Controle
1. Estruturas Condicionais
	- IF-THEN-ELSE
    - CASE-WHEN
2. Estruturas de Iteração
	- LOOP
    - WHILE-LOOP
    - FOR-LOOP
3. Estruturas de Sequência
	- GOTO
    - NULL

#### Exemplo de intregração da linguagem PL/SQL com o banco

```
SET SERVEROUTPUT ON
DECLARE
	vtotalquant number;
BEGIN
	SELECT sum(quantidade) INTO vtotalquant
    FROM TB_ITENS_PEDIDO;
    CASE
        WHEN vtotalquant <=200 THEN
            DBMS_OUTPUT.PUT_line ('O estoque está proximo do minimo' || vtotalquant);
        WHEN vtotalquant <=300 THEN
            DBMS_OUTPUT.PUT_line ('O estoque está completo' || vtotalquant);
        ELSE
            DBMS_OUTPUT.PUT_line ('O estoque está em excesso' || vtotalquant);
	END CASE;
END;
```

#### O comando nvl
Esse comando funciona como o ? do javascript. Ele testa se um campo é nulo, caso não nulo, ele retorna o campo, caso nulo ele retornar um valor default.

**Uso**
```
variavel := nvl(variavel_teste, variável_default);
```

## Cursores
1. Cursores Implicitos
	- Todo comando SQL
	- SQL%FOUND
	- SQL%NOTFOUND
	- SQL%ROWCOUNT
2. Cursores Explicitos
	- Necessários em consultas com +1 registro
    - OPEN..FETCH..CLOSE
    - FOR
    - FOR UPDATE
    - WHERE CURRENT

**Declaração dinâmica de variáveis com TYPE:**
```
DECLARE
	vid_autor TB_AUTOR.id_autor%TYPE;
```
No código acima, estamos declarando uma variável com o tipo que está especificado no banco, lembrando que estamos trabalhando com a tabela TB_AUTOR.

**Declaração dinâmica de variáveis com ROWTYPE:**
```
DECLARE
	vregautor TB_AUTOR%ROWTYPE;
```

Com o ROWTYPE, no lugar de eu pegar apenas uma variável da tabela, eu pego todas as variáveis da tabela e atribuo a um vetor, onde posteriormente ue posso usar da seguitne forma:

```
SELECT
	nome, sexo
INTO
	vregautor.nome,
    vregautor.sexo
```

**O cursos implícito NOTFOUND**
```
SET serveroutput ON
BEGIN

UPDATE
	TB_LIVRO
SET preco = preco * 1.05
WHERE
preco > 10;

IF (SQL%NOTFOUND) THEN
	DBMS_OUTPUT.PUT_LINE('Não Houve livro reajustado!!!');
ELSE
	DBMS_OUTPUT.PUT_LINE('A quantidade de livros reajustados foi de: ' || SQL%ROWCOUNT);
END IF;

END;
```

**Exemplo de código com curso explicito**

```
SET serveroutput ON

DECLARE

vreglivros TB_LIVRO%ROWTYPE;

CURSOR clivros IS
	SELECT
    	L.*
    FROM
    	TB_LIVRO L
    JOIN
    	TB_EDITORA E ON (L.id_editora = E.id_editora)
    WHERE
    	UPPER(E.descricao) = 'CAMPUS';

BEGIN
OPEN clivros;
LOOP
	FETCH clivros INTO vreglivros;
    EXIT WHEN clivros%NOTFOUND;
    DBMS_OUTPUT.PUT_LINE('Título e preço dos livros: ' || vreglivros.titulo || ' , ' ||vreglivros.preco);
END LOOP;
CLOSE clivros;
END;
    

```

O mesmo exemplo acima pode ser implementado com um FOR:
```
BEGIN
	FOR vreglivros IN clivros
    LOOP
    	DBMS_OUTPUT.PUT_LINE('Título e preço dos livros: ' || vreglivros.titulo || ' , ' ||vreglivros.preco);
    END LOOP;
END;
```

Existe uma outra forma de implementar esse tipo de cursor:

```
BEGIN
FOR vreglivros IN (
	SELECT
    	L.*
    FROM
    	TB_LIVRO L
    JOIN
    	TB_EDITORA E ON (L.id_editora = E.id_editora)
    WHERE
    	UPPER(E.descricao) = 'CAMPUS';
)
 LOOP
    	DBMS_OUTPUT.PUT_LINE('Título e preço dos livros: ' || vreglivros.titulo || ' , ' ||vreglivros.preco);
    END LOOP;
```