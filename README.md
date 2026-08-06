## Project Description

This project is an AI-powered **Natural Language to SQL (NL2SQL) system** built using **Vanna 2.0 and FastAPI**.
The main goal of the system is to allow users to interact with a database using **plain English queries**, without needing to write SQL manually. The system takes a user’s question, converts it into a SQL query using an LLM (Google Gemini), executes it on a SQLite database, and returns the results in a structured format.
For example, a user can ask:
> "Show the top 5 patients by total spending"
The system will:
1. Convert the question into a SQL query
2. Validate the query for safety
3. Execute it on the database
4. Return results along with an optional chart
The backend is built using **FastAPI**, and the AI logic is handled using **Vanna 2.0’s agent-based architecture**, which includes tools for SQL execution, visualization, and memory learning.
A sample clinic management database is used, containing data about:
* patients
* doctors
* appointments
* treatments
* invoices
This project demonstrates how AI can be integrated into backend systems to enable **intelligent data querying**, while also handling real-world challenges like SQL validation, error handling, and schema mismatches.

## Setup Instructions

Follow the steps below to set up and run the complete NL2SQL system.
### 1. Clone the Repository

```bash
git clone <https://github.com/Swathi88-devi/NL2SQL.git>
cd project
```
### 2. Create a Virtual Environment (Recommended)

```bash
python -m venv venv
venv\Scripts\activate   # On Windows
```
### 3. Install Dependencies

```bash
pip install -r requirements.txt
```
This installs:

* Vanna 2.0 (NL2SQL agent)
* FastAPI (backend framework)
* Plotly (charts)
* Google Gemini SDK (LLM)
### 4. Setup Environment Variables

Create a `.env` file in the project root:

```env
GOOGLE_API_KEY=your_api_key_here
```
Get your free API key from:
https://aistudio.google.com/apikey
### 5. Create and Populate Database

Run:

```bash
python setup_database.py
```
What this does:

* Creates `clinic.db`
* Creates all 5 tables (patients, doctors, appointments, treatments, invoices)
* Inserts realistic dummy data (200 patients, 500 appointments, etc.)

### 6. Initialize Vanna Agent (vanna_setup.py)

No manual command needed, but this file is **automatically used** when the API starts.

 What it does:

* Connects to the SQLite database
* Initializes Gemini LLM
* Sets up tools (SQL execution, visualization, memory)
* Creates the Vanna agent
### 7. Seed Agent Memory

Run:

```bash
python seed_memory.py
```

### 8. Start the API Server

```bash
uvicorn main:app --port 8000
```

### 9. Access the API

Open in browser:

```
http://localhost:8000/docs
```

This opens Swagger UI where you can test the system.

### Run Everything (Quick Setup)

```bash
pip install -r requirements.txt && \
python setup_database.py && \
python seed_memory.py && \
uvicorn main:app --port 8000
```
## How Everything Works Together
User Question
   ↓
main.py (FastAPI API)
   ↓
vanna_setup.py (Agent)
   ↓
seed_memory.py (trained examples)
   ↓
SQLite Database (setup_database.py)
   ↓
Results returned to user
##     How to Run the Memory Seeding Script

The memory seeding script is used to **pre-load the Vanna agent with sample question–SQL pairs**, which helps improve the accuracy of SQL generation.
###  Command to Run

```bash
python seed_memory.py
```
###  What It Does

* Initializes the Vanna agent using `vanna_setup.py`
* Inserts predefined **question–SQL examples** into the agent memory
* Helps the model understand:

  * database schema (tables and columns)
  * common query patterns like joins, aggregations, and filters

### Requirements Before Running

Make sure:

* The database is already created:

```bash
python setup_database.py
```

* A valid `GOOGLE_API_KEY` is set in the `.env` file

### Purpose

This step is important because it improves the agent’s ability to generate **correct and relevant SQL queries** by learning from examples.
##  How to Start the API Server

The API server is started using **FastAPI with Uvicorn**, which serves the NL2SQL system and handles user queries.

---

### Command to Start

```bash
uvicorn main:app --port 8000
```

---

### What This Does
* Starts the FastAPI application defined in `main.py`
* Loads the Vanna agent from `vanna_setup.py`
* Connects to the SQLite database (`clinic.db`)
* Exposes API endpoints such as:

  * `/chat` → for asking questions
  * `/health` → for checking system status
### Access the API
After starting the server, open:

```
http://localhost:8000/docs
```

This opens the **Swagger UI**, where you can test the API interactively.

### Requirements Before Starting

Make sure:

* Database is created (`setup_database.py`)
* Memory is seeded (`seed_memory.py`)
* `.env` file contains a valid `GOOGLE_API_KEY`

### Purpose

This step runs the backend server that connects the user, AI agent, and database, enabling natural language queries to be processed and executed.

##  API Documentation

The API is built using FastAPI and provides endpoints to interact with the NL2SQL system.

---

### 1. POST `/chat`

This is the main endpoint used to ask questions in natural language.

#### Request Body

```json
{
  "question": "How many patients do we have?"
}
```

---

#### Response Example

```json
{
  "message": "The total number of patients is 200.",
  "sql_query": "SELECT COUNT(*) as total_patients FROM patients",
  "columns": ["total_patients"],
  "rows": [[200]],
  "row_count": 1,
  "chart": null,
  "chart_type": null,
  "error": null
}
```

---

#### What Happens Internally

1. User sends a question
2. Vanna agent converts it into SQL
3. SQL is extracted and validated
4. Query is executed on SQLite
5. Results (and optional chart) are returned

---

### 2. GET `/health`

Used to check if the system is running correctly.

#### Response Example

```json
{
  "status": "ok",
  "database": "connected",
  "agent_memory_items": 18,
  "timestamp": "2026-04-07T10:30:00"
}
```

---

### 3. GET `/status`

Provides system-level details.

#### Response Example

```json
{
  "timestamp": "2026-04-07T10:30:00",
  "database": {
    "path": "clinic.db",
    "connected": true
  },
  "agent": {
    "memory_items": 18
  },
  "api": {
    "version": "1.0.0"
  }
}
```

##  Architecture Overview

The system follows a simple pipeline architecture combining AI, backend API, and database.

---

### Flow

```text
User → FastAPI (main.py) → Vanna Agent → SQLite DB → Response
```

---

### Components

* **FastAPI (`main.py`)**
  Handles API requests and responses

* **Vanna Agent (`vanna_setup.py`)**
  Converts natural language into SQL using Gemini LLM

* **Agent Memory (`seed_memory.py`)**
  Stores learned question–SQL pairs for better accuracy

* **SQLite Database (`clinic.db`)**
  Stores structured clinic data

---

### LLM Used

* Google Gemini (`gemini-2.5-flash`)
* Chosen because it is:

  * fast
  * cost-effective
  * good for structured tasks like SQL generation

---

###  Key Features

* Natural Language → SQL conversion
* SQL validation and correction
* Data visualization using Plotly
* Memory-based learning for improved accuracy
### Design Choice (main.py)

In this project, **Option B (manual SQL extraction and execution)** was implemented.

Instead of directly executing SQL through the Vanna agent, the system:

* extracts SQL from the model response
* validates it for safety (only SELECT allowed)
* applies minor corrections
* executes it on the database

#### Why this approach?

This approach provides:

* better control over SQL execution
* improved security (prevents unsafe queries)
* ability to handle errors and fix common issues

This makes the system more reliable compared to directly executing model-generated queries.
## Output

The system returns structured responses for each user query, including:

* Generated SQL query
* Query results (rows and columns)
* Row count
* Optional visualization (chart)

---

###  Example

**Input:**

```json id="j3k9ds"
{
  "question": "How many patients do we have?"
}
```

**Output:**

```json id="l9d2ka"
{
  "message": "The total number of patients is 200.",
  "sql_query": "SELECT COUNT(*) as total_patients FROM patients",
  "columns": ["total_patients"],
  "rows": [[200]],
  "row_count": 1,
  "chart": null,
  "chart_type": null,
  "error": null
}
```

---

### Visualization

For queries returning multiple rows, the system automatically generates:

* Bar charts (for 2 columns)
* Line charts (for trends)

using Plotly.

---

### Key Points

* Responses are JSON-based and easy to integrate with frontend
* SQL query is included for transparency
* Errors are returned clearly if execution fails
##  Future Improvements

Although the system works end-to-end, there are several areas for improvement:

---

### 1. Better Schema Awareness

* Provide schema context directly to the LLM
* Reduce errors like wrong table/column names

---

### 2. Improve Memory Seeding

* Add more complex examples (joins, aggregations, time-based queries)
* Increase accuracy for advanced queries

---

### 3. SQL Dialect Handling

* Ensure all queries are compatible with SQLite
* Replace unsupported functions automatically

---

### 4. Frontend Integration

* Build a UI for user interaction
* Display tables and charts visually

---

### 5. Authentication & Multi-user Support

* Add user login system
* Store user-specific query history

---

### 6. Advanced Error Handling

* Suggest corrections when SQL fails
* Provide user-friendly explanations

---

### 7. Performance Optimization

* Cache frequently used queries
* Optimize large dataset handling

---

### Goal

These improvements aim to make the system more accurate, scalable, and production-ready.

