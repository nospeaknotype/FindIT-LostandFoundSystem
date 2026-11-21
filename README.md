# FindIT-LostandFoundSystem

# FindIt Lost and Found System

FindIt is a campus lost-and-found web application for the Student Welfare and Formation Office (SWAFO) at De La Salle University–Dasmariñas. It replaces a paper logbook with a searchable inventory where students and employees can report lost or found items, review possible matches, submit claims, and communicate with the office.

## Features

- Public browsing and search for open found-item records
- Lost-item and found-item reporting with descriptions, categories, locations, dates, photos, brands, and private identifying details
- Account registration, login, profile settings, and report history
- Claim submission with ownership evidence for staff review
- In-app messaging between users and administrators
- Admin dashboards for reports, found items, claims, users, messages, settings, and analytics
- Match suggestions that combine NLP text similarity with item category, campus location, and date signals
- Campus-aware location aliases such as CBAA, GMH, MAH, and library names

## Technology

- Python 3.9+
- Flask
- Flask-SQLAlchemy and SQLAlchemy
- SQLite
- HTML, CSS, and vanilla JavaScript
- Werkzeug password hashing
- Standard-library NLP algorithms

## NLP matching pipeline

FindIt includes a transparent, rule-based matching pipeline in `backend/algorithms/`.

1. Text is normalized, lowercased, tokenized, and lightly stemmed.
2. Common English and Filipino stop words are filtered from lexical features.
3. Gazetteer-based entity recognition identifies item types, brands, colors, materials, and campus locations.
4. Bounded Levenshtein distance helps correct selected spelling errors in recognized entities.
5. TF-IDF vectors and cosine similarity compare report text.
6. The final ranking combines text similarity with location, category, and date signals.
7. Public browse search uses the project’s Boyer–Moore string-search implementation.

The match score is a ranking aid for staff review. It is not a probability of ownership, and an approved claim still requires verification.

## Project structure

```text
backend/
  app.py                       Flask application, models, routes, and APIs
  local_startup.py             Local environment setup and startup checks
  create_admin.py              Secure first-admin creation script
  algorithms/
    textutils.py               Tokenization, normalization, stemming, stop words
    ner.py                     Entity and campus-location recognition
    tfidf.py                   TF-IDF indexing and report matching
    levenshtein_distance.py    Bounded edit distance and typo correction
    boyer_moore.py             Exact string search
templates/                     Public and admin HTML views
static/css/                    Public stylesheets
static/js/                     Public page behavior and API clients
static/admin/                  Admin stylesheets and JavaScript
instance/                      Local SQLite database and runtime data
```

## Run locally on Windows

Install Python 3.9 or later, then run the application from the project root:

```powershell
cd backend
py local_startup.py
```

The startup helper creates `backend/.venv` when needed, installs the packages in `backend/requirements.txt`, checks that the project can write to `instance/` and `static/uploads/`, and starts the server. Open the URL printed in the terminal. The default address is `http://127.0.0.1:5000`.

To choose a port explicitly:

```powershell
py local_startup.py --port 5001
```

You can also run the Flask app directly after installing the dependencies:

```powershell
cd backend
py app.py
```

On macOS or Linux, use `python3 local_startup.py` from `backend`.

## Create an administrator account

Set the required values in your shell before running the script. Do not commit real credentials or secret keys.

```powershell
$env:ADMIN_NAME = "SWAFO Administrator"
$env:ADMIN_SCHOOL_ID = "ADMIN-001"
$env:ADMIN_EMAIL = "admin@example.com"
$env:ADMIN_PASSWORD = "use-a-strong-password"

cd backend
py create_admin.py
```

For a deployed environment, set a strong `SECRET_KEY`. The development fallback is intentionally unsuitable for public hosting.

## Data and privacy

The SQLite database and uploaded photos are local runtime data. Keep `instance/database.db` and `static/uploads/` out of public repositories and backups that do not need them. Public item fields are separated from private identifying details so staff can verify ownership without exposing every detail in the browse view.

Before deploying, review authentication, session configuration, upload limits, email settings, database backups, and access controls for the environment where the system will run.

## Development notes

- Keep the category list in `static/js/item-store.js` aligned with the backend category definitions.
- Keep the campus location vocabulary in `static/js/data/locations.js` aligned with the backend entity recognizer.
- Do not use claim evidence, private messages, or personal identifiers as public search content.
- When changing matching behavior, test both true matches and reports with no valid match.

## License

No license has been included yet. Add a license file before publishing the repository for reuse by others.
