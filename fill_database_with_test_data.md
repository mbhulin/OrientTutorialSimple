# Fill the Database with Test Data

Again we use OrientDB Console in batch mode to create some sample data in the database. Download the file with the Console commands: [FillDB.txt](FillDB.txt).

Open the file with a text editor. It consists of ```CREATE VERTEX``` and ```CREATE EDGE``` commands.

Edit FillDB.txt and add some additional *Student* and *Course* vertices and some *attend* edges.

Finally run FillDB.

### Create Vertex using SQL

Instead of Blueprint's addVertex method you could use SQL to create a new vertex:

```java
db.command(new OCommandSQL ("INSERT INTO Location (Name, Description) VALUES ('Sophia's room','Bedroom of Sophia')")).execute();
```

