# Mental Drift: Complete Data Schema & Architecture

**Status:** Production-ready MVP schema (Option A: Minimal)
**Stack:** PostgreSQL + Python/FastAPI + React/Next.js
**Created:** 2024
**Last Updated:** 2024-05-29

---

## 1. Core Entities & Database Schema

### Users Table
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    username VARCHAR(50) UNIQUE NOT NULL,
    email_encrypted BYTEA NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    baseline_established BOOLEAN DEFAULT FALSE,
    baseline_established_at TIMESTAMP,
    preferences JSONB DEFAULT '{"timezone": "UTC", "integrations": ["manual"], "notifications_enabled": true}',
    encryption_salt BYTEA NOT NULL,
    INDEX idx_users_email (email_encrypted),
    INDEX idx_users_username (username)
);
```

**Fields:**
- `id`: UUID primary key
- `username`: Unique username for login
- `email_encrypted`: Encrypted email (at rest)
- `password_hash`: Hashed password (bcrypt)
- `baseline_established`: Flag set to true after 14 days of data
- `baseline_established_at`: Timestamp when baseline was locked
- `preferences`: JSON storing timezone, integration sources, notification settings
- `encryption_salt`: Per-user salt for encryption key derivation

**Data Lifecycle:**
- Day 1-14: User logs data, baseline_established = FALSE
- Day 15: System calculates baseline metrics, sets baseline_established = TRUE
- After Day 15: Drift detection activates

---

### SleepLogs Table
```sql
CREATE TABLE sleep_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    date DATE NOT NULL,
    sleep_start TIMESTAMP NOT NULL,
    sleep_end TIMESTAMP NOT NULL,
    duration_minutes INTEGER NOT NULL,
    quality_score SMALLINT CHECK (quality_score >= 1 AND quality_score <= 10),
    notes_encrypted BYTEA,
    source VARCHAR(20) DEFAULT 'manual',
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    UNIQUE(user_id, date),
    INDEX idx_sleep_user_date (user_id, date DESC)
);
```

**Fields:**
- `date`: Which date the sleep was for (not necessarily when logged)
- `sleep_start`, `sleep_end`: Full timestamps
- `duration_minutes`: Computed on insert: `(sleep_end - sleep_start) / 60`
- `quality_score`: Optional 1-10 rating by user
- `notes_encrypted`: Encrypted notes (e.g., "Had bad dreams")
- `source`: "manual", "apple_health", "google_fit" (for future integrations)

**Example Insert:**
```json
{
  "user_id": "550e8400-e29b-41d4-a716-446655440000",
  "date": "2024-05-28",
  "sleep_start": "2024-05-28T23:30:00Z",
  "sleep_end": "2024-05-29T07:00:00Z",
  "quality_score": 7,
  "notes": "Woke up twice"
}
```

---

### TaskLogs Table
```sql
CREATE TABLE task_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    external_id VARCHAR(100),
    title VARCHAR(500) NOT NULL,
    completed_at TIMESTAMP,
    due_date DATE,
    created_at TIMESTAMP DEFAULT NOW(),
    source VARCHAR(20) DEFAULT 'manual',
    metadata JSONB DEFAULT '{}',
    INDEX idx_task_user_created (user_id, created_at DESC)
);
```

**Fields:**
- `external_id`: For Todoist/Notion integration (e.g., "todoist:12345678")
- `title`: Task name
- `completed_at`: NULL if not done, timestamp if done
- `due_date`: Optional deadline
- `source`: "manual", "todoist", "notion"
- `metadata`: JSON for project name, priority, labels, etc.

**Example Insert:**
```json
{
  "user_id": "550e8400-e29b-41d4-a716-446655440000",
  "title": "Study for exam",
  "due_date": "2024-05-30",
  "source": "manual"
}
```

---

### JournalEntries Table
```sql
CREATE TABLE journal_entries (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    content_encrypted BYTEA NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    date DATE NOT NULL,
    sentiment_score FLOAT CHECK (sentiment_score >= -1 AND sentiment_score <= 1),
    word_count INTEGER NOT NULL DEFAULT 0,
    detected_topics JSONB DEFAULT '[]',
    raw_tokens JSONB DEFAULT '{}',
    INDEX idx_journal_user_date (user_id, date DESC)
);
```

**Fields:**
- `content_encrypted`: Full journal text, encrypted at rest
- `date`: Which day it's about (can journal about past days)
- `sentiment_score`: -1 (very negative) to 1 (very positive), computed via TextBlob
- `word_count`: Number of words (computed)
- `detected_topics`: Array like ["stress", "sleep", "social_withdrawal"]
- `raw_tokens`: Word frequency map for drift analysis

**Example Insert:**
```json
{
  "user_id": "550e8400-e29b-41d4-a716-446655440000",
  "date": "2024-05-28",
  "content": "Felt really anxious today. Didn't sleep well. Couldn't focus on work."
}
```

---

### UserBaselineMetrics Table
```sql
CREATE TABLE user_baseline_metrics (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    period_start DATE NOT NULL,
    period_end DATE NOT NULL,
    
    -- Sleep metrics
    avg_sleep_duration_minutes FLOAT NOT NULL,
    avg_sleep_start_time TIME,
    avg_sleep_end_time TIME,
    sleep_consistency_score FLOAT CHECK (sleep_consistency_score >= 0 AND sleep_consistency_score <= 100),
    
    -- Activity metrics
    avg_daily_tasks_completed FLOAT NOT NULL,
    avg_task_completion_rate FLOAT NOT NULL CHECK (avg_task_completion_rate >= 0 AND avg_task_completion_rate <= 100),
    peak_productivity_hours JSONB DEFAULT '[]',
    
    -- Social metrics
    avg_daily_messages_sent FLOAT DEFAULT 0,
    avg_response_time_minutes FLOAT DEFAULT 0,
    
    -- Text metrics
    avg_journal_sentiment FLOAT DEFAULT 0,
    avg_journal_length_words FLOAT DEFAULT 0,
    vocabulary_diversity FLOAT DEFAULT 0 CHECK (vocabulary_diversity >= 0 AND vocabulary_diversity <= 1),
    
    created_at TIMESTAMP DEFAULT NOW(),
    status VARCHAR(20) DEFAULT 'active',
    INDEX idx_baseline_user (user_id, created_at DESC)
);
```

**Purpose:** Stores the user's "normal state" calculated from their first 14 days of data.

**Example (After 14 days of data collection):**
```json
{
  "user_id": "550e8400-e29b-41d4-a716-446655440000",
  "period_start": "2024-05-15",
  "period_end": "2024-05-28",
  "avg_sleep_duration_minutes": 450.0,
  "avg_sleep_start_time": "23:30:00",
  "avg_sleep_end_time": "07:30:00",
  "sleep_consistency_score": 78.5,
  "avg_daily_tasks_completed": 6.8,
  "avg_task_completion_rate": 82.5,
  "peak_productivity_hours": [9, 10, 14, 15, 16],
  "avg_journal_sentiment": 0.25,
  "avg_journal_length_words": 185.0,
  "vocabulary_diversity": 0.72,
  "status": "active"
}
```

---

### DriftAlerts Table
```sql
CREATE TABLE drift_alerts (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    alert_type VARCHAR(50) NOT NULL,
    severity VARCHAR(20) NOT NULL CHECK (severity IN ('low', 'medium', 'high')),
    dimension VARCHAR(100) NOT NULL,
    baseline_value FLOAT NOT NULL,
    current_value FLOAT NOT NULL,
    deviation_percentage FLOAT NOT NULL,
    detected_at TIMESTAMP DEFAULT NOW(),
    message TEXT NOT NULL,
    is_acknowledged BOOLEAN DEFAULT FALSE,
    metadata JSONB DEFAULT '{}',
    INDEX idx_drift_user_severity (user_id, severity, detected_at DESC)
);
```

**Fields:**
- `alert_type`: "sleep_drift", "activity_drift", "sentiment_drift", "collapse_risk"
- `severity`: "low" (10-30% deviation), "medium" (30-50%), "high" (>50%)
- `dimension`: What's drifting (e.g., "sleep_duration", "task_completion_rate")
- `deviation_percentage`: `(current - baseline) / baseline * 100`
- `message`: Human-readable alert
- `metadata`: Additional context like `{"days_in_drift": 5, "confidence": 0.87}`

**Example Alert:**
```json
{
  "user_id": "550e8400-e29b-41d4-a716-446655440000",
  "alert_type": "sleep_drift",
  "severity": "high",
  "dimension": "sleep_duration",
  "baseline_value": 450,
  "current_value": 240,
  "deviation_percentage": -46.67,
  "message": "Your sleep duration is 47% below baseline. Averaging 4 hours vs your normal 7.5 hours over the last 5 days.",
  "metadata": {
    "days_in_drift": 5,
    "confidence": 0.91,
    "trend": "declining"
  }
}
```

---

### CollapseRiskPrediction Table
```sql
CREATE TABLE collapse_risk_prediction (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    predicted_at TIMESTAMP DEFAULT NOW(),
    valid_until TIMESTAMP DEFAULT NOW() + INTERVAL '14 days',
    
    -- Composite score
    risk_score FLOAT NOT NULL CHECK (risk_score >= 0 AND risk_score <= 100),
    confidence FLOAT NOT NULL CHECK (confidence >= 0 AND confidence <= 1),
    
    -- Component scores
    sleep_component FLOAT NOT NULL,
    activity_component FLOAT NOT NULL,
    sentiment_component FLOAT NOT NULL,
    social_component FLOAT NOT NULL,
    
    -- Factors & suggestions
    risk_factors JSONB NOT NULL DEFAULT '[]',
    recovery_suggestions JSONB NOT NULL DEFAULT '[]',
    
    -- Probabilities
    collapse_probability_7days FLOAT NOT NULL CHECK (collapse_probability_7days >= 0 AND collapse_probability_7days <= 1),
    collapse_probability_14days FLOAT NOT NULL CHECK (collapse_probability_14days >= 0 AND collapse_probability_14days <= 1),
    
    INDEX idx_prediction_user_valid (user_id, valid_until DESC)
);
```

---

## 2. Data Flow & User Journey

### Week 1: Baseline Collection (Days 1-14)

User logs sleep, tasks, and journal entries. System stores data but doesn't detect drift yet (baseline_established = FALSE).

### Day 15: Baseline Calculation

System calculates average metrics from first 14 days and sets baseline_established = TRUE.

### Days 16+: Drift Detection (Active)

Nightly job compares recent data against baseline. If significant deviation detected, creates DriftAlert.

---

## 3. Encryption Strategy

**Fields to Encrypt:**
- users.email_encrypted
- sleep_logs.notes_encrypted
- journal_entries.content_encrypted

**Flow:**
1. User password → PBKDF2(password, salt) → user_encryption_key
2. Data → AES-256-GCM encrypt(plaintext, key) → ciphertext
3. On read: decrypt(ciphertext, key) → plaintext

**Security:** Database admin cannot read encrypted fields. Only user with correct password can decrypt.

---

## 4. Indexes & Query Performance

```sql
CREATE INDEX idx_users_username ON users(username);
CREATE INDEX idx_sleep_user_date ON sleep_logs(user_id, date DESC);
CREATE INDEX idx_task_user_created ON task_logs(user_id, created_at DESC);
CREATE INDEX idx_journal_user_date ON journal_entries(user_id, date DESC);
CREATE INDEX idx_drift_user_severity ON drift_alerts(user_id, severity, detected_at DESC);
CREATE INDEX idx_prediction_user_valid ON collapse_risk_prediction(user_id, valid_until DESC);
```

---

## 5. Data Retention & Privacy

**Retention Policy:**
- SleepLogs: 2 years
- TaskLogs: 1 year
- JournalEntries: 2 years
- DriftAlerts: 6 months
- CollapseRiskPrediction: 1 year

**GDPR Compliance:**
- Right to Access: Export data in JSON
- Right to Deletion: Wipe all data within 30 days
- Right to Portability: Download data in standard format

---

## 6. API Examples

### Create Sleep Log
```bash
POST /api/v1/sleep
Authorization: Bearer <token>

{
  "date": "2024-05-28",
  "sleep_start": "2024-05-28T23:30:00Z",
  "sleep_end": "2024-05-29T07:00:00Z",
  "quality_score": 7
}
```

### Get Collapse Risk Assessment
```bash
GET /api/v1/predictions/collapse-risk
Authorization: Bearer <token>

Response:
{
  "risk_score": 72.5,
  "confidence": 0.84,
  "collapse_probability_7days": 0.38,
  "collapse_probability_14days": 0.62,
  "recovery_suggestions": [
    "Try to sleep 30 minutes earlier tonight",
    "Complete just 1 small task today",
    "Send 1 message to a friend"
  ]
}
```

---

Schema is production-ready for MVP deployment.