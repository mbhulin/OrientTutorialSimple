# Additional Tasks and Quiz
In this chapter you have learned how to create an OrientDB graph database using OrientDB Console. Now you should do some programming tasks yourself.

* Add some more unit tests to ``CourseTests.java``
.
* Create a Java class ``StudentTests.java`` with unit tests for the OrientDB classes *Student* and *attends*.
* Add some *students* and *courses* to your database with OrientDB Studio.

<quiz name="Quiz: Database Schema for 'Course Participation'">
    <question multiple>
        <p>Which alternatives does OrientDB offer regarding a database schema?</p>
        <answer correct>Schema-less mode: Each vertex or edge may have different properties</answer>
        <answer>Automatic-schema mode: OrientDB creates and updates a schema automatically when new vertices are inserted</answer>
        <answer correct>Schema-full mode: All vertices of a vertex- or edge-class must have the same predefined structure</answer>
        <answer correct>Schema-mixed mode: Some properties of a vertex- or edge-class are predifined, others may be added arbitrarily for each object</answer>
        <answer>OrientDB offers a set of predefined schemas, e. g. Business, Routing, Finance or WaterSupply. You can choose one that fits for your problem.</answer>
        <explanation>OrientDB supports schema-less, schema-full and schema-mixed mode (see <a href="http://orientdb.com/docs/last/Graph-Schema.html"> documentation</a> for details)</explanation>
    </question>
    <question multiple>
        <p>Which tools or languages can you use to define a schema for OrientDB?</p>
        <answer correct>OrientDB Studio with a graphical user interface</answer>
        <answer>OrientDB OXML Schema Creator</answer>
        <answer correct>OrientDB Studio with SQL</answer>
        <answer correct>OrientDB Console</answer>
        <answer correct>Java with Tinkerpop Blueprints</answer>
        <answer>Oracle designer</answer>
        <explanation>To define a schema for OrientDB you can use <a href="http://orientdb.com/docs/last/Studio-Schema.html"> Studio with the Schema Manager</a> or Studio with SQL <a href="http://orientdb.com/docs/last/SQL-Create-Class.html"> 'Create Class'</a> and <a href="http://orientdb.com/docs/last/SQL-Create-Property.html"> 'Create Property'</a> commands, OrientDB Console or in Java the <a href="http://orientdb.com/docs/last/Graph-Schema.html#working-with-custom-vertex-and-edge-types"> createVertexType() and createEdgeType() methods.</a></explanation>
    </question>
    <question>
    <p>Which is the correct syntax if you want to create a new vertex class "Account" using the Console with SQL?</p>
    <answer>create vertex class Account;</answer>
    <answer correct>create class Account extends V;</answer>
    <answer>create class Account under V;</answer>
    <answer>create new vertex Account;</answer>
    <explanation><a href="http://orientdb.com/docs/last/SQL-Create-Class.html"> See documentation for CREATE CLASS</a></explanation>
    </question>
    <question>
    <p>Which is the correct syntax if you want to create a new property "accountNr" of the OrientDB class "Account" using the Console with SQL?</p>
    <answer>alter class Account add (accountNr integer);</answer>
    <answer correct>create property Account.accountNr integer;</answer>
    <answer>create property accountNr integer;</answer>
    <answer>create property accountNr in Account as integer;</answer>
    <explanation><a href="http://orientdb.com/docs/last/SQL-Create-Class.html"> See documentation for CREATE CLASS</a></explanation>
    </question>
    <question>
    <p>You want to create the property "accountNr" of the OrientDB class "Account" which should exist for every record of the class and must not be empty (null). Which constraint(s) is/are necessary?</p>
    <answer>mandatory constraint</answer>
    <answer>not null constraint</answer>
    <answer>obligate constraint</answer>
    <answer correct>both constraints: mandatory and not null</answer>
    <explanation>The mandatory constraint ensures that every record has a field accountNr but this field need not have a value. Not null means that if a field accountNr is present it must have a value other than NULL but there may be records without this field. Only both constraints together guarantee that each record has an accountNr with a value. There is no constraint "obligate".</explanation>
    </question>
</quiz>

