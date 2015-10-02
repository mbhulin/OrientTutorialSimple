# Create a new Database
OrientDB offers several possibilities to create new databases:
* Use **Studio** to [create a new database interactively](http://orientdb.com/docs/last/Home-page.html#create-a-new-database)
* Use **Console** and the [create database command](http://orientdb.com/docs/last/Console-Command-Create-Database.html)
* Use the **Java API** to create a database with a Java program.

In this tutorial we will use the Console with a script file.

## Create a Database using the Console
If you prefer to watch a screencast video click on the video start page.

<a href="EclipseRobotWorldModel.mp4
" target="_blank"><img src="StartScreencastVideo.jpg"
alt="Eclipse Video" width="200" height="30" border="10" /></a>

You can start the OrientDB Console application and then enter one console command after the other interactively. However we will use the batch mode of Console.

* Create an empty text file *CreateDB.txt* and open it with your favorite editor.
* Enter OrientDB commands line by line (see below). Close each command by a semicolon ";".
* Save *CreateDB.txt*
* Open a command window or shell
* Go to the bin directory of your OrientDB installation, e.g. ```cd /orientdb/bin```
* Start OrientDB Console with the command file:  
```console.bat <path to command file>/CreateDB.txt``` on Windows or  
```console.sh <path to command file>/CreateDB.txt``` on Linux
* Enter the console command ```info```. You get an overview of the just created database with all created classes.
* Type ```exit``` to close OrientDB Console.

Which are the commands you enter in the command file *CreateDB.txt*?

First switch on Echo mode. This will show you the executed commands during the batch run:  
```set echo true;```

Then create the new database:  
```create database plocal:/orientdb/databases/CourseParticipation;```

You are automatically connected to the newly created database. So you can go on and create some classes in it:  
```sql
create class Student extends V;
create class Course extends V;
create class attends extends E;
create class requires extends E;
create class Name;
```

CourseParticipation was created as graph database. OrientDB creates two default classes for a graph database: **V** for vertices and **E** for edges. Objects like students or courses are usually stored as vertices. Hence *Student* and *Course* are created as subclasses of *V*. Relationships are usually stored as edges. Hence the relations *attends* and *requires* are created as subclasses of *E*.

Students have a name. This name is not a single value but structured. It consists of a first name, a last name, perhaps a title ect. Instead of creating separate fields for all these attributes as we would have to do in a relational table in OrientDB we can connect these fields more tightly in an extra class. Since Names are not standalone objects but belong to students the Name objects will not be stored as vertices in the graph. They will be embedded in the Student objects. Hence the Name class is created as a pure document class.

In schema-less mode we could begin to store objects in the classes. However we want to work with a schema. Hence we create the properties of the classes:
```
create property Name.FirstName string;
create property Name.SecondName string;
create property Name.LastName string;
create property Name.Title string;
```
An empty name doesn't make sense. A *mandatory* and a *not-null* constraint enforce that at least the last name is set. *MANDATORY* ensures that every record has this field and *NOTNULL* guarantees that this field has a value. Use ```ALTER PROPERTY``` to set the constraints.
```
alter property Name.LastName mandatory true;
alter property Name.LastName notnull true;
```

Now you can create the properties of the Student class:
```
create property Student.Name embedded Name;
alter property Student.Name mandatory true;
alter property Student.Name notnull true;
create property Student.DOB date;  // Date of birth
create property Student.Gender string;
alter property Student.Gender regexp [male|female];
create property Student.MatriculationNr integer;
alter property Student.MatriculationNr mandatory true;
alter property Student.MatriculationNr min 1000;
```

The student's name is embedded and must not be empty. For gender only the values "male" and "female" are allowed. This is guaranteed by a regular expression constraint. 