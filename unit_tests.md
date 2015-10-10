# Unit Tests
After establishing the schema you can try to store some data in your database. The schema helps to avoid storing wrong or inconsistent data. You can test this behaviour with *unit tests*. In this chapter you will also learn to access the database by a Java program.

### Create a new Java Project

Start Eclipse on your computer. In the main menu of eclipse click on *File* > *New* > *Java Project*

A "New Java Project"-dialog opens. Type in a name for the project e.g. *CourseParticipation* and choose the default JRE (Java Runtime Environment). Click *Next* to continue.

In the following "Java Settings"-dialog click on the *Libraries* tab and then on *Add External JARs...*

A file dialog opens. Navigate to your orient root directory and then to the jar-directory. Choose the following JAR-files where * is the version number installed on your computer ([compare documentation](http://orientdb.com/docs/last/Graph-Database-Tinkerpop.html)):
```
blueprints-core-*.jar
concurrentlinkedhashmap-lru-*.jar
jna-*.jar
jna-platform-*.jar
orientdb-client-*.jar
orientdb-core-*.jar
orientdb-enterprise-*.jar
orientdb-graphdb-*.jar
```
Add the library *JUnit4* to your project. To do this click on *Add Library*, choose *JUnit* and then *JUnit4*.

Click *Finish* - a new empty Java project is created and is added to the list of your Java projects in your workspace. You can see it in the Package Explorer. If it is not visible open it with
*Window* > *Show View* > *Package Explorer*

### Create a new Package
In the package explorer choose your new project *CourseParticipation*. Click on the "New Package"-icon or click on *File* > *New* > *Package* in the main menu. In the "New Package"-dialog type the package name *tests*.

### Create a new Java Class for the Unit Tests
In the package explorer choose the newly created package *tests*. In the main menu click on *File* > *New* > *Class* or click on the "New Java Class"-icon. In the "New Class"-dialog type in the class name e.g. *CourseTests*.

Add a static method ``setupBeforeClass()``. This method is executed before all tests and hence is used to prepare a proper environment for the tests. The connection to the database is established. Notice: This time a *remote connection* to the database is used. Hence the server must be running when you execute the tests.

```java
public class LocationTests {
	private static OrientGraph db;
	private static OrientGraphFactory factory;

	@BeforeClass
	public static void setUpBeforeClass() throws Exception {
		factory = new OrientGraphFactory("remote:localhost/RobotWorld1", "admin", "admin"); // The OrientDB server must be running
		db = factory.getTx(); // Connect to the database
		
		db.command(new OCommandSQL ("delete vertex Location")).execute(); // Delete all Location vertices in the database
	}
```

Add a static method ``teardownAfterClass()``. This method is executed after all tests and is used to cleanup the environment. The database connection is shut down and the factory is closed.

```java
	@AfterClass
	public static void tearDownAfterClass() throws Exception {
		db.shutdown();
		factory.close();
	}

```

* Add a test where you try to store a *Position2D* without the x-coordinate. This should fail because x is mandatory.

```java
	@Test
	public void testPositionWithoutX() {
		String errorMessage = "";
		Vertex pos = db.addVertex("class:Position");
		pos.setProperty("y", 100);
		pos.setProperty("z", 0);
		try {
			db.commit();
		} catch (Exception e) {
			db.rollback();
			errorMessage = e.getMessage();
		}
		Assert.assertTrue(errorMessage.contains("x' is mandatory"));
	}

```

* Add a test where you try to store a *Location* with *Name* set to NULL. In this test method SQL is used. Again OrientDB should not store this location.

```java
	@Test
	public void testLocationWithoutName () {
		String errorMessage = "";
		try {
			db.command(new OCommandSQL ("insert into Location (Name, Description) values (NULL, 'A location without name')")).execute();
			db.commit();
		} catch (Exception e) {
			db.rollback();
			errorMessage = e.getMessage();
		}
		Assert.assertTrue(errorMessage.contains("Name' cannot be null"));
	}
```

* Add a test where you try to store a correctly constructed *Location* together with its shape. This Location should be stored in the database.

```java
	@Test
	public void testLocationOK () {
		long nrLocationsBefore = db.countVertices("Location");
		String errorMessage = "No exception";
		Vertex pos1 = db.addVertex("class:Position", "x", 0, "y", 0, "z", 0);
		Vertex pos2 = db.addVertex("class:Position", "x", 1000, "y", 0, "z", 0);
		Vertex pos3 = db.addVertex("class:Position", "x", 1000, "y", 500, "z", 0);
		Vertex pos4 = db.addVertex("class:Position", "x", 0, "y", 500, "z", 0);
		
		ArrayList <Vertex> shape = new ArrayList <Vertex> ();
		shape.add(pos1);
		shape.add(pos2);
		shape.add(pos3);
		shape.add(pos4);
		
		Vertex newLocation = db.addVertex("class:Location");
		newLocation.setProperty("Name", "Extra Room");
		newLocation.setProperty("Description", "This room is not needed. It is an extra room.");
		newLocation.setProperty("shape", shape);

		try {
			db.commit();
		} catch (Exception e) {
			db.rollback();
			errorMessage = e.getMessage();
		}
		long nrLocationsAfter = db.countVertices("Location");
		Assert.assertEquals("No exception", errorMessage);
		Assert.assertEquals(nrLocationsBefore + 1, nrLocationsAfter);
		db.removeVertex(pos1);
		db.removeVertex(pos2);
		db.removeVertex(pos3);
		db.removeVertex(pos4);
		db.removeVertex(newLocation);
		db.commit();
	}
```

* Start the [OrientDB server](http://orientdb.com/docs/last/Tutorial-Run-the-server.html). Then run your test class. To do this right click on *LocationTests.java* in the Eclipse package explorer. In the pop up menu choose *Run As* > *JUnit Test*. All tests should succeed.

You can download both Java files, *CreateDBSchema.java* and *LocationTests.java*, as [ZIP-Archive here](RobotWorldModel_V1.zip).
