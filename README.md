To do this you need:
1. Fork a repository
2. Download your version of the project to your computer.
3. Add dependencies to pom.xml:
    mysql: mysql-connector-java: 8.0.30
    org.hibernate: hibernate-core-jakarta: 5.6.11.Final
4. Make a maven build (mvn clean install). For variety, we use Java version 1.8.
5. Add launch configuration via Idea. The implementation of this point can be seen in the lecture https://javarush.com/quests/lectures/jru.module3.lecture02 (point 4). The only difference is a different name for the artifact. If you did everything correctly and launched the application, you will see something like this:
6. In Workbench, run the rpg schema creation script:
CREATE SCHEMA `rpg` ;

7.Optional . If you want to see what behavior is expected, you can change com.game.service.PlayerServicethe annotation value in the class constructor parameter from “db” to “memory” . In this case, Spring will use the . After the test, don't forget to change the annotation value back to "db" .@QualifierIPlayerRepositoryPlayerRepositoryMemory@Qualifier

8. Place all the necessary annotations in the entity class com.game.entity.Player. The table should be called "player", the schema "rpg". For enams, use @Enumerated(EnumType.ORDINAL)in addition to annotations @Column. Let me remind you that the length of the name field should be up to 12 characters, the title field should be up to 30 characters. Absolutely all fields must not be null.

9. PlayerRepositoryDBAdd a private final field to the class SessionFactory sessionFactoryand initialize this field in the class constructor. Use properties as in normal tasks (we will work with the MySQL database version 8). Interesting things - add
properties.put(Environment.HBM2DDL_AUTO, "update");
This will allow you not to create a table manually (or through executing an sql script).

10.Implement all methods of the class. For a change, let's do it this way:
  * getAllImplement the method via NativeQuery- this method should receive all players taking into account the parameters: pageNumber- page number, pageSize- number of records per page
  * Implement the method getAllCountvia NamedQuery. Should return the total number of players in the database.
  * In the method beforeStop, call sessionFactorythe close. By having an annotation over the method @PreDestroy, Spring will call this method before stopping the application, and this will allow all system resources to be validly released.
  * The implementation of other methods is at your discretion. But don’t forget about transactions and commits for methods that somehow change the contents of the database.

11. Launch the application. If you did everything correctly, you will receive a working application. But there is no data there, so run the init.sql script (from resources) through Workbench so that it appears. After that, press F5 in the browser and check that you have implemented all the methods correctly.

12. It would be interesting to see exactly what queries Hibernate performs, so let's add query logging. To do this, add the p6spy:p6spy:3.9.1 dependency to pom.xml . In the resources folder, create a file spy.properties , in which specify:
driverlist=com.mysql.cj.jdbc.Driver
dateformat=yyyy-MM-dd hh:mm:ss a
appender=com.p6spy.engine.spy.appender.StdoutLogger
logMessageFormat=com.p6spy.engine.spy.appender.MultiLineFormat
And in the constructor of the PlayerRepositoryDB class, change two options:
properties.put(Environment.DRIVER, "com.p6spy.engine.spy.P6SpyDriver");
properties.put(Environment.URL, "jdbc:p6spy:mysql://localhost:3306/rpg");

Now in the server output for each request you will see 2 lines. The first is what statement is prepared, the second is the request with the inserted parameters.
