# Mental Drift: Backend Prototype (FastAPI)

## Project Structure
```
mental-drift-backend/
├── app/
│   ├── __init__.py
│   ├── main.py                 # FastAPI entry point
│   ├── config.py               # Environment + DB config
│   ├── database.py             # SQLAlchemy setup
│   ├── models/
│   │   ├── __init__.py
│   │   ├── user.py
│   │   ├── sleep.py
│   │   ├── task.py
│   │   ├── journal.py
│   │   ├── alerts.py
│   │   └── predictions.py
│   ├── schemas/                # Pydantic request/response
│   │   ├── __init__.py
│   │   ├── sleep.py
│   │   ├── task.py
│   │   └── journal.py
│   ├── api/
│   │   ├── __init__.py
│   │   ├── auth.py            # Login/signup
│   │   ├── sleep.py           # Sleep endpoints
│   │   ├── tasks.py           # Task endpoints
│   │   ├── journal.py         # Journal endpoints
│   │   ├── baseline.py        # Baseline calculation
│   │   ├── alerts.py          # Alert detection
│   │   └── predictions.py     # Risk scoring
│   ├── ml/
│   │   ├── __init__.py
│   │   ├── drift_detector.py  # Anomaly detection engine
│   │   ├── risk_predictor.py  # Collapse prediction
│   │   ├── nlp_processor.py   # Sentiment + tokenization
│   │   └── utils.py           # ML helpers
│   └── services/
│       ├── __init__.py
│       ├── encryption.py      # Data encryption
│       └── notifications.py   # Alert delivery
├── migrations/
│   ├── versions/
│   └── env.py
├── tests/
│   ├── __init__.py
│   └── conftest.py
├── requirements.txt
├── .env.example
├── docker-compose.yml
└── README.md
```

---

## 1. Configuration & Setup

### config.py
```python
import os
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    DATABASE_URL: str = os.getenv(
        "DATABASE_URL", 
        "postgresql://user:password@localhost:5432/mental_drift"
    )
    SECRET_KEY: str = os.getenv("SECRET_KEY", "your-secret-key")
    ALGORITHM: str = "HS256"
    ACCESS_TOKEN_EXPIRE_MINUTES: int = 30
    ENCRYPTION_KEY: str = os.getenv("ENCRYPTION_KEY", "base64-key")
    DEBUG: bool = os.getenv("DEBUG", "false").lower() == "true"
    API_VERSION: str = "v1"
    
    # ML Thresholds
    BASELINE_DAYS: int = 14
    DRIFT_DETECTION_WINDOW_DAYS: int = 7
    SLEEP_DRIFT_THRESHOLD_PCT: float = 20.0
    ACTIVITY_DRIFT_THRESHOLD_PCT: float = 25.0
    
    class Config:
        env_file = ".env"

settings = Settings()
```

### database.py
```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, declarative_base
from app.config import settings

DATABASE_URL = settings.DATABASE_URL
engine = create_engine(DATABASE_URL, echo=settings.DEBUG)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

---

## 2. ML Core: Drift Detection

### ml/drift_detector.py
```python
import numpy as np
from datetime import datetime, timedelta

class DriftDetector:
    """Detects departure from baseline."""
    
    def __init__(self, baseline_metrics):
        self.baseline = baseline_metrics
    
    def detect_sleep_drift(self, sleep_logs_7d):
        if len(sleep_logs_7d) < 3:
            return False, "low", {}
        
        recent_avg = np.mean([log.duration_minutes for log in sleep_logs_7d])
        baseline = self.baseline.avg_sleep_duration_minutes
        deviation_pct = (recent_avg - baseline) / baseline * 100
        
        is_drift = abs(deviation_pct) > 20
        severity = "high" if abs(deviation_pct) > 50 else ("medium" if abs(deviation_pct) > 30 else "low")
        
        return is_drift, severity, {
            "deviation_percentage": round(deviation_pct, 2),
            "baseline_value": baseline,
            "current_value": round(recent_avg, 2)
        }
    
    def detect_activity_drift(self, task_logs_7d):
        if len(task_logs_7d) < 3:
            return False, "low", {}
        
        completed = len([t for t in task_logs_7d if t.completed_at])
        rate = (completed / len(task_logs_7d) * 100) if task_logs_7d else 0
        baseline_rate = self.baseline.avg_task_completion_rate
        deviation_pct = rate - baseline_rate
        
        is_drift = abs(deviation_pct) > 25
        severity = "high" if deviation_pct < -50 else ("medium" if deviation_pct < -30 else "low")
        
        return is_drift, severity, {
            "deviation_percentage": round(deviation_pct, 2),
            "baseline_value": baseline_rate,
            "current_value": round(rate, 2)
        }
    
    def detect_sentiment_drift(self, journal_entries_7d):
        if len(journal_entries_7d) < 2:
            return False, "low", {}
        
        recent = np.mean([e.sentiment_score for e in journal_entries_7d])
        baseline = self.baseline.avg_journal_sentiment
        shift = recent - baseline
        
        is_drift = abs(shift) > 0.3
        severity = "high" if shift < -0.5 else ("medium" if shift < -0.3 else "low")
        
        return is_drift, severity, {
            "sentiment_shift": round(shift, 2),
            "baseline_value": round(baseline, 2),
            "current_value": round(recent, 2)
        }
```

### ml/risk_predictor.py
```python
import numpy as np

class CollapseRiskPredictor:
    """Predicts risk of mental collapse."""
    
    def __init__(self, drift_detector):
        self.detector = drift_detector
    
    def predict_collapse_risk(self, sleep_logs, task_logs, journal_entries, drift_history):
        """Comprehensive risk assessment."""
        
        sleep_7d = self._get_recent(sleep_logs, 7)
        tasks_7d = self._get_recent(task_logs, 7)
        journal_7d = self._get_recent(journal_entries, 7)
        
        risk_factors = []
        scores = {}
        
        # Sleep component
        sleep_score = 0
        if sleep_7d and np.mean([s.duration_minutes for s in sleep_7d]) < 300:
            sleep_score = 60
            risk_factors.append("Severe sleep deprivation: <5 hours/night")
        scores["sleep"] = sleep_score
        
        # Activity component
        completed = len([t for t in tasks_7d if t.completed_at])
        rate = (completed / len(tasks_7d) * 100) if tasks_7d else 100
        activity_score = 60 if rate < 30 else (40 if rate < 50 else 0)
        if rate < 30:
            risk_factors.append(f"Task completion critically low: {rate:.0f}%")
        scores["activity"] = activity_score
        
        # Sentiment component
        avg_sentiment = np.mean([j.sentiment_score for j in journal_7d]) if journal_7d else 0
        sentiment_score = 60 if avg_sentiment < -0.5 else (35 if avg_sentiment < -0.2 else 0)
        if avg_sentiment < -0.5:
            risk_factors.append(f"Highly negative sentiment: {avg_sentiment:.2f}")
        scores["sentiment"] = sentiment_score
        scores["social"] = 0
        
        # Composite score
        composite = np.mean(list(scores.values()))
        
        data_points = len(sleep_7d) + len(tasks_7d) + len(journal_7d)
        confidence = min(data_points / 21, 1.0)
        
        return {
            "risk_score": round(composite, 1),
            "confidence": round(confidence, 2),
            "sleep_component": round(scores["sleep"], 1),
            "activity_component": round(scores["activity"], 1),
            "sentiment_component": round(scores["sentiment"], 1),
            "social_component": 0,
            "collapse_probability_7days": round(min(composite / 100 * 1.0, 0.95), 2),
            "collapse_probability_14days": round(min(composite / 100 * 1.5, 0.95), 2),
            "risk_factors": risk_factors,
            "recovery_suggestions": self._generate_suggestions(scores)
        }
    
    def _get_recent(self, items, days):
        from datetime import datetime, timedelta
        cutoff = datetime.utcnow() - timedelta(days=days)
        return [item for item in items if item.created_at >= cutoff]
    
    def _generate_suggestions(self, scores):
        suggestions = []
        if scores["sleep"] > 40:
            suggestions.append("Try to sleep 30 minutes earlier tonight")
        if scores["activity"] > 40:
            suggestions.append("Complete just 1 small task today")
        if scores["sentiment"] > 40:
            suggestions.append("Send 1 message to a friend today")
        return suggestions if suggestions else ["Maintain current habits"]
```

### ml/nlp_processor.py
```python
from textblob import TextBlob
import re
from collections import Counter

class NLPProcessor:
    """Processes journal text."""
    
    STRESS_KEYWORDS = ["stress", "anxious", "panic", "overwhelmed"]
    SLEEP_KEYWORDS = ["sleep", "insomnia", "tired", "exhausted"]
    SOCIAL_KEYWORDS = ["alone", "lonely", "isolated", "withdraw"]
    BURNOUT_KEYWORDS = ["burnout", "exhausted", "no energy"]
    
    def process_entry(self, text):
        blob = TextBlob(text)
        sentiment = blob.sentiment.polarity
        
        text_lower = text.lower()
        topics = []
        
        if any(kw in text_lower for kw in self.STRESS_KEYWORDS):
            topics.append("stress")
        if any(kw in text_lower for kw in self.SLEEP_KEYWORDS):
            topics.append("sleep")
        if any(kw in text_lower for kw in self.SOCIAL_KEYWORDS):
            topics.append("social_withdrawal")
        if any(kw in text_lower for kw in self.BURNOUT_KEYWORDS):
            topics.append("burnout")
        
        words = re.findall(r'\b[a-z]+\b', text_lower)
        stop_words = {"the", "a", "is", "it", "and", "or", "but", "in", "on"}
        words = [w for w in words if w not in stop_words and len(w) > 2]
        
        return {
            "sentiment_score": sentiment,
            "word_count": len(text.split()),
            "detected_topics": list(set(topics)),
            "raw_tokens": dict(Counter(words))
        }
```

---

## 3. Requirements

### requirements.txt
```
fastapi==0.104.0
uvicorn==0.24.0
sqlalchemy==2.0.0
psycopg2-binary==2.9.0
pydantic==2.0.0
pydantic-settings==2.0.0
pandas==2.1.0
numpy==1.24.0
scikit-learn==1.3.0
textblob==0.17.1
python-jose==3.3.0
passlib==1.7.4
cryptography==41.0.0
python-dotenv==1.0.0
pytest==7.4.0
pytest-asyncio==0.21.0
```

---

## 4. Docker Setup

### docker-compose.yml
```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_USER: mental_drift_user
      POSTGRES_PASSWORD: secure_password_here
      POSTGRES_DB: mental_drift
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      DATABASE_URL: postgresql://mental_drift_user:secure_password_here@postgres:5432/mental_drift
      DEBUG: "true"
    depends_on:
      - postgres
    volumes:
      - .:/app
    command: uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload

volumes:
  postgres_data:
```

---

## 5. Quick Start

```bash
# Clone & setup
git clone https://github.com/khangxD1304/mental-drift.git
cd mental-drift

# Run with Docker
docker-compose up -d

# API available at: http://localhost:8000/docs
# Database: postgresql://localhost:5432/mental_drift
```

Backend prototype is ready to extend with full API endpoints.