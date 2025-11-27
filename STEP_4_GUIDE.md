# Migration Step 4: Data Layer Migration

## Step Overview and Objectives

The **Data Layer Migration** step focuses on converting the database models and queries from PHP to Python. This involves porting the database schema, adapting the ORM or raw queries, and ensuring that the data access layer in the Python application replicates the functionality of the source PHP application. 

### Objectives:
- Ensure the Python application has an equivalent and optimized database schema.
- Replace PHP-based ORM or raw queries with Python equivalents.
- Migrate data access logic to align with Pythonic conventions.
- Maintain data integrity and consistent performance.

---

## Prerequisites and Dependencies

Before starting, ensure the following:

1. **Environment Setup**:
   - Python 3.x installed.
   - Target database server (e.g., PostgreSQL, MySQL) configured and accessible.
   - Required Python libraries (e.g., `SQLAlchemy`, `Django ORM`, or `Peewee`) installed.

   ```bash
   pip install sqlalchemy psycopg2  # Example for SQLAlchemy with PostgreSQL
   ```

2. **Access to PHP Application**:
   - Source PHP codebase and database queries.
   - Database schema (in SQL or migration scripts) from the PHP application.

3. **Database Access**:
   - Credentials and access to both source and target databases for testing and validation.

4. **Repository Setup**:
   - Python repository initialized under version control (`repository`).
   - Existing Python application structure ready for integrating the database layer (e.g., `models.py` or equivalent).

5. **Backup Data**:
   - Backup the source database to protect against accidental data loss.
   - Example for MySQL:
     ```bash
     mysqldump -u root -p source_db > source_db_backup.sql
     ```

---

## Detailed Implementation Instructions

### Task 1: Port Database Schema

#### Step 1.1: Export the Source Database Schema
Export the schema from the source PHP application's database.

```bash
mysqldump -u root -p --no-data source_db > schema.sql
```

#### Step 1.2: Analyze Schema Compatibility
Review the schema for any differences between the source and target database systems (e.g., MySQL to PostgreSQL). Look for:
- Data types (e.g., `TINYINT` in MySQL maps to `BOOLEAN` in PostgreSQL).
- Indexes and constraints.
- Naming conventions (e.g., snake_case vs camelCase).

#### Step 1.3: Define Schema in Python
Use a Python ORM like SQLAlchemy or Django ORM to define the schema.

**Example using SQLAlchemy**:

```python
from sqlalchemy import Column, Integer, String, Boolean, ForeignKey
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()

class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True)
    username = Column(String(50), nullable=False, unique=True)
    email = Column(String(100), nullable=False, unique=True)
    is_active = Column(Boolean, default=True)

class Post(Base):
    __tablename__ = "posts"
    id = Column(Integer, primary_key=True)
    title = Column(String(200), nullable=False)
    content = Column(String)
    user_id = Column(Integer, ForeignKey('users.id'))
```

#### Step 1.4: Apply Migrations
Use a migration tool like Alembic or Django migrations to apply the schema to the target database.

```bash
alembic init migrations
alembic revision --autogenerate -m "Initial migration"
alembic upgrade head
```

Verify the schema in the database using a tool like `pgAdmin` or `MySQL Workbench`.

---

### Task 2: Convert ORM/Queries

#### Step 2.1: Identify Source Queries
Extract queries from the PHP application. Example PHP query:

```php
$query = "SELECT * FROM users WHERE email = :email";
$stmt = $pdo->prepare($query);
$stmt->execute(['email' => $email]);
$result = $stmt->fetchAll();
```

#### Step 2.2: Convert Queries to Python
Translate queries into Python ORM or raw SQL.

**Example with SQLAlchemy**:

```python
from sqlalchemy.orm import Session

def get_user_by_email(session: Session, email: str):
    return session.query(User).filter(User.email == email).first()
```

**Example with Raw SQL**:

```python
def get_user_by_email_raw(connection, email: str):
    query = "SELECT * FROM users WHERE email = %s"
    result = connection.execute(query, (email,))
    return result.fetchone()
```

---

### Task 3: Migrate Data Access Layer

#### Step 3.1: Abstract Data Access Logic
Create a repository pattern to manage database access. This helps decouple the database logic from the application logic.

**Example**:

```python
class UserRepository:
    def __init__(self, session):
        self.session = session

    def get_user_by_email(self, email: str):
        return self.session.query(User).filter(User.email == email).first()

    def create_user(self, username: str, email: str):
        user = User(username=username, email=email)
        self.session.add(user)
        self.session.commit()
        return user
```

#### Step 3.2: Integrate with Application
Replace PHP data access calls with Python repository methods in the application logic.

---

## Code Examples and Snippets

### Full Example: Data Access and Query Execution

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

DATABASE_URL = "postgresql://user:password@localhost/db_name"

engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

# Initialize session
session = SessionLocal()

# Use the repository
repo = UserRepository(session)
user = repo.get_user_by_email("test@example.com")
print(user.username)
```

---

## Common Pitfalls and How to Avoid Them

1. **Data Type Mismatches**:
   - **Issue**: Differences in data types between MySQL and PostgreSQL.
   - **Solution**: Use a compatibility guide (e.g., [MySQL to PostgreSQL Data Type Mapping](https://www.postgresql.org/docs/current/datatype.html)).

2. **Missing Constraints**:
   - **Issue**: Foreign keys or unique constraints may not be properly migrated.
   - **Solution**: Verify schema integrity after migration using database tooling.

3. **Performance Issues**:
   - **Issue**: ORM queries may introduce inefficiencies.
   - **Solution**: Profile queries using tools like `EXPLAIN` and optimize them.

---

## Testing Checklist

- [ ] Schema matches between source and target databases.
- [ ] All queries return identical results between PHP and Python implementations.
- [ ] Data access layer integrates seamlessly with the application.
- [ ] Performance benchmarks meet or exceed expectations.

---

## Validation Criteria

1. Schema is correctly migrated and verified.
2. Data queries in Python return the same results as in PHP.
3. Automated tests for data models and queries pass successfully.

---

## Troubleshooting Guide

### Issue: "OperationalError: could not connect to server"
- **Cause**: Database credentials are incorrect or the server is unreachable.
- **Solution**: Check connection string and database server status.

### Issue: "ProgrammingError: column does not exist"
- **Cause**: Schema mismatch.
- **Solution**: Verify the schema migration process and inspect the database.

---

## Resources and References

1. [SQLAlchemy Documentation](https://docs.sqlalchemy.org/)
2. [Django ORM Documentation](https://docs.djangoproject.com/en/stable/topics/db/)
3. [Alembic Documentation](https://alembic.sqlalchemy.org/en/latest/)
4. [MySQL to PostgreSQL Migration Guide](https://wiki.postgresql.org/wiki/Converting_from_other_Databases_to_PostgreSQL)

---

## Next Steps

- Proceed to **Step 5: Business Logic Migration**, ensuring that the data layer integrates seamlessly with the application logic.
- Finalize the database connection pooling and error handling strategies.