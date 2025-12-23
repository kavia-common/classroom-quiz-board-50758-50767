# Quiz Database Schema and Connection Info

This container hosts a PostgreSQL database for the classroom quiz app.

Port alignment:
- Database listens on localhost:5001 (preview environment)
- Connection examples below use 5001

Connection string (psql):
psql postgresql://appuser:dbuser123@localhost:5001/myapp

Environment variables expected by dependent services (backend):
- POSTGRES_URL: postgresql://localhost:5001/myapp
- POSTGRES_USER: appuser
- POSTGRES_PASSWORD: dbuser123
- POSTGRES_DB: myapp
- POSTGRES_PORT: 5001

Schema (created via one-by-one psql commands):
- quizzes
  - id SERIAL PRIMARY KEY
  - title TEXT NOT NULL
  - description TEXT
  - created_at TIMESTAMPTZ DEFAULT NOW()

- questions
  - id SERIAL PRIMARY KEY
  - quiz_id INTEGER NOT NULL REFERENCES quizzes(id) ON DELETE CASCADE
  - text TEXT NOT NULL
  - answer TEXT NOT NULL
  - points INTEGER DEFAULT 1
  - order_index INTEGER DEFAULT 0

- sessions
  - id UUID PRIMARY KEY DEFAULT gen_random_uuid()
  - quiz_id INTEGER NOT NULL REFERENCES quizzes(id) ON DELETE CASCADE
  - status TEXT NOT NULL DEFAULT 'pending'
  - current_question_index INTEGER DEFAULT 0
  - started_at TIMESTAMPTZ
  - ended_at TIMESTAMPTZ

- teams
  - id SERIAL PRIMARY KEY
  - session_id UUID NOT NULL REFERENCES sessions(id) ON DELETE CASCADE
  - name TEXT NOT NULL
  - created_at TIMESTAMPTZ DEFAULT NOW()

- score_events
  - id SERIAL PRIMARY KEY
  - session_id UUID NOT NULL REFERENCES sessions(id) ON DELETE CASCADE
  - team_id INTEGER NOT NULL REFERENCES teams(id) ON DELETE CASCADE
  - question_id INTEGER REFERENCES questions(id) ON DELETE SET NULL
  - delta_points INTEGER NOT NULL
  - reason TEXT
  - created_at TIMESTAMPTZ DEFAULT NOW()

Seed data (minimal):
- 1 quiz: "General Knowledge"
- 2 questions: "What is the capital of France?" (Paris), "2 + 2 = ?" (4)
- 1 active session for the quiz
- 2 teams for the session: Team A, Team B
- 1 score event (+1 for Team A on question 1 for "Correct answer")

Notes:
- If pgcrypto is not enabled and gen_random_uuid() is unavailable, backend can store UUIDs using uuid-ossp or generate UUIDs application-side.
- Backend should source env from its .env and connect using these variables. Do not hardcode connection info in code.
