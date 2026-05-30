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

---

### UserBaselineMetrics Table
```sql
CREATE TABLE user_baseline_metrics (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    period_start DATE NOT NULL,
    period_end DATE NOT NULL,
    avg_sleep_duration_minutes FLOAT NOT NULL,
    avg_sleep_start_time TIME,
    avg_sleep_end_time TIME,
    sleep_consistency_score FLOAT CHECK (sleep_consistency_score >= 0 AND sleep_consistency_score <= 100),
    avg_daily_tasks_completed FLOAT NOT NULL,
    avg_task_completion_rate FLOAT NOT NULL CHECK (avg_task_completion_rate >= 0 AND avg_task_completion_rate <= 100),
    peak_productivity_hours JSONB DEFAULT '[]',
    avg_daily_messages_sent FLOAT DEFAULT 0,
    avg_response_time_minutes FLOAT DEFAULT 0,
    avg_journal_sentiment FLOAT DEFAULT 0,
    avg_journal_length_words FLOAT DEFAULT 0,
    vocabulary_diversity FLOAT DEFAULT 0 CHECK (vocabulary_diversity >= 0 AND vocabulary_diversity <= 1),
    created_at TIMESTAMP DEFAULT NOW(),
    status VARCHAR(20) DEFAULT 'active',
    INDEX idx_baseline_user (user_id, created_at DESC)
);
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

---

### CollapseRiskPrediction Table
```sql
CREATE TABLE collapse_risk_prediction (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    predicted_at TIMESTAMP DEFAULT NOW(),
    valid_until TIMESTAMP DEFAULT NOW() + INTERVAL '14 days',
    risk_score FLOAT NOT NULL CHECK (risk_score >= 0 AND risk_score <= 100),
    confidence FLOAT NOT NULL CHECK (confidence >= 0 AND confidence <= 1),
    sleep_component FLOAT NOT NULL,
    activity_component FLOAT NOT NULL,
    sentiment_component FLOAT NOT NULL,
    social_component FLOAT NOT NULL,
    risk_factors JSONB NOT NULL DEFAULT '[]',
    recovery_suggestions JSONB NOT NULL DEFAULT '[]',
    collapse_probability_7days FLOAT NOT NULL CHECK (collapse_probability_7days >= 0 AND collapse_probability_7days <= 1),
    collapse_probability_14days FLOAT NOT NULL CHECK (collapse_probability_14days >= 0 AND collapse_probability_14days <= 1),
    INDEX idx_prediction_user_valid (user_id, valid_until DESC)
);
```

---

## 2. Data Flow & User Journey

### Week 1: Baseline Collection (Days 1-14)
- User logs sleep, tasks, journal entries
- System stores data, baseline_established = FALSE
- No drift detection yet

### Day 15: Baseline Calculation
- System calculates average metrics
- Sets baseline_established = TRUE

### Days 16+: Drift Detection Active
- Nightly job compares recent data against baseline
- Creates DriftAlert if significant deviation
- Calculates CollapseRiskPrediction if multiple drifts

---

## 3. Encryption Strategy

**Fields to Encrypt:**
- users.email_encrypted
- sleep_logs.notes_encrypted
- journal_entries.content_encrypted

**Flow:**
1. Password → PBKDF2(password, salt) → user_encryption_key
2. Data → AES-256-GCM encrypt(plaintext, key) → ciphertext
3. On read: decrypt(ciphertext, key) → plaintext

**Security:** Only user with correct password can decrypt their data.

---

## 4. API Examples

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

### Get Collapse Risk
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

## 5. Retention Policy

- SleepLogs: 2 years
- TaskLogs: 1 year
- JournalEntries: 2 years
- DriftAlerts: 6 months
- CollapseRiskPrediction: 1 year

---

Schema is production-ready for MVP deployment.