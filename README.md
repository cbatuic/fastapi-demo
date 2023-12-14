# fastapi-demo

```bash
# backend
|_ main.py
|_ models
  |__ Student.py
  |__ Violation.py

# Setup
$ pip install fastapi mysql-connector-python pydantic
$ uvicorn main:app --reload
```

```python
# models/Student.py

from pydantic import BaseModel

class Student(BaseModel):
    id: int
    name: str
    address: str
```

```python
# main.py

from fastapi import FastAPI, HTTPException
from fastapi.responses import JSONResponse
from models.Student import Student
import mysql.connector

# Database configuration - A function to create a connection to the database
def create_connection():
    db_config = {
         "host": "localhost",
         "user": "root",
         "password": "",
         "database": "testreact",
         "port": 3306
     } 
    return mysql.connector.connect(**db_config)

# FastAPI app
app = FastAPI()

# API endpoint to get all student
@app.get("/student/", response_model=list[Student])
async def get_student():
    try:
        # Create a connection to the database
        conn = create_connection()
        cursor = conn.cursor(dictionary=True)

        # Execute SQL query to fetch all student
        cursor.execute("SELECT * FROM student")
        student = cursor.fetchall()
        return student
    except mysql.connector.Error as e:
        # Handle MySQL errors
        return JSONResponse(content={"error": f"MySQL Error: {e}"}, status_code=500)
    except Exception as e:
        # Handle other exceptions
        return JSONResponse(content={"error": f"Error: {e}"}, status_code=500)
    finally:
        # Close the cursor and database connection
        if 'cursor' in locals() and cursor is not None:
            cursor.close()
        if 'conn' in locals() and conn is not None:
            conn.close()
```
