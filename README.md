# Subreply

![Subreply Screenshot](screenshot.png)

**Subreply** is a lightweight social networking platform designed around simplicity, performance, and maintainable backend architecture.

It provides essential social networking features while keeping the backend implementation lean, with a strong focus on efficient database queries, PostgreSQL connection pooling, and low request overhead.

🌐 **Live Demo:** https://subreply.com

## Features

- **User Management** — Account registration, authentication, profile management, and account recovery.
- **Social Feed** — Create posts, browse feeds, and discover trending discussions.
- **Threaded Discussions** — Reply to posts, maintain conversation threads, and navigate discussion history.
- **Social Connections** — Follow users and explore member profiles.
- **Direct Messaging** — Private user-to-user messaging.
- **Content Discovery** — Hashtags, mentions, and user directory.
- **Bookmarks** — Save posts for later access.
- **Account Management** — Update personal information, export account data, and delete accounts.

## Technology Stack

| Component | Technology |
|---|---|
| Language | Python |
| Web Framework | Falcon |
| ORM | Django ORM |
| Application Server | Gunicorn |
| Database | PostgreSQL |
| Connection Pooling | PgBouncer |
| Template Engine | Jinja2 |
| Security | Cryptography / Fernet |
| Database Driver | psycopg2 |

## Architecture

Subreply uses a deliberately lightweight synchronous architecture:

```text
                 Client
                   |
                   v
              Gunicorn
                   |
                   v
                Falcon
                   |
          Application Layer
                   |
                   v
               Django ORM
                   |
                   v
               PgBouncer
                :6432
                   |
                   v
              PostgreSQL
```

### Architecture Decisions

**1. Falcon + Gunicorn**

- Lightweight WSGI application architecture.
- Minimal framework overhead.
- Process-based worker isolation.
- Suitable for synchronous request processing.

**2. Django ORM**

- Leverages mature ORM capabilities without requiring the Django web framework.
- Uses advanced query expressions such as `OuterRef`, `Subquery`, and `ArraySubquery`.
- Supports complex relational queries and query composition.
- Reduces unnecessary database round trips.

**3. PostgreSQL + PgBouncer**

- PostgreSQL provides reliable relational data storage.
- PgBouncer manages database connection pooling.
- Reduces connection management overhead.
- Avoids the need to introduce asynchronous database drivers solely for connection handling.

**4. Synchronous Processing**

CPU-intensive operations such as PBKDF2 password hashing, template rendering, and DNS MX lookups execute within Gunicorn workers.

This approach avoids unnecessary asynchronous complexity and keeps request processing straightforward.

## Performance Philosophy

Subreply follows three engineering principles:

- **Simplicity:** Keep the codebase easy to read, understand, and modify.
- **Efficiency:** Minimize database round trips and unnecessary processing.
- **Maintainability:** Avoid unnecessary abstractions and dependencies.

The project targets a request processing time of 50ms or less as an engineering goal, rather than a guaranteed benchmark.

## Getting Started

### Prerequisites

- Python 3
- PostgreSQL
- PgBouncer

### 1. Clone the Repository

```bash
git clone https://github.com/xiaoyaocai/subreply.git
cd subreply
```

### 2. Configure PostgreSQL and PgBouncer

Create the application database:

```bash
createdb subreply
```

Configure PgBouncer to listen on port `6432` and connect to the PostgreSQL database.

Ensure the database credentials and connection settings match the application configuration.

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure Application Secrets

Create `project/local.py`:

```python
from cryptography.fernet import Fernet

SIGNATURE = Fernet.generate_key()
```

Keep the generated secret private and do not commit production secrets to version control.

### 5. Apply Database Migrations

```bash
python manage.py migrate
```

### 6. Start the Application

```bash
gunicorn main:app
```

The application will be available at:

http://localhost:8000

## Project Structure

```text
subreply/
├── app/
│   ├── api.py
│   ├── models.py
│   ├── resources.py
│   ├── serializers.py
│   ├── validation.py
│   ├── forms.py
│   ├── hooks.py
│   ├── filters.py
│   └── utils.py
├── project/
├── static/
├── templates/
├── tests/
├── main.py
├── manage.py
├── requirements.txt
├── gunicorn.conf.py
└── README.md
```

## Development Philosophy

Subreply is built around a minimalistic engineering philosophy:

- Readable code over unnecessary abstraction.
- Efficient SQL over excessive database queries.
- Practical architecture over technology complexity.
- Maintainable implementation over premature optimization.

## License

See [LICENSE](LICENSE) for details.

## Acknowledgements

This repository is a fork of [lucianmarin/subreply](https://github.com/lucianmarin/subreply).

The original project provides the foundation for this implementation. Please refer to the original repository for its upstream history and contributions.
