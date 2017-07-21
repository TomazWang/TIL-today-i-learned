# Android Architecture Components Learning Note - Room


## What is Room
Room is a Sqlite object mapping library. A member of Android Architecture Compnents. Room provides a easier way to create and use sqlite database without reapting code. Futhermore, error codes make senecs when the error was cause by SQL queries.


## How to use Room

### Layers
Room has 3 major components: Database, Entity, Dao(Data Access Objects).

- Database: Use annotation to define an abstract database class which provides one or more DAOs.
- Dao: Dao is the bridge to get or change the values in db. 
- Entity: Entities repersents a value object in the db.


### The Codes
Let's say I'm building a todo list app. I have a table of "task" and a table of "note" in my db. Those notes will be linked with a task when generated.

First, define your model.

```java

// Task.java
 public interface Task {

    long getId();
    String getTitle();
    int getLevel();
    boolean isDone();

}


// TaskEntity.java
@Entity(tableName = "tasks")
public class TaskEntity implements Task {
    // the primary key of db
    @PrimaryKey(autoGenerate = true)
    private long id;

    private String title;
    private int level;
    
    // link to a specific coloumn in db.
    @ColumnInfo(name = "done_status")
    private boolean isDone;

    // getter and setter here...
}

```

Don't forget, I want to link my note with task using foreign key. So my NoteEntity will look like this:

```java

@Entity(tableName = "notes",
        foreignKeys = {
                @ForeignKey(
                        entity = TaskEntity.class,
                        parentColumns = "id",
                        childColumns = "task_id",
                        onDelete = ForeignKey.CASCADE
                )},
        indices = { @Index(value = "task_id")}
)
public class NoteEntity implements Note {

    @PrimaryKey(autoGenerate = true)
    private long id;

    @ColumnInfo(name = "task_id")
    private long taskId;

    @ColumnInfo(name = "note_text")
    private String noteText;

    @ColumnInfo(name = "post_at")
    private Date postAt;


    // getter, setter and consturcters

}
```

Now everything's prepared. We move to the second layer - Dao. Use `@Dao` annotation to mark the interface as a dao.

```java
// TaskDao.java
@Dao
public interface TaskDao {

    @Query("SELECT * FROM tasks")
    List<TaskEntity> loadTasks();

    @Query("select * from tasks where id = :taskId")
    TaskEntity loadTask(int taskId);

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    void insertAll(List<TaskEntity> tasks);

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    void insertTask(TaskEntity... tasks);

    @Update
    void updateTask(TaskEntity... tasks);

    @Update
    void updateTask(List<TaskEntity> tasks);


    // and so on..

}

```

Note that sql query are now in annotation. Which makes Room to process them before use. Room will generate semantic error code if the SQL query has a typo or something wrong.

Now, we are finally creating our database class.

```java
// AppDatabase.java
@Database(
        entities = {
                TaskEntity.class,
                NoteEntity.class
        },
        version = 1
)
@TypeConverters(DataConverter.class)
public abstract class AppDatabase extends RoomDatabase{

        static final String DATABASE_NAME = "app-db";

        public abstract TaskDao taskDao();
        public abstract NoteDao noteDao();
}
```

`@TypeConverters` make Room know where how to do when dealing with data types that sqlite database can't recognize.

```java
// Build the database with Room is so simple now!!
mDb = Room.databaseBuilder(context, AppDatabase.class, DATABASE_NAME).build();

```


This is just tip of the iceberg of the new Android architecture. Androdi architecture is now more like MVVM than the old way.


## See Also:
> [Room Persistence Library](https://developer.android.com/topic/libraries/architecture/room.html)
> [Architecture Components: Improve Your App's Design](https://www.youtube.com/watch?v=vOJCrbr144o)
