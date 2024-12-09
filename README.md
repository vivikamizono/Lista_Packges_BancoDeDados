# **Lista de Exercícios: Packages**
<p align="center">
  <img src="https://img.shields.io/badge/Oracle-F80000?style=for-the-badge&logo=oracle&logoColor=white" />
  <img src="https://img.shields.io/badge/Database-000000?style=for-the-badge&logo=database&logoColor=white" />
</p>


### Objetivo
Desenvolver um conjunto de pacotes em PL/SQL que implementem operações relacionadas às entidades Aluno, Disciplina e Professor, utilizando o Oracle como banco de dados. O trabalho busca consolidar os conhecimentos de criação e uso de procedures, functions e cursores, com ênfase em operações que envolvam parâmetros, cálculos e manipulação de dados.

---

## **Como executar os scripts**

1. **Abra seu cliente Oracle**:  
   Use o Oracle SQL Developer, caso nao esteja instalado, será nescessário a instalação.

2. **Conecte-se ao banco de dados**:  
   Certifique-se de que você tem acesso ao banco de dados e insira suas credenciais (usuário, senha).

3. **Verifique as tabelas**:  
   Certifique-se de que as tabelas necessárias estão criadas no banco de dados. Um exemplo básico:

   ```sql
   CREATE TABLE ALUNOS (
       ID_ALUNO NUMBER PRIMARY KEY,
       NOME VARCHAR2(100),
       DATA_NASCIMENTO DATE
   );

   CREATE TABLE DISCIPLINAS (
       ID_DISCIPLINA NUMBER PRIMARY KEY,
       NOME VARCHAR2(100),
       DESCRICAO VARCHAR2(255),
       CARGA_HORARIA NUMBER
   );

   CREATE TABLE PROFESSORES (
       ID_PROFESSOR NUMBER PRIMARY KEY,
       NOME VARCHAR2(100)
   );

   CREATE TABLE TURMAS (
       ID_TURMA NUMBER PRIMARY KEY,
       ID_PROFESSOR NUMBER REFERENCES PROFESSORES(ID_PROFESSOR),
       ID_DISCIPLINA NUMBER REFERENCES DISCIPLINAS(ID_DISCIPLINA)
   );

   CREATE TABLE MATRICULAS (
       ID_MATRICULA NUMBER PRIMARY KEY,
       ID_ALUNO NUMBER REFERENCES ALUNOS(ID_ALUNO),
       ID_DISCIPLINA NUMBER REFERENCES DISCIPLINAS(ID_DISCIPLINA)
   );



Carregue e execute os scripts:

Cole os pacotes no editor SQL e execute-os na sequência:
Defina o PACKAGE (estrutura do pacote).
Defina o PACKAGE BODY (implementação do pacote).
Teste as funcionalidades:
Use os exemplos fornecidos abaixo para validar os comandos.

## **Resumo dos pacotes**
Pacote PKG_ALUNO
Excluir Aluno (EXCLUIR_ALUNO):
Recebe o ID de um aluno como parâmetro, exclui o registro do aluno e todas as matrículas associadas a ele.

Exemplo de uso:

   ```sql
BEGIN
    PKG_ALUNO.EXCLUIR_ALUNO(1); -- Substitua "1" pelo ID do aluno a ser excluído.
END;
   ```
Listar Alunos Maiores de 18 Anos (CURSOR_ALUNOS_MAIORES):
Lista o nome e a data de nascimento dos alunos com mais de 18 anos.

  ```sql
DECLARE
    V_NOME ALUNOS.NOME%TYPE;
    V_DATA_NASCIMENTO ALUNOS.DATA_NASCIMENTO%TYPE;
BEGIN
    FOR R IN PKG_ALUNO.CURSOR_ALUNOS_MAIORES LOOP
        DBMS_OUTPUT.PUT_LINE('Nome: ' || R.NOME || ', Data de Nascimento: ' || R.DATA_NASCIMENTO);
    END LOOP;
END;
   ```

Listar Alunos por Curso (CURSOR_ALUNOS_POR_CURSO):
Lista os nomes dos alunos matriculados em um curso específico.

   ```sql
DECLARE
    V_NOME ALUNOS.NOME%TYPE;
BEGIN
    FOR R IN PKG_ALUNO.CURSOR_ALUNOS_POR_CURSO(101) LOOP
        DBMS_OUTPUT.PUT_LINE('Nome: ' || R.NOME);
    END LOOP;
END;
   ```

Pacote PKG_DISCIPLINA
Cadastrar Disciplina (CADASTRAR_DISCIPLINA):
Insere uma nova disciplina na tabela, recebendo nome, descrição e carga horária como parâmetros.

   ```sql
BEGIN
    PKG_DISCIPLINA.CADASTRAR_DISCIPLINA(
        P_NOME => 'Matemática',
        P_DESCRICAO => 'Álgebra Básica',
        P_CARGA_HORARIA => 60
    );
END;
   ```

Disciplinas com Mais de 10 Alunos (CURSOR_TOTAL_ALUNOS_DISCIPLINA):
Lista disciplinas que possuem mais de 10 alunos matriculados, com o total de alunos.

Média de Idade por Disciplina (CURSOR_MEDIA_IDADE):
Calcula a média de idade dos alunos matriculados em uma disciplina específica.

Listar Alunos de uma Disciplina (LISTAR_ALUNOS_DISCIPLINA):
Lista os alunos matriculados em uma disciplina específica.

   ```sql
BEGIN
    PKG_DISCIPLINA.LISTAR_ALUNOS_DISCIPLINA(201); -- Substitua "201" pelo ID da disciplina.
END;
   ```

Pacote PKG_PROFESSOR
Total de Turmas por Professor (CURSOR_TURMAS_PROFESSOR):
Lista os professores e o total de turmas que cada um leciona, filtrando apenas aqueles responsáveis por mais de uma turma.

   ```sql
DECLARE
    CURSOR_PROFESSORES PKG_PROFESSOR.CURSOR_TURMAS_PROFESSOR;
    V_NOME_PROFESSOR PROFESSORES.NOME%TYPE;
    V_TOTAL_TURMAS NUMBER;
BEGIN
    OPEN CURSOR_PROFESSORES;
    LOOP
        FETCH CURSOR_PROFESSORES INTO V_NOME_PROFESSOR, V_TOTAL_TURMAS;
        EXIT WHEN CURSOR_PROFESSORES%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE('Professor: ' || V_NOME_PROFESSOR || ' - Total de Turmas: ' || V_TOTAL_TURMAS);
    END LOOP;
    CLOSE CURSOR_PROFESSORES;
END;
   ```

Número de Turmas de um Professor (TOTAL_TURMAS_PROFESSOR):
Retorna o número total de turmas de um professor específico.

   ```sql
DECLARE
    V_TOTAL_TURMAS NUMBER;
BEGIN
    V_TOTAL_TURMAS := PKG_PROFESSOR.TOTAL_TURMAS_PROFESSOR(101); -- Substitua "101" pelo ID do professor.
    DBMS_OUTPUT.PUT_LINE('Total de Turmas: ' || V_TOTAL_TURMAS);
END;
   ```
Professor de uma Disciplina (PROFESSOR_DISCIPLINA):
Retorna o nome do professor que ministra uma disciplina específica.

   ```sql
DECLARE
    V_NOME_PROFESSOR VARCHAR2(100);
BEGIN
    V_NOME_PROFESSOR := PKG_PROFESSOR.PROFESSOR_DISCIPLINA(301); -- Substitua "301" pelo ID da disciplina.
    DBMS_OUTPUT.PUT_LINE('Professor: ' || V_NOME_PROFESSOR);
END;
   ```
