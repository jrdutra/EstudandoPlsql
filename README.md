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
