# Fill the Database with Test Data

Again we use OrientDB Console in batch mode to create some sample data in the database. Download the file with the Console commands: [FillDB.txt](FillDB.txt).

Open the file with a text editor. It consists of ```CREATE VERTEX``` and ```CREATE EDGE``` commands. Here are three examples:

```sql
create vertex Student set Name = {'@type':'d', '@class':'Name', 'FirstName':'Max', 'LastName':'Maker'},  StudentNr = 5000, Gender = 'male', DOB = '1993-05-15';
create vertex Course set Subject = 'Mathematics',  CourseNr = 50000, CreditPoints = 5, LearningObjectives = ['can multiply matrices', 'can integrate trigonometric functions', 'knows the definition of vector space'];
```



Edit FillDB.txt and add some additional *Student* and *Course* vertices and some *attend* and *requires* edges.

Finally run FillDB.

### Create Vertices and Edges Using a Java Program

You can use SQL to create a new vertex in a Java program where db represents an open database connection:

```java
db.command(new OCommandSQL ("INSERT INTO Course (Subject, CourseNr, CreditPoints) VALUES ('Mathematics', 50000, 5)")).execute();
```

As an alternative you can use Blueprint's addVertex method.



