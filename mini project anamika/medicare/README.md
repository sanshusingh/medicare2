# MediCare+

**MediCare+: A Smart Medicine Reminder and Personal Health Management System** is a Flask + PostgreSQL mini-project for managing medicines, dose reminders, appointments, health records, prescriptions, emergency details, and a QR emergency health card.

## Included features

- Secure registration, login, password hashing, session protection, and CSRF checks
- Database-backed medicine, appointment, report, prescription, stock, notification, and history workflows
- Browser notifications and Web Speech API voice reminders for scheduled medicine doses
- Securely validated PDF/JPG/JPEG/PNG uploads, stored separately from database metadata
- Chart.js dashboard charts using the signed-in user’s database data
- Real QR code generation containing only emergency details (never passwords)
- Demo interaction checker with an explicit medical-information disclaimer
- Responsive desktop and mobile layout with a collapsible mobile sidebar

## Technology

- Python, Flask, Flask-SQLAlchemy, psycopg
- PostgreSQL 14+
- Bootstrap 5, vanilla JavaScript, Chart.js, Bootstrap Icons
- `qrcode[pil]` for health-card QR generation

## Setup

1. Create the PostgreSQL database and schema:

   ```powershell
   createdb -U postgres medicare
   psql -U postgres -d medicare -f database.sql
   ```

2. Create and activate a virtual environment, then install packages:

   ```powershell
   py -m venv .venv
   .\.venv\Scripts\Activate.ps1
   pip install -r requirements.txt
   ```

3. The included `.env` file contains the local PostgreSQL connection settings. For a different environment, set these values in the shell instead:

   ```powershell
   $env:DATABASE_URL = 'postgresql://postgres:YOUR_PASSWORD@localhost:5432/medicare'
   $env:SECRET_KEY = 'use-a-long-random-production-secret'
   ```

4. Create tables and optional database-backed sample data:

   ```powershell
   flask --app app.py init-db
   flask --app app.py seed-demo
   ```

5. Start the application:

   ```powershell
   flask --app app.py run --debug
   ```

Open `http://127.0.0.1:5000`. The seed command creates `demo@medicare.local` with password `Demo@123` for a classroom demonstration. Change/remove this account in any non-demo deployment.

## Deploying the full-stack application

MediCare+ is a Flask server application, so it cannot be deployed as a Netlify static site. Netlify's 404 page means it did not start the Python application.

Use a Python web-service host such as Render instead:

1. Push the complete repository (including the root-level `render.yaml`) to GitHub.
2. In Render, create a **Blueprint** from the repository, or create a **Web Service** manually with:

   ```text
   Root Directory: medicare
   Build Command: pip install -r requirements.txt
   Start Command: gunicorn app:app --bind 0.0.0.0:$PORT
   ```

3. Create a hosted PostgreSQL database (for example, Render PostgreSQL) and add its external connection string as `DATABASE_URL` in the service's environment settings.
4. Add a long random `SECRET_KEY` in those environment settings. Do not upload or commit `.env`.
5. After the first successful deployment, open the service's Shell and run:

   ```bash
   flask --app app.py init-db
   flask --app app.py seed-demo
   ```

`localhost` works only on your own computer. It must not be used as the deployed `DATABASE_URL`; use the URL of the hosted PostgreSQL service instead.

### Development without PostgreSQL

PostgreSQL is the configured project database. For a quick local UI smoke test only, set `DATABASE_URL` to `sqlite:///medicare_dev.db`, then run `init-db` and `seed-demo`. Do not use SQLite if the project submission requires PostgreSQL.

## Project structure

```text
medicare/
├── app.py                 # Flask routes, models, validation, CLI commands
├── config.py              # Environment-led configuration
├── database.sql           # MySQL database definition
├── requirements.txt
├── templates/             # Landing, auth, dashboard, and feature pages
├── static/css/style.css   # Responsive health-care UI
├── static/js/             # Charts, shared UI, browser/voice reminders
└── uploads/               # Reports, prescriptions, profile images
```

## Future improvements

- Integrate a verified medicine-interaction provider and clinical review process.
- Send real SMS/email emergency alerts via a configured provider.
- Add password reset email, audit logs, and two-factor authentication.
- Add calendar export and a progressive web app/offline reminder service worker.

> Medical disclaimer: MediCare+ is an educational mini-project. Reminder and interaction features do not replace professional medical advice.
