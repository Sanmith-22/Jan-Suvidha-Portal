# Jan Suvidha Portal

Jan Suvidha Portal helps citizens discover government welfare schemes that may match their personal and household circumstances. It provides a guided registration and questionnaire flow, rule-based eligibility matching, document upload support, and an administrator dashboard for monitoring usage and sending application reminders.

The application is designed as a local development and demonstration project. It works without paid third-party services: AI assistance falls back to a static question flow, and SMS delivery is simulated when no provider key is configured.

## Features

- Citizen registration with OTP verification and a demo fallback when SMS is not configured
- Guided eligibility questionnaire with rule-based scheme recommendations
- AI-assisted questions and simplified scheme information when Gemini is enabled
- Browser voice interaction support for a more accessible citizen journey
- Document upload and verification status handling
- Regional location lookup and multilingual interface support
- Scheme results with eligibility and benefit-probability information
- Separate administrator portal with analytics, SMS-log access, and reminder actions
- Rule-based reminder service for citizens who are eligible but have not applied

## Technology

| Area | Tools |
| --- | --- |
| Web application | Python, Django 4.2 |
| AI microservice | Flask, Google Generative AI |
| Front end | HTML, CSS, JavaScript |
| Main local database | SQLite |
| Reminder/SMS log storage | MongoDB (optional for local demo) |
| SMS provider | Fast2SMS (optional) |

## Application flow

1. A citizen registers and verifies their mobile number.
2. The citizen completes a guided questionnaire about relevant eligibility criteria.
3. The rule engine evaluates the answers and returns matching schemes.
4. The citizen can review scheme results and upload supporting documents.
5. Administrators can review activity, see analytics, and trigger pending-application reminders.

## Project structure

```text
.
├── ai_service/                 # Flask service for Gemini-powered assistance
├── core/                       # Django app: views, rules, SMS, reminders, URLs
│   └── management/commands/    # Reminder command
├── jan_suvidha/                # Django project configuration
├── static/                     # Styles and browser-side JavaScript
├── templates/                  # Citizen and administrator pages
├── .env.example                # Optional external-service configuration template
├── manage.py                   # Django command entry point
├── requirements.txt
└── seed_data.py                # Development data seeding utility
```

## Requirements

- Python 3.10 or later
- `pip`
- MongoDB only if you want persistent SMS/reminder logs
- A Gemini API key only for generative AI features
- A Fast2SMS API key only for real OTP and reminder SMS delivery

## Local setup

Clone the repository and enter its folder:

```bash
git clone https://github.com/Sanmith-22/Jan-Suvidha-Portal.git
cd Jan-Suvidha-Portal
```

Create and activate a virtual environment:

```bash
python -m venv venv
# Windows PowerShell
.\\venv\\Scripts\\Activate.ps1
# macOS/Linux
source venv/bin/activate
```

Install dependencies and prepare the database:

```bash
pip install -r requirements.txt
python manage.py migrate
```

Optionally add demonstration data:

```bash
python seed_data.py
```

Start the Django portal:

```bash
python manage.py runserver
```

Open <http://127.0.0.1:8000/> in a browser.

### Run the AI service

The AI service runs separately and is expected at `http://127.0.0.1:5000` by default. Start it in a second terminal after activating the same virtual environment:

```bash
cd ai_service
python app.py
```

Confirm it is available at <http://127.0.0.1:5000/health>.

## Environment configuration

Copy the example file before adding optional credentials:

```bash
copy .env.example .env
```

On macOS/Linux, use `cp .env.example .env` instead. Keep `.env` private; it is intentionally ignored by Git.

| Variable | Default | Purpose | Required? |
| --- | --- | --- | --- |
| `GEMINI_API_KEY` | Empty | Enables dynamic questions and plain-language simplification through Gemini | No |
| `MONGO_URI` | `mongodb://localhost:27017/` | MongoDB connection used by reminder/SMS logging | No for the basic portal |
| `AI_SERVICE_URL` | `http://localhost:5000` | Address of the local Flask AI service | No; use the default when running locally |
| `FAST2SMS_API_KEY` | Empty | Sends real OTP and reminder SMS through Fast2SMS | No; SMS is simulated without it |

## Routes and APIs

### Citizen pages

| Route | Description |
| --- | --- |
| `/` | Landing page |
| `/register/` | Registration and OTP flow |
| `/questionnaire/` | Eligibility questionnaire |
| `/results/` | Recommended schemes and results |
| `/documents/<scheme_id>/` | Document submission for a scheme |

### Citizen API endpoints

| Route | Purpose |
| --- | --- |
| `/api/register/` | Register a citizen |
| `/api/send-otp/` | Request an OTP |
| `/api/verify-otp/` | Verify an OTP |
| `/api/check-eligibility/` | Evaluate questionnaire responses |
| `/api/upload-document/` | Upload supporting documents |
| `/api/locations/` | Retrieve supported locations |
| `/api/ask-question/` | Get the next guided or AI-assisted question |
| `/api/schemes/` | Retrieve available schemes |
| `/api/switch-language/` | Change the selected language |

### Administrator routes

| Route | Purpose |
| --- | --- |
| `/admin-portal/login/` | Administrator sign-in |
| `/admin-portal/` | Dashboard |
| `/api/admin/analytics/` | Dashboard analytics |
| `/api/admin/send-reminder/` | Trigger pending-application reminders |
| `/api/admin/sms-logs/` | Retrieve SMS reminder logs |
| `/django-admin/` | Standard Django administration site |

### AI service endpoints

| Route | Method | Purpose |
| --- | --- | --- |
| `/health` | `GET` | Service availability check |
| `/ask` | `POST` | Generate the next guided question from current responses |
| `/simplify` | `POST` | Simplify complex scheme text |

## Reminder command

Preview reminders without sending them:

```bash
python manage.py send_reminders --dry-run
```

Run the reminder job:

```bash
python manage.py send_reminders
```

Without `FAST2SMS_API_KEY`, the application simulates delivery rather than sending a real message.

## Development notes

- The eligibility engine is deterministic and rule-based; it does not require an AI key to return scheme matches.
- Gemini features are optional and gracefully fall back when the service or key is unavailable.
- The current administrator credentials are defined in source for development. Replace this approach with Django users, hashed passwords, and role-based authorization before deployment.
- SQLite is suitable for local development. Use a managed relational database and proper media storage for production.
- Never commit `.env`, virtual environments, SQLite files, logs, or uploaded media. The included `.gitignore` covers these local artifacts.

## Future improvements

- Integrate verified government scheme data sources
- Add production-grade user authentication and role management
- Introduce automated document-verification workflows
- Add test coverage for eligibility rules and API endpoints
- Deploy with a production WSGI server, secure configuration, and managed data services
- Build a mobile-friendly or native mobile client

## Contributing

1. Create a branch from `main`.
2. Make focused changes and test them locally.
3. Do not add credentials or generated local files to commits.
4. Open a pull request describing the change and verification performed.

## License

No license is currently specified. Add a license file before distributing or reusing this project outside the repository owner’s intended terms.
