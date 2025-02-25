# Room Database in Android
## 1. Introduction
Managing local data efficiently is crucial for Android applications, and Google's Room Database provides a robust solution for handling persistent data. Room is an ORM (Object-Relational Mapping) library that simplifies database interactions and improves app performance compared to the traditional SQLite approach.
### Why Use Room Database?
- **Easier than raw SQLite:** Room reduces boilerplate code and makes database handling simple.
- **Compile-time verification of SQL queries:** Ensures error-free queries before runtime.
- **Provides better performance and maintainability:** Encourages a structured database design.
## 2. What is Room Database?
Room is a part of the **Android Jetpack** architecture components that provides an **abstraction layer** over SQLite. Instead of writing raw SQL queries, developers can use Room’s annotations and entity-based approach for easy data storage and retrieval.
### How Room Differs from SQLite
| Feature	| SQLite	| Room Database | 
| ------- | ------- | ------------- |
| SQL Queries	| Manually written	| Simplified with annotations (@Query) |
| Compile-time Verification	| No	| Yes |
| Kotlin Flow Support |	No | Yes |
| Boilerplate Code |	High |	Minimal |
## 3. Key Components of Room Database
Room consists of three major components:
1. **Entity** – Represents a table in the database.
2. **DAO (Data Access Object)** – Defines SQL operations.
3. **Database Class** – Provides the database instance.
## 4. Setting Up Room Database in an Android Project
### Add Dependencies
Add the following dependencies in your top-level Gradle build file:
```
plugins {
    id("androidx.room") version "2.6.1" apply false
    id("com.google.devtools.ksp") version "2.0.20-1.0.25" apply false
}
```
Add the following dependencies in your build.gradle (Module: app):
```
plugins {
    id("androidx.room")
    id("com.google.devtools.ksp")
}
android {
    ...
    room {
        schemaDirectory("$projectDir/schemas")
    }
}
dependencies {
    val room_version = "2.6.1"
    implementation("androidx.room:room-runtime:$room_version")
    implementation("androidx.room:room-ktx:$room_version")
    ksp("androidx.room:room-compiler:$room_version")
}
```
## 5. Creating an Entity in Room
An Entity represents a table in the database. Let’s create a User entity:
```
@Entity(tableName = "user_table")
data class User(
    @PrimaryKey(autoGenerate = true)
    val id: Int = 0,
    val name: String,
    @ColumnInfo(name = "user_email") 
    val email: String
)
```
### Explanation:
- **@Entity** – Defines a table named "user_table".
- **@PrimaryKey(autoGenerate = true)** – Creates an auto-incrementing ID.
- **@ColumnInfo(name = "user_email")** – Customizes column names.
## 6. Implementing DAO (Data Access Object)
The DAO interface defines methods for database operations:
```
@Dao
interface UserDao {
    @Insert
    suspend fun insert(user: User)

    @Query("SELECT * FROM user_table ORDER BY id ASC")
    fun getAllUsers(): Flow<List<User>>

    @Update
    suspend fun update(user: User)

    @Delete
    suspend fun delete(user: User)
}
```
### Explanation:
- **@Insert** – Adds a new user.
- **@Query** – Retrieves all users.
- **@Update** – Updates an existing user.
- **@Delete** – Deletes a user.
## 7. Creating the Room Database Class
```
@Database(entities = [User::class], version = 1, exportSchema = false)
abstract class UserDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao

    companion object {
        @Volatile
        private var INSTANCE: UserDatabase? = null

        fun getDatabase(context: Context): UserDatabase {
            return INSTANCE ?: synchronized(this) {
                val instance = Room.databaseBuilder(
                    context.applicationContext,
                    UserDatabase::class.java,
                    "user_database"
                ).build()
                INSTANCE = instance
                instance
            }
        }
    }
}
```
### Explanation:
- **@Database** – Defines database entities and version.
- **Singleton Pattern** – Ensures a single instance of the database.

By integrating Room into your Android project, you can **enhance performance, reduce errors, and simplify data management.** Happy coding! 🚀
## FAQs
### 1. Is Room Database better than SQLite?
Yes, Room Database simplifies SQLite usage by reducing boilerplate code and providing compile-time SQL query verification.
### 2. How does Room handle database updates?
Room uses migrations to modify the database schema without data loss.
### 3. Can I use Room without Kotlin Flows?
Yes, but Kotlin Flows provides real-time updates and lifecycle awareness, making it a recommended choice. Flow provides coroutine support, making it more flexible for background operations.
### 5. Can Room be used with multiple databases in an app?
Yes, you can define multiple @Database classes for different databases within the same app.
