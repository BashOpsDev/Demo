# Ironsail Prescription Intelligence API

[![FastAPI](https://img.shields.io/badge/FastAPI-0.109.0-009688?style=for-the-badge&logo=fastapi)](https://fastapi.tiangolo.com)
[![Python](https://img.shields.io/badge/Python-3.11+-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15+-336791?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.postgresql.org)
[![Deployed on Railway](https://img.shields.io/badge/Deployed_on-Railway-0B0D0E?style=for-the-badge&logo=railway&logoColor=white)](https://railway.app)

> **20% Production Prototype** - AI-powered drug interaction checking and prescription verification system for Ironsail Pharma

## 🚀 Live Demo

**API Documentation:** https://ironsail-demo.up.railway.app/docs

Try the endpoints directly in your browser with the interactive Swagger UI.

## 📋 Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [API Endpoints](#api-endpoints)
- [Installation](#installation)
- [Environment Variables](#environment-variables)
- [Usage Examples](#usage-examples)
- [Database Schema](#database-schema)
- [AI Integration](#ai-integration)
- [Deployment](#deployment)
- [Testing](#testing)
- [Project Structure](#project-structure)
- [Contributing](#contributing)
- [License](#license)
- [Contact](#contact)

## ✨ Features

### Core Functionality
- ✅ **Prescription Upload & Parsing** - Validate and store prescription data
- ✅ **AI-Powered Drug Interaction Checking** - Real-time interaction detection using Groq LLM
- ✅ **Patient Medication History** - Track patient prescriptions over time
- ✅ **Pharmacy Inventory System** - Check drug availability and alternatives
- ✅ **Async Architecture** - High-performance async/await with FastAPI
- ✅ **Automatic API Documentation** - OpenAPI 3.1 compliant with Swagger UI

### Security & Production
- 🔒 Input validation with Pydantic
- 🔒 SQL injection prevention
- 🔒 CORS configuration
- 🔒 Error handling and logging
- 🔒 Environment-based configuration

## 🛠️ Tech Stack
| Category | Technology |
|----------|-----------|
| **Framework** | FastAPI 0.109.0 |
| **Language** | Python 3.11+ |
| **Database** | PostgreSQL 15+ |
| **ORM** | SQLAlchemy 2.0 (async) |
| **AI/LLM** | Groq SDK (Llama2/Mixtral) |
| **Deployment** | Railway |
| **API Docs** | OpenAPI 3.1 / Swagger UI |
| **Caching** | Redis (optional) |

## 📡 API Endpoints

### Prescriptions
- `POST /api/v1/prescriptions/upload` - Upload and validate prescription
- `POST /api/v1/prescriptions/check-interactions` - Check drug interactions (AI-powered)

### Patients
- `GET /api/v1/patients/{patient_id}/medications` - Get patient medication history

### Drugs
- `GET /api/v1/drugs/{drug_name}/availability` - Check pharmacy inventory

## 🚦 Installation

### Prerequisites
- Python 3.11 or higher
- PostgreSQL 15+
- pip and virtualenv

### Local Setup

```bash
# 1. Clone the repository
git clone https://github.com/yourusername/ironsail-prescription-api.git
cd ironsail-prescription-api

# 2. Create virtual environment
python -m venv venv

# Activate on Windows
venv\Scripts\activate

# Activate on macOS/Linux
source venv/bin/activate

# 3. Install dependencies
pip install -r requirements.txt
# 4. Set up database
# Create PostgreSQL database
createdb ironsail_demo

# 5. Configure environment variables
# Copy .env.example to .env and fill in your values
cp .env.example .env

# 6. Run database migrations
python -m alembic upgrade head

# 7. Seed database (optional)
python app/database/seed.py

# 8. Run the application
uvicorn app.main:app --reload

# Server runs at http://localhost:8000
# API docs at http://localhost:8000/docs
```

## 🔐 Environment Variables

Create a `.env` file in the root directory:

```env
# Database
DATABASE_URL=postgresql+asyncpg://user:password@localhost:5453/ironsail_demo

# Groq API (Get yours at https://console.groq.com)
GROQ_API_KEY=your_groq_api_key_here

# OpenRouter (Optional fallback)
OPENROUTER_API_KEY=your_openrouter_key_here

# Application
APP_NAME="Ironsail Prescription Intelligence API"
DEBUG=true
SECRET_KEY=your_secret_key_here

# CORS
ALLOWED_ORIGINS=http://localhost:3000,https://ironsail-demo.up.railway.app

# Redis (Optional)
REDIS_URL=redis://localhost:6379
```

See `.env.example` for template.

## 📖 Usage Examples
### 1. Upload a Prescription

```bash
curl -X POST "http://localhost:8000/api/v1/prescriptions/upload" \
  -H "Content-Type: application/json" \
  -d '{
    "patient_id": 1,
    "raw_text": "Take 1 tablet daily",
    "drug_name": "Lisinopril",
    "dosage": "10mg",
    "frequency": "daily"
  }'
```

**Response:**
```json
{
  "patient_id": 1,
  "raw_text": "Take 1 tablet daily",
  "drug_name": "Lisinopril",
  "dosage": "10mg",
  "frequency": "daily",
  "id": 1,
  "status": "active",
  "prescribed_date": "2026-06-13T08:42:52.122Z"
}
```

### 2. Check Drug Interactions (AI-Powered)

```bash
curl -X POST "http://localhost:8000/api/v1/prescriptions/check-interactions" \
  -H "Content-Type: application/json" \
  -d '{
    "medications": ["Lisinopril", "Aspirin", "Metformin"]
  }'
```

**Response:**
```json
{
  "severity": "moderate",
  "description": "Lisinopril and Aspirin may have moderate interaction. Aspirin may reduce the antihypertensive effect of Lisinopril.",
  "recommendations": "Monitor blood pressure closely. Consider spacing administration times. Consult physician if symptoms occur."
}
```

### 3. Get Patient Medications
```bash
curl "http://localhost:8000/api/v1/patients/1/medications"
```

### 4. Check Drug Availability

```bash
curl "http://localhost:8000/api/v1/drugs/Lisinopril/availability"
```

## 🗄️ Database Schema

### Tables

**patients**
```sql
- id (INTEGER, PRIMARY KEY)
- name (VARCHAR)
- email (VARCHAR, UNIQUE)
- date_of_birth (DATE)
- created_at (TIMESTAMP)
```

**prescriptions**
```sql
- id (INTEGER, PRIMARY KEY)
- patient_id (INTEGER, FK -> patients.id)
- raw_text (TEXT)
- drug_name (VARCHAR)
- dosage (VARCHAR)
- frequency (VARCHAR)
- prescribed_date (TIMESTAMP)
- status (VARCHAR)
```

**drugs**
```sql
- id (INTEGER, PRIMARY KEY)
- name (VARCHAR, UNIQUE)
- generic_name (VARCHAR)
- category (VARCHAR)
- common_side_effects (TEXT)
```

**drug_interactions**
```sql
- id (INTEGER, PRIMARY KEY)
- drug1_id (INTEGER, FK -> drugs.id)
- drug2_id (INTEGER, FK -> drugs.id)
- severity (VARCHAR)- description (TEXT)
```

## 🤖 AI Integration

### Groq LLM for Drug Interactions

The system uses Groq's ultra-fast LLM inference to analyze drug combinations:

```python
from groq import Groq

client = Groq(api_key=os.getenv("GROQ_API_KEY"))

def check_interactions(medications: List[str]) -> dict:
    prompt = f"""
    You are a clinical pharmacist AI. Analyze potential drug interactions 
    between these medications: {', '.join(medications)}.
    
    Return:
    1. Severity level (critical/moderate/mild/none)
    2. Detailed description of interactions
    3. Clinical recommendations
    4. Monitoring parameters
    
    Be concise but thorough. Use medical terminology appropriately.
    """
    
    response = client.chat.completions.create(
        model="mixtral-8x7b-32768",
        messages=[{"role": "user", "content": prompt}],
        temperature=0.3,
        max_tokens=500
    )
    
    return parse_response(response.choices[0].message.content)
```

**Why Groq?**
- 10x faster than traditional LLM APIs
- Cost-effective for high-volume checks
- Mixtral 8x7B provides excellent medical reasoning

## 🚀 Deployment

### Railway (Recommended)

1. **Install Railway CLI:**
```bash
npm install -g @railway/cli```

2. **Login and Deploy:**
```bash
railway login
railway init
```

3. **Add PostgreSQL:**
```bash
railway add postgresql
```

4. **Set Environment Variables:**
```bash
railway variables set GROQ_API_KEY=your_key
```

5. **Deploy:**
```bash
railway up
```

6. **Open API Docs:**
```bash
railway open
```

Your API will be live at `https://your-project.up.railway.app/docs`

### Docker

```bash
# Build
docker build -t ironsail-api .

# Run
docker run -p 8000:8000 --env-file .env ironsail-api
```

## 🧪 Testing

```bash
# Install test dependencies
pip install pytest pytest-asyncio httpx

# Run tests
pytest

# Run with coveragepytest --cov=app --cov-report=html

# Run specific test file
pytest tests/test_prescriptions.py -v
```

## 📁 Project Structure
