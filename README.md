# GigaAcademy Mentees Tracker

A command-line tool that manages GigaAcademy mentees and their assessment scores, built with Python and PostgreSQL running in Docker.

## Prerequisites

- Docker Desktop — `docker --version`
- Python 3.11 or newer — `python --version`
- Git Bash (Windows) — for running `tasks.sh` commands

## Setup

```bash
# 1. Clone the repo and enter the folder
git clone https://github.com/drenxhyliqi/giga-mentees-curd.git
cd giga-mentees-crud

# 2. Copy the env file
cp .env.example .env

# 3. Create virtual environment and install dependencies
python -m venv .venv
.venv\Scripts\activate        # Windows
pip install -r requirements.txt

# 4. Start the database
docker compose up -d

# 5. Seed the database with sample data (run from Git Bash)
bash tasks.sh seed
```

## How to run

### Interactive menu (Level 1 style)
```bash
python app.py
```

### CLI commands (Level 2 — argparse)

```bash
# Mentee management
python app.py mentee list
python app.py mentee add --name "John Doe" --email john@email.com --cohort B5
python app.py mentee update --id 1 --cohort B6
python app.py mentee delete --id 1

# Reports
python app.py report averages
python app.py report below --threshold 85
python app.py report assessments
```

### Tasks shortcut (Git Bash)

```bash
bash tasks.sh up      # start the database
bash tasks.sh down    # stop the database
bash tasks.sh seed    # load sample data
bash tasks.sh test    # quick connection test
```

## Testing the app

Once setup is complete, run through these commands to verify everything works:

```bash
# 1. Add three mentees
python app.py mentee add --name "John Doe" --email john@test.com --cohort B5
python app.py mentee add --name "Filan Fisteku" --email filan@test.com --cohort B5
python app.py mentee add --name "Jane Doe" --email jane@test.com --cohort B5

# 2. List all mentees — should show 3 rows sorted by name
python app.py mentee list

# 3. Update a cohort
python app.py mentee update --id 1 --cohort B6

# 4. Delete a mentee
python app.py mentee delete --id 1

# 5. Try adding a duplicate email — should show a friendly error
python app.py mentee add --name "Test" --email filan@test.com --cohort B5

# 6. Try updating an ID that doesn't exist — should show a friendly error
python app.py mentee update --id 9999 --cohort B6

# 7. Reports (requires seed data with assessment scores)
python app.py report averages
python app.py report below --threshold 85
python app.py report assessments

# 8. Interactive menu
python app.py
```

## Stopping / tearing down

```bash
docker compose down        # stop container, keep data
docker compose down -v     # stop and wipe all data (fresh start)
```

## Level completed

**Level 1 + Level 2** — all parts completed including:
- Full CRUD for mentees
- Three report queries with JOINs and aggregations
- Transaction-safe assessment recording (Part B)
- Argparse CLI with subcommands (Part C)
- Code split into `db.py`, `queries.py`, `cli.py`, `app.py`
- Error handling for `UniqueViolation`, `ForeignKeyViolation`, and invalid input

## How I used AI

Part C was the part where I used Claude AI the most. Even though I used AI throughout this assignment, I did not just copy and paste the code from Claude to VS Code — I tried to learn the logic behind it because understanding how these things work was really important to me. Below you can also see some of the prompts I used to navigate through this assignment. This assignment helped me more than the actual Python course, which was mostly syntax-based and not real-world application. It helped me understand how Docker works, how Python connects to a database, and how to write CRUD functions with SQL queries — concepts we also touched on in the SQL course.

### Prompts I used

> "Before moving forward, I want to manually change the database credentials so I can understand how the configuration flows from `.env` to Docker to PostgreSQL

> "What is the actual reason we're using Docker here instead of just installing PostgreSQL directly on the machine?"

> "What happens if I use an LEFT JOIN here instead of a INNER JOIN?

> "Having all the user input logic inside `elif` blocks in `run_menu()` doesn't feel clean. Is there a better way to structure this without changing the core CRUD functions?"

> "Instead of putting all the input handling inside `run_menu()`, would it make more sense to extract each case into its own function? What's the tradeoff between keeping it flat versus splitting it out?"

> "Why are we testing individual functions with `python -c` instead of just running `python app.py`? Is this a standard practice or just a workaround because `run_menu()` isn't implemented yet?"

## What was hard

The argparse CLI (Part C) was the most difficult part. I had never worked with subparsers before — not in my Python course and not at university — so understanding how to wire subcommands to functions took a lot of trial and error. The `lambda args:` pattern was confusing at first because I kept forgetting the parentheses when calling functions, which caused the function to be referenced but never actually called. Connecting Python to a real database and writing the CRUD functions was surprisingly straightforward once I understood how `get_connection()`, cursors, and parameterised queries work together. Docker was completely new to me but once I understood that it just runs PostgreSQL in an isolated container without installing anything permanently, it made a lot of sense.
