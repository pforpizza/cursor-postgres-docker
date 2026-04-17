Docker is a tool that lets you package an application (like PostgreSQL) together with everything it needs to run into a lightweight bundle called a container. A container is like a self‑contained mini‑computer that always has the right versions of libraries and settings, so your app runs the same way on any machine.


You need
Docker Desktop to create the container.
Cursor IDE (specfically to work with integrated AI)
I Needed Python 13 --- As 11 and 14 did not work with Postgres
You use Postgres but you don't need that installed it exists within our Docker container. 

---------------

We use the python package uv -  it is like pip but much faster - run the install as administrator
The code to install uv package: iwr https://astral.sh/uv/install.ps1 -UseBasicParsing | iex

iwr (Invoke-WebRequest) downloads the uv installer script from astral.sh
| (pipe) passes it to the next command
iex (Invoke-Expression) executes it. It's like downloading a file and running it in one step.

uv --version to verify installation

---------------

initialise a new project within the folder

uv init

--------------

add MCP servers to Cursor by selecting the settings button then tools & MCP

{
  "mcpServers": {
    "docker": {
      "command": "uv",
      "args": ["run", "--with", "docker-mcp", "docker-mcp", "--access-mode=unrestricted"],
      "env": { "DOCKER_HOST": "npipe:////./pipe/docker_engine" }
    },
    "postgres": {
      "command": "uv",
      "args": ["run", "--with", "postgres-mcp", "postgres-mcp", "--access-mode=unrestricted"],
      "env": { "DATABASE_URI": "postgresql://app:app@localhost:5432/demo" }
    }
  }
}

I think google what you need for each MCP Server?

----------------

Restart Cursor for these changes to take effect

---------------

Create PostgreSQL container and database by asking cursor AI

Using the Docker MCP, create a PostgreSQL 16 container named
pg-local on port 5432 with user/password 'app' and database 'demo'

postgres:16 is the version of PostgreSQL

An image is like a template for a container – it's a pre‑packaged recipe that says "here's how to run PostgreSQL with all the right files and settings." Docker uses this PostgreSQL 16 image to create the actual running database.

A container named pg-local is the running PostgreSQL database created from that image. Giving it a name just makes it easier to find and manage later.

Mapping port 5432 means "open door number 5432 on your computer and connect it to the database inside Docker." PostgreSQL is configured to listen on port 5432, which just means it watches that door for incoming requests. When tools outside Docker (like MCP, psql, or apps) want to talk to the database, they send network traffic to localhost:5432, and Docker passes it through that door into the container.

The username and password are like login details for your database. 

------------------

Configure the database users and schemas

Using the Postgres MCP, configure the database user named 'app' with 
password 'app', grant it all permissions on the demo database, and 
create a schema called 'app' owned by this user


-------------------

Load the data into the database

in terminal window of IDE

Get-Content demo_data.sql | docker exec -i pg-local psql -U app -d demo


| (pipe) passes it to the next command
executes the SQL commands inside the container as the app user in the demo database.

------------------

Now you can start asking the AI agent to perform queries, update & improve archetecituer and more. 

Example Queries.

Visualise the database schema as tables and connections in a mermaid diagram format. 
What is a product that has not sold. 
All customers begining with "A".

For optimisation something like: 

Using PostgreSQL MCP, audit my database (app schema: customers, orders, order_items, products).

Run these checks:
1. EXPLAIN ANALYZE on a join query between orders and customers - show execution time and scan types
2. Missing indexes - check which tables lack indexes on foreign keys
3. Cache hit rate - query pg_stat_database

Present findings in a table format with columns: Issue | Impact | Priority

Then provide:
- Top 3 optimization recommendations with exact SQL to implement

Keep responses concise but include key metrics (execution times, cache hit %, row counts).

----------------------

Remove container

Using the Docker MCP:
Stop the pg-local container
Remove the pg-local container


