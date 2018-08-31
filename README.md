# neo4j

MATCH (n:NODE)
DETACH DELETE n

MATCH (n:NODE {type: "Person" })
DETACH DELETE n

MATCH (n:NODE {type: "Practice" })
DETACH DELETE n

MATCH (n:NODE {type: "Role" })
DETACH DELETE n


LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/kelevraZero/neo4j/master/Practice.dat" as row FIELDTERMINATOR ';'
CREATE (n:NODE { id: row.ID, name: row.NAME, type: row.TYPE, weight: 5, practice: toInt(row.ID)})
RETURN row

LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/kelevraZero/neo4j/master/Role.dat" as row FIELDTERMINATOR ';'
CREATE (n:NODE { id: row.ID, name: row.NAME, type: row.TYPE, weight: row.EFFECTIF })
RETURN row

LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/kelevraZero/neo4j/master/PracticeRole.dat" as row FIELDTERMINATOR ';'
MATCH (a:NODE { id: row.ID_PRACTICE, type: "Practice" }), (b:NODE { id: row.ID_ROLE, type: "Role"})
SET b.practice =  toInt(row.ID_PRACTICE)
CREATE (b)-[r:LINKS { weight: 10 }]->(a) 
RETURN row

LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/kelevraZero/neo4j/master/RoleRole.dat" as row FIELDTERMINATOR ';'
MATCH (a:NODE { id: row.ID_ROLE_SRC, type: "Role"}), (b:NODE { id: row.ID_ROLE_TGT, type: "Role"})
MERGE (a)-[r:LINKS]->(b) ON CREATE SET r.weight = row.POID
RETURN row

LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/kelevraZero/neo4j/master/PersonRole.dat" as row FIELDTERMINATOR ';'
CREATE (a:NODE { id: row.ID_PERSON, name: " ", type: row.TYPE, weight: 0 })
RETURN row

LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/kelevraZero/neo4j/master/PersonRole.dat" as row FIELDTERMINATOR ';'
MATCH (a:NODE { id: row.ID_PERSON, type: "Person"}), (b:NODE { id: row.ID_ROLE, type: "Role"})
SET a.practice = toInt(b.practice)
CREATE (a)-[r:LINKS { weight: 10 }]->(b) 
RETURN row
