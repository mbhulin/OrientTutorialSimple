# Create a Java Program to Print a Grade Report

Learning objectives: In this chapter of the tutorial you will learn to
* use SQL inside of a Java program to query the database
* to iterate result sets
* to retrieve the conected vertices when qurying an edge class


If you prefer you can watch a screencast video:
<a href="RWM-Search-2.mp4
" target="_blank"><img src="StartScreencastVideo.jpg"
alt="Eclipse Video" width="200" height="30" border="10" /></a>

As a simple programming task we first will develop a Java program which prints a grade report for a selected student. The program consists of the following parts:

1. User input: name of a student
1. Retrieve all students with this name from the database and print their data
1. User input: student number of the student
1. Retrieve all *attends* edges of the selected student and the connected course vertices
2. Print the grades of each attends edge together with the subject of the corresponding course

In the section [Unit Tests](unit_tests.md) of this tutorial you created a Java project in Eclipse. Open this project. To implement the grade report create a new package in Eclipse: **applications**. Then create a new JAVA class **GradeReport** in this package.

Since this is a very short program a main method with a linear structure is sufficient. First we establish the connection to the database.

```java
public static void main(String[] args) {
  // Connect to database
  OrientGraphFactory factory = new OrientGraphFactory("remote:localhost/CourseParticipation", "admin", "admin"); // The OrientDB server must be running
  OrientGraph db = factory.getTx();
```

The user provides a student's name that is read using a BufferedReader.

```java
// User input: student name
String lastName;
System.out.println ("Type the last name of the student: ");
BufferedReader bfr = new BufferedReader (new InputStreamReader(System.in));
try {
  lastName = bfr.readLine();
} catch (IOException e) {
  e.printStackTrace();
  return;
}
```

The next step is to query the database: retrieve all students with the provided name. We use a simple SQL-query to do this.

```sql
select * from Student where Name.LastName = ?
```
This is a [prepared query](http://orientdb.com/docs/last/Document-Database.html#prepared-query) where ? is substituted at execution time by the current parameter of the execute method. Thus the query can be reused with other parameter values later. The result of ``db.command(<SQL-query>).execute(<parameter>)`` is an Iterable which is used in a for-loop to print the data of all students with the provided name.

```java
// Search for all students with the provided LastName
OSQLSynchQuery <Vertex> studQuery = new OSQLSynchQuery <Vertex> ("select * from Student where Name.LastName = ?");
Iterable <Vertex> studs = db.command(studQuery).execute(lastName);
if (studs.iterator().hasNext()) {
  for (Vertex stud : studs) {
    Vertex name = stud.getProperty("Name");
    System.out.println ((String) name.getProperty("FirstName") + " " + (String) name.getProperty("LastName") + ", DOB: " + stud.getProperty("DOB") + ", Stud-Nr: " + stud.getProperty("StudentNr"));
  }
} else { 
  System.out.println("No student found with this name!"); 
  return;
}

```

To select exactly one student even if more students exist with the same name, the user is asked to provide the desired student number.

```java
//User input: Student Number
String studNr;
System.out.println ("Type the student number: ");
try {
  studNr = bfr.readLine();
} catch (IOException e) {
  e.printStackTrace();
  return;
}
```

In a similar query as above but with the student number as parameter instead of the name the student data are retrieved and printed. Since the student number is unique for students we need not iterate the result set.

```java
// Retrieve the student and all his courses
studQuery = new OSQLSynchQuery <Vertex> ("select * from Student where StudentNr = ?");
studs = db.command(studQuery).execute(studNr);
Vertex stud = studs.iterator().next();
if (stud == null) {
  System.out.println ("No student with this student number!");
  return;
} else {
  Vertex name = stud.getProperty("Name");
  System.out.print ("Grade report for ");
  System.out.println ((String) name.getProperty("FirstName") + " " + (String) name.getProperty("LastName") + ", DOB: " + stud.getProperty("DOB") + ", Stud-Nr: " + stud.getProperty("StudentNr"));;
}
```

The last part of the program is the grade report. The student's grades are stored in the *attends* edges. Therefore we retrieve all attends edges that start at the selected student.  
Each edge connects two vertices: **out** specifies the source vertex where the edge comes out and **in** specifies the target vertex where the edge goes into. **out** must be our selected student. Instead of doing some String-operations and insert the **rid** (record id) of the student into the where condition we again use a prepared query and get the condition ``where out = ?``.

Instead of a **join** operation to connect the attends edges with the corresponding course which is necessary in relational databases we can retrieve the course easily by the **in** property of the *attends* edge.

```java
OSQLSynchQuery <Vertex> courseQuery = new OSQLSynchQuery <Vertex> ("select in, Semester, Attempt, Grade from attends where out = ? order by Semester");
Iterable <Vertex> result = db.command(courseQuery).execute(stud.getId());
for (Vertex item : result) {
  Vertex course = item.getProperty("in");
  System.out.println (course.getProperty("CourseNr") + " " + course.getProperty("Subject") + " " + item.getProperty("Semester") + " " + item.getProperty("Attempt") + " " + item.getProperty("Grade"));
}
```



