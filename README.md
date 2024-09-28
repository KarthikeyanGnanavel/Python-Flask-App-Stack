You are tasked with deploying a multi-container application using Docker and Docker Compose. The application consists of the following components:
A Flask web application that acts as the frontend.
A PostgreSQL database that stores user data.
A Redis caching service that improves the performance of the Flask application.
Task-1: Create docker containers for all these components manually using docker run command and make the stack working into your local workstation/laptop/VM

Task-2: On a different host (VM), deploy the stack using docker compose. You should reuse the images you will be creating in task-1

Requirements:

Flask Application:

The Flask app should connect to the PostgreSQL database to retrieve user data.
The Flask app should also use Redis to cache frequently accessed data.
PostgreSQL Database:

It should run on port 5432.
Use a Docker image (postgres:13).
Use environment variables for setting up the database (POSTGRES_USER, POSTGRES_PASSWORD, POSTGRES_DB).
Redis Cache:

Use a Docker image (redis:latest).
Docker Compose:

The Flask container should depend on both the PostgreSQL and Redis services.
Use Docker volumes to persist PostgreSQL data in /var/lib/postgresql/data directory
Configure network communication between the three services.


Flask Application Code:

from flask import Flask, jsonify
import psycopg2
import redis

app = Flask(__name__)

# Connect to Redis
cache = redis.Redis(host='redis', port=6379)

# Connect to PostgreSQL
def get_db_connection():
    conn = psycopg2.connect(host="db",
                            database="mydb",
                            user="postgres",
                            password="password")
    return conn

@app.route('/')
def hello():
    return "Hello, Docker Multi-Container!"

@app.route('/users')
def get_users():
    conn = get_db_connection()
    cur = conn.cursor()
    cur.execute("SELECT * FROM users;")
    users = cur.fetchall()
    cur.close()
    conn.close()

    return jsonify(users)

@app.route('/cache')
def cache_example():
    if cache.get('key'):
        return f"Cache Hit: {cache.get('key')}"
    else:
        cache.set('key', 'Hello from Redis Cache!')
        return "Cache Miss: Key Set"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)

Flask Application packages (sample requirements.txt):
Flask==2.0.1
psycopg2-binary==2.9.1
redis==3.5.3
Werkzeug==2.2.2


## Deliverables:

Create a Word doc or PDF to include all your Dockerfiles, docker compose file and the commands and their outputs (snapshots) you used to achieve both parts and submit that file.
