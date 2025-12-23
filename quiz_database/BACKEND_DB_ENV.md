Backend connection expectations

The Django backend should read the following env vars to connect via its ORM:
- POSTGRES_URL=postgresql://localhost:5001/myapp
- POSTGRES_USER=appuser
- POSTGRES_PASSWORD=dbuser123
- POSTGRES_DB=myapp
- POSTGRES_PORT=5001

Recommended Django DATABASES config (pseudo):
DATABASES = {
  "default": {
    "ENGINE": "django.db.backends.postgresql",
    "NAME": os.getenv("POSTGRES_DB"),
    "USER": os.getenv("POSTGRES_USER"),
    "PASSWORD": os.getenv("POSTGRES_PASSWORD"),
    "HOST": "localhost",
    "PORT": os.getenv("POSTGRES_PORT"),
  }
}

Alternatively, parse POSTGRES_URL and override fields accordingly. If both URL and individual vars are present, prefer individual vars to avoid conflicts during port changes. Ensure POSTGRES_PORT=5001 for this environment.

Important:
- Do not hardcode DB credentials in code; rely on .env.
- Ensure network/port (5001) is reachable from the backend container.
- Apply migrations after integrating models; the schema above exists but the backend should manage future changes via migrations.
