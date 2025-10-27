## ✅ Step 1: Install Necessary Libraries

First, you need two key Python libraries:

    SQLAlchemy: The Object-Relational Mapper (ORM) that lets you interact with the database using Python objects.

    psycopg2-binary: The database driver that allows SQLAlchemy to communicate specifically with PostgreSQL.

You can add these to your requirements.txt file and install them:
Bash

# Add these lines to your requirements.txt
SQLAlchemy
psycopg2-binary

# Then run the installation
pip install -r requirements.txt

## 🔑 Step 2: Configure the Database URL

You should never hardcode database credentials in your source code. We'll store them in a .env file and load them using Pydantic's settings management.

    Create a .env file in the root of your backend/ directory:

    # backend/.env

    # Format: postgresql://<user>:<password>@<host>:<port>/<database_name>
    DATABASE_URL="postgresql://postgres:mysecretpassword@localhost:5432/datn_db"

        Note: Replace the username, password, and database name with your actual PostgreSQL settings.

    Update app/core/config.py to load this variable:
    Python

    # app/core/config.py

    from pydantic_settings import BaseSettings, SettingsConfigDict

    class Settings(BaseSettings):
        # This tells Pydantic to load variables from a .env file
        model_config = SettingsConfigDict(env_file=".env")

        DATABASE_URL: str

    settings = Settings()

## 🔗 Step 3: Create the SQLAlchemy Engine

The engine is the central component that connects SQLAlchemy to your database. We'll set this up in app/core/database.py.
Python

# app/core/database.py

from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

from .config import settings # Import settings from config.py

# Create the SQLAlchemy engine using the DATABASE_URL from our settings
engine = create_engine(settings.DATABASE_URL)

# Each instance of SessionLocal will be a new database session.
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

# Base class that our ORM models will inherit from.
Base = declarative_base()

