# Create a Java Program to Assign a Student to a Course

## Learning objectives
In this chapter of the tutorial you will learn to use the SQL extension **traverse** to traverse a graph recursively and to navigate from one vertex to a connected vertex.


If you prefer you can watch a screencast video:
<a href="RWM-Search-2.mp4
" target="_blank"><img src="StartScreencastVideo.jpg"
alt="Eclipse Video" width="200" height="30" border="10" /></a>

## Structure of the Application
This application assigns a student to a course. It checks whether the student has already attended all required courses of the desired course. The application consists of the following parts:

1. User input: name of a student
1. Retrieve all students with this name from the database and print their data
1. User input: student number of the student
2. Retrieve and print all courses
2. User input: course number
3. Select desired course and retrieve all courses which are required for this course following the *required* edges
1. For each of the required courses retrieve all *attends* edges and check whether one of them is connected to the selected student and the grade is better than F
2. If the check succeeds create a new attends edge from the selected student to the selected course


## Develop the Program
In the section [Unit Tests](unit_tests.md) of this tutorial you created a Java project in Eclipse. Open this project. Create a new JAVA class **CourseAssignment** in the package **applications**.

Since this is a very short program a main method with a linear structure is sufficient. The first three steps are equal to the grade report application. Hence the code can be copied from that application.

```java
public static void main(String[] args) {
  // Connect to database
  OrientGraphFactory factory = new OrientGraphFactory("remote:localhost/CourseParticipation", "admin", "admin"); // The OrientDB server must be running
  OrientGraph db = factory.getTx();

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

//User input: Student Number
String studNr;
System.out.println ("Type the student number: ");
try {
  studNr = bfr.readLine();
} catch (IOException e) {
  e.printStackTrace();
  return;
}

// Retrieve the student
studQuery = new OSQLSynchQuery <Vertex> ("select * from Student where StudentNr = ?");
studs = db.command(studQuery).execute(studNr);
Vertex stud = studs.iterator().next();
if (stud == null) {
  System.out.println ("No student with this student number!");
  return;
}
```

In the next step the user has to select a course. A list of all courses is printed and the user is asked to for the course number. This course is retrieved from the database.

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
