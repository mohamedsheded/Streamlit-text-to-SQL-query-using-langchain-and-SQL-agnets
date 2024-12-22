# LangChain SQL Chat Application

This application allows users to interact with a SQL database using natural language queries powered by LangChain and a Groq LLM model. It supports both SQLite and MySQL databases and uses Streamlit as the web interface.

---

## Features

- **Natural Language to SQL Query Conversion:** Allows users to query databases using plain English.
- **Multiple Database Support:** Works with LOCALDB and YOURDB just provide the host, user, password , db_name.
- **Streamlit-Based Web UI:** Provides an intuitive chat interface.
- **Groq LLM Integration:** Utilizes the Llama3-8b-8192 model for natural language processing.
- **Session-Based Chat History:** Retains previous queries and responses.

---

## Prerequisites

- Python 3.8 or later.
- pip (Python package installer).
- Access to a SQLite or MySQL database.
- Groq API Key for LLM processing.

---

## Installation

1. **Clone the Repository:**
```bash
$ git clone <repository-url>
$ cd <repository-folder>
```

2. **Install Dependencies:**
```bash
$ pip install -r requirements.txt
```

3. **Set Up the Database:**
- For SQLite, place your `student.db` file in the root directory.
- For MySQL, ensure the database is accessible and note down the credentials.

---

## Configuration

1. Start the Streamlit application:
```bash
$ streamlit run app.py
```

2. Choose a database option in the sidebar:
   - SQLite (default)
   - MySQL

3. For MySQL, provide:
   - Host
   - Username
   - Password
   - Database Name

4. Enter the Groq API Key in the sidebar.

---

## Usage

1. Select a database from the sidebar.
2. Enter database connection details (if using MySQL).
3. Enter your Groq API key.
4. Use the chat input to query the database.
5. View responses in the chat window.

---

## Code Explanation

### 1. Import Libraries
```python
import streamlit as st
from pathlib import Path
from langchain.agents import create_sql_agent
from langchain.sql_database import SQLDatabase
from langchain.agents.agent_types import AgentType
from langchain.callbacks import StreamlitCallbackHandler
from langchain.agents.agent_toolkits import SQLDatabaseToolkit
from sqlalchemy import create_engine
import sqlite3
from langchain_groq import ChatGroq
```
- **streamlit**: Creates the web interface.
- **langchain**: Provides tools for building SQL agents.
- **sqlalchemy**: Handles database connections.
- **sqlite3**: Manages SQLite database interactions.
- **ChatGroq**: Integrates the Groq LLM model.

### 2. Configure the Application
```python
st.set_page_config(page_title="LangChain: Chat with SQL DB", page_icon="🦜")
st.title("🦜 LangChain: Chat with SQL DB")
```
- Sets up the page title and icon.

### 3. Sidebar for Input
```python
radio_opt=["Use SQLLite 3 Database- Student.db","Connect to you MySQL Database"]
selected_opt=st.sidebar.radio(label="Choose the DB which you want to chat",options=radio_opt)
```
- Allows users to select the database type (SQLite or MySQL).

### 4. API Key Input
```python
api_key=st.sidebar.text_input(label="GRoq API Key",type="password")
```
- Takes the Groq API key for accessing the LLM model.

### 5. Configure Database Connection
```python
@st.cache_resource(ttl="2h")
def configure_db(db_uri,mysql_host=None,mysql_user=None,mysql_password=None,mysql_db=None):
```
- Caches database configuration to reduce reload times.
- Supports SQLite and MySQL connections.

### 6. Create SQL Agent
```python
agent=create_sql_agent(
    llm=llm,
    toolkit=toolkit,
    verbose=True,
    agent_type=AgentType.ZERO_SHOT_REACT_DESCRIPTION
)
```
- Configures a SQL agent using the Groq LLM model.
- Uses zero-shot learning for query generation.

### 7. Chat Interface
```python
if "messages" not in st.session_state or st.sidebar.button("Clear message history"):
    st.session_state["messages"] = [{"role": "assistant", "content": "How can I help you?"}]
```
- Maintains chat history across sessions.

```python
user_query=st.chat_input(placeholder="Ask anything from the database")

if user_query:
    st.session_state.messages.append({"role": "user", "content": user_query})
    st.chat_message("user").write(user_query)

    with st.chat_message("assistant"):
        streamlit_callback=StreamlitCallbackHandler(st.container())
        response=agent.run(user_query,callbacks=[streamlit_callback])
        st.session_state.messages.append({"role":"assistant","content":response})
        st.write(response)
```
- Handles user queries and displays responses from the LLM model.

---

## Dependencies

Dependencies are listed in the `requirements.txt` file:

```
streamlit
langchain
sqlalchemy
sqlite3
langchain-groq
```

---

## Notes

- Ensure the database has proper permissions.
- API key security is crucial; avoid sharing it publicly.
- Extend functionality by adding more LLM models or databases as needed.

---


