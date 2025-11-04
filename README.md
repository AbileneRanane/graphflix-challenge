# graphflix-challenge
## Projeto de desafio sobre Grafos de Conhecimento em um serviço de streaming, explorando conexões entre usuários, filmes, diretores e o ator/produtor Adam Sandler.






<img width="1920" height="1080" alt="Grafo de Conhecimento – Serviço de Streaming" src="https://github.com/user-attachments/assets/e175e6ac-254d-43dc-9116-ca2da7a33cc5" />









# SCRIPT CYPHER (.cypher) que cria CONTRAINTS para os nós ( ex: unique para IDS):








  ###  // -------------------------------------------------------
###  // Grafo de Conhecimento – Serviço de Streaming
###  // Com Adam Sandler (ator/produtor) conectado a filmes, diretores e usuários
 ### // -------------------------------------------------------

### // 1) Constraints
### CREATE CONSTRAINT user_id_unique IF NOT EXISTS
###  FOR (u:User) REQUIRE u.id IS UNIQUE;

### CREATE CONSTRAINT film_id_unique IF NOT EXISTS
###  FOR (f:Film) REQUIRE f.id IS UNIQUE;

### CREATE CONSTRAINT director_id_unique IF NOT EXISTS
###  FOR (d:Director) REQUIRE d.id IS UNIQUE;

### CREATE CONSTRAINT person_id_unique IF NOT EXISTS
  ### FOR (p:Person) REQUIRE p.id IS UNIQUE;

### // 2) Criar Adam Sandler (Person)
### MERGE (adam:Person {id:'P1'})
###  ON CREATE SET adam.name = 'Adam Sandler',
                adam.roles = ['actor','producer'],
                adam.createdAt = datetime();

### // 3) Criar Directors
### UNWIND [
 ###  {id: 'D1', name: 'Frank Coraci'},
###   {id: 'D2', name: 'Peter Segal'},
 ###  {id: 'D3', name: 'Dennis Dugan'},
###   {id: 'D4', name: 'Jeremiah Zagar'}
###     ] AS dir
###   MERGE (d:Director {id: dir.id})
 ###  ON CREATE SET d.name = dir.name;

###   // 4) Criar Films
### UNWIND [
  ###  {id: 'F1',  title: 'Click',                            year: 2006, directorId: 'D1'},
 ###   {id: 'F2',  title: 'Como Se Fosse a Primeira Vez',     year: 2004, directorId: 'D2'},
 ###   {id: 'F3',  title: 'Gente Grande',                     year: 2010, directorId: 'D3'},
###    {id: 'F4',  title: 'Esposa de Mentirinha',             year: 2011, directorId: 'D3'},
###    {id: 'F5',  title: 'Um Maluco no Golfe',               year: 1996, directorId: 'D3'},
  ###  {id: 'F6',  title: 'Tratamento de Choque',             year: 2003, directorId: 'D2'},
###    {id: 'F7',  title: 'O Paizão',                         year: 1999, directorId: 'D3'},
 ###   {id: 'F8',  title: 'Juntos e Misturados',              year: 2014, directorId: 'D1'},
  ###  {id: 'F9',  title: 'Arremessando Alto',                year: 2022, directorId: 'D4'},
###    {id: 'F10', title: 'The Ridiculous 6',                year: 2015, directorId: 'D1'}
###       ] AS film
###  MERGE (f:Film {id: film.id})
###   ON CREATE SET f.title = film.title, f.year = film.year, f.createdAt = datetime()
###  WITH film, f
### MATCH (d:Director {id: film.directorId})
###  MERGE (f)-[:DIRECTED_BY]->(d);

### // 5) Criar Users
### UNWIND [
###   {id: 'U1',  name: 'João'},
###   {id: 'U2',  name: 'Maria'},
 ###  {id: 'U3',  name: 'Ana'},
 ###  {id: 'U4',  name: 'Pedro'},
###   {id: 'U5',  name: 'Lucas'},
 ###  {id: 'U6',  name: 'Carla'},
###   {id: 'U7',  name: 'Bruno'},
###   {id: 'U8',  name: 'Júlia'},
###   {id: 'U9',  name: 'Felipe'},
###   {id: 'U10', name: 'Sofia'}
###      ] AS u
### MERGE (usr:User {id: u.id})
###   ON CREATE SET usr.name = u.name, usr.createdAt = datetime();

### // 6) Criar relacionamentos WATCHED
### UNWIND [
 ###  {userId: 'U1',  filmIds: ['F1','F3']},
###   {userId: 'U2',  filmIds: ['F4','F2']},
###   {userId: 'U3',  filmIds: ['F1','F9']},
###   {userId: 'U4',  filmIds: ['F5','F3']},
###   {userId: 'U5',  filmIds: ['F4','F10']},
###   {userId: 'U6',  filmIds: ['F2','F7']},
 ###  {userId: 'U7',  filmIds: ['F6','F9']},
 ###  {userId: 'U8',  filmIds: ['F8','F1']},
###   {userId: 'U9',  filmIds: ['F10','F5']},
 ###  {userId: 'U10', filmIds: ['F7','F2']}
###      ] AS watchRow
###    MATCH (uNode:User {id: watchRow.userId})
###  UNWIND watchRow.filmIds AS fid
###    MATCH (fNode:Film {id: fid})
###  MERGE (uNode)-[r:WATCHED]->(fNode)
 ###  ON CREATE SET r.watchedAt = datetime();

###  // 7) Adam Sandler atua e produz todos os filmes
###  MATCH (adam:Person {id:'P1'})
###  MATCH (f:Film)
### MERGE (adam)-[:ACTED_IN]->(f)
### MERGE (adam)-[:PRODUCED_BY]->(f);

### // 8) Adam Sandler trabalhou com todos os diretores
### MATCH (adam:Person {id:'P1'})
### MATCH (d:Director)
### MERGE (adam)-[:WORKED_WITH]->(d);

### // 9) Adam Sandler é popular entre todos os usuários
### MATCH (adam:Person {id:'P1'})
### MATCH (u:User)
### MERGE (adam)-[:POPULAR_WITH]->(u);

### // 10) Índice adicional
### CREATE INDEX film_title_idx IF NOT EXISTS
###   FOR (f:Film) ON (f.title);

#### // -------------------------------------------------------
