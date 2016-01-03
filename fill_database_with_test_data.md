# Fill the Database with Test Data

Again we use OrientDB Console in batch mode to create some sample data in the database. Download the file with the Console commands: [FillDB.txt](FillDB.txt).

Open the file with a text editor. First old test data are deleted if you had run FillDB.txt before:
```
delete edge E;
delete vertex V;
```

Then data are created with ```CREATE VERTEX``` and ```CREATE EDGE``` commands. Here are five examples:

```sql
script sql;
begin;
let s1 = create vertex Student set Name = {'@type':'d', '@class':'Name', 'FirstName':'Max', 'LastName':'Maker'},  StudentNr = 5000, Gender = 'male', DOB = '1993-05-15';
let c1 = create vertex Course set Subject = 'Mathematics',  CourseNr = 50000, CreditPoints = 5, LearningObjectives = ['can multiply matrices', 'can integrate trigonometric functions', 'knows the definition of vector space'];
let c7 = create vertex Course set Subject = 'Artificial Intelligence',  CourseNr = 50006, CreditPoints = 5, LearningObjectives = ['Data Mining', 'Expert Systems'];
commit;
begin;
let a1 = create edge attends from $s1 to $c1 set Semester = 20130, Attempt = 1, Grade = 'B';
let r4 = create edge requires from $c7 to $c1;
commit;
end;
```

To create the edges we must remember the previously created verteces. This is possible with a SQL-script. With ```let <variable> = <SQL>``` the result of a SQL command can be assigned to a variable. To reuse the variable prefix it with the dollar sign $. Additionally transactions are possible inside SQL-scripts. Start a transaction with ```begin``` and finish it with ```commit```. An SQL-script begins with ```script sql;``` and is finished and executed with ```end;```

Edit FillDB.txt and add some additional *Student* and *Course* vertices and some *attend* and *requires* edges.

Finally run FillDB.

### Create Vertices and Edges Using a Java Program

You can use SQL to create a new vertex in a Java program where db represents an open database connection:

```java
Vertex c1 = db.command(new OCommandSQL ("INSERT INTO Course (Subject, CourseNr, CreditPoints) VALUES ('Mathematics', 50000, 5)")).execute();
Vertex s1 = db.command(new OCommandSQL ("create vertex Student set Name = {'@type':'d', '@class':'Name', 'FirstName':'Max', 'LastName':'Maker'},  StudentNr = 5000, Gender = 'male', DOB = '1993-05-15'")).execute();
```

As an alternative you can use Blueprint's addVertex method. Here is an example:
```java
ODocument n1 = new ODocument ("Name");
n1.field("FirstName", "Max");
n1.field("LastName", "Maker");
Vertex S1 = db.addVertex("class:Student", "Name", n1, "StudentNr", 5000, "Gender", "male", "DOB", "1993-05-15");
```




