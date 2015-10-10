# Fill the Database with Test Data

Again we use OrientDB Console in batch mode to create some sample data in the database. You can download the file  [FillDB.txt](FillDB.txt)
It consists of 

* If you like you can edit FillDB.java and add some additional locations, objects or positions.
* Finally run FillDB.





### Create Vertex using SQL
Instead of Blueprint's addVertex method you could use SQL to create a new vertex:

```java
db.command(new OCommandSQL ("INSERT INTO Location (Name, Description) VALUES ('Sophia's room','Bedroom of Sophia')")).execute();
```

### Add an Embedded Document
In our database for service robots mobile objects have a size which consists of three dimensions: x, y and z. Since the property **Size3D** is not a linked vertex but embedded inside of the object vertex it has to be created as a **document** first, using the [Document API](http://orientdb.com/docs/last/Document-Database.html):

```java
ODocument sizeTable = new ODocument ("Size3D");
sizeTable.field("x", 100);
sizeTable.field("y", 100);
sizeTable.field("z", 85);
Vertex tableI = db.addVertex("class:Object", "Name", "dining table", "Description", "My circular dining table", "Size", sizeTable);
```

### Add a Linked List
Locations have a Shape property which consists of a list of positions. To store a location with its shape you have to store the positions first, then create an ArrayList of positions, and finally use this list as parameter in the ``addVertex()`` method:

```java
Vertex p1 = db.addVertex("class:Position", "x", 400, "y", 200, "z", 0);
Vertex p2 = db.addVertex("class:Position", "x", 600, "y", 200, "z", 0);
Vertex p3 = db.addVertex("class:Position", "x", 800, "y", 200, "z", 0);
Vertex p4 = db.addVertex("class:Position", "x", 1050, "y", 200, "z", 0);
ArrayList <Vertex> shapeSleepingRoom = new ArrayList <Vertex> ();
shapeSleepingRoom.add(p1);
shapeSleepingRoom.add(p2);
shapeSleepingRoom.add(p3);
shapeSleepingRoom.add(p4);
Vertex mySleepingRoom = db.addVertex("class:Location", "Name", "sleeping room", "Description", "Sleeping room of Mr. Miller", "Shape", shapeSleepingRoom);
```

### Add an Edge
To create an edge use the ``addEdge()`` method and provide the vertices to connect as parameters. If you do not use light weight edges you can add properties to edges.

```java
Edge passTime1 = db.addEdge(null, posTable2, doorSleepM, "IS_CONNECTED_TO");
passTime1.setProperty("PassTimeSec", 10);
```

Notice that the first parameter is not necessary in OrientDB but you can provide the subclass of E as first parameter instead of null:

```java
Edge passTime1 = db.addEdge("class:IS_CONNECTED_TO", posTable2, doorSleepM, "IS_CONNECTED_TO");
```
