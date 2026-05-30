# Mental Drift: Ethical Review Framework

## Objective
Ensure Mental Drift remains a helpful tool, not a surveillance system or false diagnostician.

---

## 1. Core Ethical Principles

### 1.1 Non-Diagnostic Boundary
**Principle:** Never diagnose, treat, or cure mental health conditions.

**Implementation:**
- ❌ Don't say: "You have depression"
- ✅ Say: "Your sleep patterns have changed significantly"
- ❌ Don't say: "Take medication X"
- ✅ Say: "These patterns are worth discussing with a healthcare provider"

**Enforcement:**
- All user-facing messages reviewed by communications team
- API responses include: "This is pattern detection, not medical advice"
- Help section clearly states limitations

---

### 1.2 Data Minimalism
**Principle:** Collect only what's necessary. Don't drift into surveillance.

**Scope (MVP):**
- ✅ Sleep logs (user-provided)
- ✅ Task completion (external integration)
- ✅ Journal entries (user-provided)

**Explicitly NOT collected:**
- ❌ Browsing history
- ❌ Location data
- ❌ Camera/microphone
- ❌ Social media scraping

---

### 1.3 User Autonomy
**Principle:** Users own their data and control its use.

**Rights:**
1. **Access:** Export all data anytime (JSON format)
2. **Deletion:** Delete all data → wiped within 30 days
3. **Opt-out:** Disable ML analysis while keeping raw data
4. **Transparency:** See exactly what was detected and why

**Implementation:**
- Settings page with clear data controls
- Export button (one-click download)
- Delete account (hard delete, not soft)
- Plaintext explanations of drift detection

---

### 1.4 No Exploitation of Vulnerability
**Principle:** Don't use mental health data to manipulate or monetize suffering.

**Restrictions:**
- ❌ No targeted ads based on mental health patterns
- ❌ No data selling to pharmaceutical companies
- ❌ No gamification that creates compulsive checking
- ❌ No dark patterns (scare tactics)

**Safeguards:**
- Business model: subscription only (not ads)
- Privacy policy: data never sold
- Terms of service: binding promises on restrictions

---

## 2. Specific Risks & Mitigation

### 2.1 Risk: False Positives
**Scenario:** System flags high-risk when just a normal bad week.

**Mitigation:**
- High confidence threshold before alerts (>80%)
- Allow users to provide context
- Pattern must persist 5+ days before escalating
- "Ignore this alert" option always available

---

### 2.2 Risk: Algorithm Bias
**Scenario:** System misses warning signs in different demographics.

**Mitigation:**
- Baseline is per-person, not population average
- Regular audit for demographic disparities
- Diverse testing group during beta
- Document known limitations

---

### 2.3 Risk: Surveillance by Third Parties
**Scenario:** Employer/school/partner uses Mental Drift to monitor someone.

**Mitigation:**
- No login via work email
- Encryption: data unreadable even if shared
- Clear ToS: account is personal
- Option to use anonymously

---

### 2.4 Risk: Over-Reliance
**Scenario:** User ignores professional help because "the app says I'm fine."

**Mitigation:**
- Every alert includes: "Share with a healthcare provider"
- If risk_score > 70: direct crisis helpline link
- Dashboard never replaces provider communication
- FAQ: "Always see therapist if concerned, regardless of app"

---

### 2.5 Risk: Data Breach
**Scenario:** Private journal entries exposed.

**Mitigation:**
- End-to-end encryption: only user can decrypt
- No master decryption key (even admins can't read)
- Incident response: users notified within 48 hours
- Annual third-party security audits
- SOC 2 Type II compliance target

---

## 3. Consent & Transparency

### 3.1 Informed Consent Flow

**Step 1: Sign Up**
```
"Mental Drift uses AI to track pattern changes.
It is NOT a medical tool.

We collect:
- Sleep logs (you provide)
- Tasks completed (from integrations)
- Journal entries (you provide)

We NEVER:
- Monitor location/browsing
- Sell data
- Use for ads

Agree? [Yes] [Learn More]"
```

**Step 2: First Baseline Alert**
```
"We now understand your normal patterns.
Starting tomorrow, we'll alert you if patterns change.

These alerts are informational, NOT diagnoses.
Please talk to a healthcare provider if struggling.

Understand? [Got it]"
```

**Step 3: High-Risk Alert**
```
"Your patterns have shifted significantly.
You're not yourself lately.

Resources:
- Crisis hotline: [number]
- Provider finder: [link]

What would help?
[Share with therapist] [Just let me know]"
```

---

## 4. Governance & Accountability

### 4.1 Ethics Board

**Composition:**
- Mental health professional (psychologist/psychiatrist)
- Data ethicist / Privacy expert
- User representative
- Legal counsel
- Company leadership

**Responsibilities:**
- Review all new features before launch
- Quarterly audit of alerts (accuracy? harms?)
- Investigate complaints
- Annual public report

---

### 4.2 Public Accountability

**Annual Report (published openly):**

```
Mental Drift 2024 Annual Ethics Report

Users: [X] active
Data points: [Y] total records
Harmful alerts: [Z] % of total

Complaints received: [N]
Resolutions: [description]

Data breaches: 0 (or details)

Algorithm changes: [description]

Research partnerships: [list]

What's next: [improvements planned]
```

---

### 4.3 User Bill of Rights

**Mental Drift User Bill of Rights**

1. **Right to Know**
   - What data is collected
   - How it's used
   - What alerts mean

2. **Right to Control**
   - Choose which data sources
   - Edit/delete any entry
   - Delete account + all data

3. **Right to Privacy**
   - Data encrypted
   - Not sold
   - Not shared with employers/schools

4. **Right to Context**
   - Every alert explains why
   - Every alert says: "Not a diagnosis"
   - Resources for professional help

5. **Right to Recourse**
   - Report issue → response in 7 days
   - Request data audit
   - Request algorithm explanation

6. **Right to Opt-Out**
   - Stop tracking anytime
   - Delete data anytime
   - No penalty for leaving

---

## 5. Crisis Response Protocol

### 5.1 When User Indicates Immediate Risk

**DO:**
1. Display immediate support resources
2. Crisis hotline with one-click call
3. Encourage professional contact
4. Keep alert but mark "reviewed"

**DON'T:**
- ❌ Try to assess if risk is "real"
- ❌ Contact authorities (unless legal requirement)
- ❌ Message aggressively
- ❌ Delete/hide alert

**Example UI:**
```
⚠️ URGENT SUPPORT

If you're having thoughts of self-harm:

[CALL 988 - Suicide & Crisis Lifeline]
Text HOME to 741741
Emergency: Call 911 or go to ER

We're not a crisis service.
A human professional can help more.
```

---

## 6. Launch Checklist

- [ ] **Legal:** Privacy policy reviewed by lawyer
- [ ] **Legal:** ToS explicitly state non-diagnostic
- [ ] **Legal:** GDPR/CCPA compliance verified
- [ ] **Tech:** Encryption implemented and audited
- [ ] **Tech:** No plaintext sensitive data logging
- [ ] **Product:** User can opt-out each data source
- [ ] **Product:** User can download all data
- [ ] **Product:** User can delete account + data
- [ ] **Content:** All alerts reviewed for accuracy
- [ ] **Content:** Help section covers crisis
- [ ] **Governance:** Ethics board established
- [ ] **Research:** If collecting for research, IRB approval
- [ ] **Communication:** Launch includes ethical commitments
- [ ] **Testing:** Beta test with diverse users
- [ ] **Monitoring:** Alert accuracy tracked
- [ ] **Incident:** Data breach response plan documented

---

## 7. Red Lines (Never Cross)

These would disqualify the project:

1. ❌ Claiming to diagnose/treat mental illness
2. ❌ Selling user data
3. ❌ Using mental health data for targeted ads
4. ❌ Collecting data without consent
5. ❌ Storing unencrypted sensitive data
6. ❌ Sharing data with employers/schools without explicit consent
7. ❌ Making alerts without explaining reasoning
8. ❌ Manipulating users based on vulnerability

**If any happen:** Project shuts down, users notified, data deleted.

---

## 8. How This Makes Money (Ethically)

### Business Model
- **Freemium:** Basic logging free forever
- **Pro:** $9.99/month for ML analysis & alerts
- **Family:** $19.99/month for up to 5 people
- **B2B:** Universities/corps can deploy (data stays with user)

**NOT:**
- ❌ Ads
- ❌ Data sales
- ❌ Hiring/insurance models
- ❌ Pharma company access

---

## 9. Sunset Clause

**If the system is ever used contrary to these principles:**

1. **Immediate notice** to all users
2. **30-day grace period** for data export/deletion
3. **Transparent explanation** of what happened
4. **Path to remedy** (fix feature or shut down)

This isn't legal fine-print. This is a promise.

---

**Version:** 1.0
**Last Updated:** 2024-05-29
**Public Review:** [Feedback form link]