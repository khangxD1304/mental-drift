# Mental Drift: Frontend Wireframes & UI/UX Design

**Stack:** React 18 + Next.js 14 + TypeScript + Tailwind CSS
**Status:** Production-ready component designs
**Last Updated:** 2024-05-29

---

## 1. User Journey & Page Structure

### User Flow
```
Landing Page
    ↓
[Sign Up / Login]
    ↓
Onboarding (Set timezone, preferences)
    ↓
[Dashboard - Days 1-14: Data Collection Phase]
    ↓
[Dashboard - Days 15+: Drift Detection Active]
```

### Page Map
```
/                          → Landing page
/auth/signup              → Sign up form
/auth/login               → Login form
/onboarding               → Initial setup
/dashboard                → Main dashboard
/dashboard/sleep          → Sleep log history
/dashboard/tasks          → Task tracking
/dashboard/journal        → Journal entries
/dashboard/alerts         → Drift alerts
/dashboard/risk-assessment → Risk score & predictions
/settings                 → User settings & data controls
/settings/data-export     → Export data
/settings/privacy         → Privacy & consent info
```

---

## 2. Landing Page (Public)

### Hero Section
```
┌─────────────────────────────────────────────────────┐
│                                                       │
│  Mental Drift                                         │
│  Understand your patterns, recognize changes early   │
│                                                       │
│  [Sign Up]  [Learn More]                             │
│                                                       │
│  ⚠️  This is NOT a diagnosis tool                     │
│  📊 Pattern detection to support your wellbeing      │
│                                                       │
└─────────────────────────────────────────────────────┘
```

### Key Features Section
```
┌──────────────────┬──────────────────┬──────────────────┐
│  🛌 Sleep       │  📋 Tasks        │  📝 Journal      │
│  Tracking        │  Completion      │  Sentiment       │
│  Understand      │  Monitor your    │  Track your      │
│  sleep patterns  │  productivity    │  emotional state │
└──────────────────┴──────────────────┴──────────────────┘

┌──────────────────┬──────────────────┬──────────────────┐
│  🎯 Drift        │  ⚡ Risk Score   │  🔒 Privacy      │
│  Detection       │  Assessment      │  First           │
│  Get alerted     │  Understand your │  Your data is    │
│  to changes      │  wellbeing score │  encrypted & safe│
└──────────────────┴──────────────────┴──────────────────┘
```

### Trust Indicators
```
✅ End-to-end encryption
✅ No data selling
✅ Not a medical diagnosis
✅ Open ethics framework
✅ User data controls
```

---

## 3. Authentication Screens

### Sign Up Form
```
┌──────────────────────────────────────────┐
│  Create Your Account                     │
│                                          │
│  📧 Email                                │
│  [ _________________ ]                   │
│                                          │
│  👤 Username                             │
│  [ _________________ ]                   │
│                                          │
│  🔐 Password (min 12 chars)              │
│  [ _________________ ]                   │
│                                          │
│  ☑️  I understand this is not a          │
│      medical diagnosis tool              │
│                                          │
│  [Create Account]                        │
│                                          │
│  Already have an account? [Login]        │
└──────────────────────────────────────────┘
```

### Login Form
```
┌──────────────────────────────────────────┐
│  Welcome Back                            │
│                                          │
│  👤 Username or Email                    │
│  [ _________________ ]                   │
│                                          │
│  🔐 Password                             │
│  [ _________________ ]                   │
│                                          │
│  ☑️  Remember me                         │
│                                          │
│  [Login]                                 │
│                                          │
│  Don't have an account? [Sign Up]        │
│  [Forgot Password?]                      │
└──────────────────────────────────────────┘
```

---

## 4. Onboarding Flow (Days 1-3)

### Step 1: Timezone & Preferences
```
┌──────────────────────────────────────────┐
│  Let's Get Started                       │
│  Step 1 of 3                             │
│                                          │
│  What's your timezone?                   │
│  [ America/New_York ▼ ]                  │
│                                          │
│  Notification preferences?               │
│  ☑️  Email alerts                        │
│  ☑️  In-app alerts                       │
│  ☐  SMS alerts                           │
│                                          │
│  [Back]  [Next]                          │
└──────────────────────────────────────────┘
```

### Step 2: Data Sources
```
┌──────────────────────────────────────────┐
│  Connect Your Data                       │
│  Step 2 of 3                             │
│                                          │
│  Which data sources would you like?      │
│                                          │
│  ☑️  Manual Sleep Logging                │
│      Log sleep manually each day         │
│                                          │
│  ☐  Apple Health Integration             │
│      [Connect]                           │
│                                          │
│  ☐  Google Fit Integration               │
│      [Coming Soon]                       │
│                                          │
│  ☑️  Journal Entries                     │
│      Write your thoughts daily           │
│                                          │
│  ☑️  Task Tracking (Manual)              │
│      Log task completion                 │
│                                          │
│  [Back]  [Next]                          │
└──────────────────────────────────────────┘
```

### Step 3: Informed Consent
```
┌──────────────────────────────────────────┐
│  Understanding Mental Drift              │
│  Step 3 of 3                             │
│                                          │
│  ⚠️  Important Information                │
│                                          │
│  Mental Drift is NOT:                    │
│  ❌ A medical diagnosis tool             │
│  ❌ A substitute for therapy             │
│  ❌ A crisis intervention service        │
│                                          │
│  Mental Drift IS:                        │
│  ✅ A personal pattern tracker           │
│  ✅ A wellness support tool              │
│  ✅ Your data, your control              │
│                                          │
│  I understand and accept [✓]             │
│                                          │
│  [Back]  [Start Using Mental Drift]      │
└──────────────────────────────────────────┘
```

---

## 5. Dashboard - Main View (Days 1-14: Collection Phase)

### Top Navigation
```
┌──────────────────────────────────────────────────────────────┐
│  Mental Drift              [Sleep] [Tasks] [Journal]          │
│                                            [⚙️ Settings] [👤] │
└──────────────────────────────────────────────────────────────┘
```

### Baseline Collection Phase
```
┌──────────────────────────────────────────────────────────────┐
│                                                              │
│  📊 Baseline Collection in Progress                          │
│                                                              │
│  ████████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  10 / 14 days  │
│                                                              │
│  We're learning your patterns. Start tracking:              │
│                                                              │
│  🛌 Sleep                📋 Tasks              📝 Journal    │
│  ☑️ 7 entries            ☑️ 8 entries          ☑️ 3 entries  │
│  [+ Log Sleep]           [+ Add Task]          [+ Write]     │
│                                                              │
│  💡 Tip: The more consistent your entries, the better       │
│     we can understand your normal patterns                   │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

### Recent Entries Quick View
```
┌────────────────────────────────────┬─────────────────────┐
│  Last 7 Days                       │  Today's Tracking   │
│                                    │                     │
│  Sleep:                            │  🛌 Sleep           │
│  May 23: 7.5h (Quality: 8/10)      │  Last night: 6.5h   │
│  May 24: 6.0h (Quality: 5/10) ⚠️   │  Quality: 6/10      │
│  May 25: 7.2h (Quality: 7/10)      │                     │
│  May 26: 5.8h (Quality: 4/10)      │  📋 Tasks           │
│  May 27: 7.0h (Quality: 7/10)      │  Completed: 5/8     │
│  May 28: 6.8h (Quality: 6/10)      │  Progress: 62%      │
│  May 29: 7.3h (Quality: 8/10)      │                     │
│                                    │  📝 Journal         │
│  Avg: 6.8 hours                    │  Last entry:        │
│                                    │  "Feeling stressed" │
│                                    │  Sentiment: -0.3    │
│                                    │  [+ Add Entry]      │
└────────────────────────────────────┴─────────────────────┘
```

---

## 6. Dashboard - Post-Baseline (Days 15+: Drift Detection Active)

### Risk Assessment Card (Top Priority)
```
┌────────────────────────────────────────────────────────────┐
│  ⚠️  RISK ASSESSMENT                                        │
│                                                            │
│  Overall Risk Score: 68 / 100  [MEDIUM RISK] 🟡            │
│                                                            │
│  Component Breakdown:                                      │
│  🛌 Sleep Component:    ████░░░  (45%)  - Declining        │
│  📋 Activity Component: ██░░░░░░  (25%)  - Normal          │
│  😔 Sentiment Component:██████░░  (60%)  - Negative        │
│  👥 Social Component:   ███░░░░░  (30%)  - Stable          │
│                                                            │
│  ⚡ 7-Day Collapse Probability: 28%                         │
│  ⚡ 14-Day Collapse Probability: 44%                        │
│                                                            │
│  Recovery Suggestions:                                     │
│  1. Try to sleep 30 minutes earlier tonight                │
│  2. Complete just 1 small task today                       │
│  3. Send 1 message to a friend                             │
│                                                            │
│  [Share with Healthcare Provider] [More Details]           │
│                                                            │
│  ℹ️  This is pattern detection, not a diagnosis. If you're │
│     in crisis, call 988 (Suicide & Crisis Lifeline)        │
└────────────────────────────────────────────────────────────┘
```

### Drift Alerts Feed
```
┌────────────────────────────────────────────────────────────┐
│  🔔 Recent Alerts                                          │
│                                                            │
│  [🔴 HIGH] Sleep Drift - Yesterday                         │
│  ───────────────────────────────────────────────────────── │
│  Your sleep is 47% below baseline (4.2h vs 7.5h normal)    │
│  This has been happening for 5 days                        │
│  📊 [View Details]  ✓ [Acknowledge]  [Snooze]             │
│                                                            │
│  [🟡 MEDIUM] Activity Drift - 2 days ago                   │
│  ───────────────────────────────────────────────────────── │
│  Task completion rate dropped 35% (47% vs 82% normal)      │
│  📊 [View Details]  ✓ [Acknowledge]  [Snooze]             │
│                                                            │
│  [🟡 MEDIUM] Sentiment Shift - 3 days ago                  │
│  ───────────────────────────────────────────────────────── │
│  Your journal entries are more negative (-0.45 vs +0.2)    │
│  Topics: stress, overwhelmed, can't focus                  │
│  📊 [View Details]  ✓ [Acknowledge]  [Snooze]             │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

### Quick Stats Summary
```
┌─────────────────┬─────────────────┬─────────────────┐
│  This Week      │  vs Baseline    │  Trend          │
├─────────────────┼─────────────────┼─────────────────┤
│  Sleep:         │  -47%           │  ↓ Declining    │
│  6.2 hours      │  (vs 11.7h)     │  Last 5 days    │
├─────────────────┼─────────────────┼─────────────────┤
│  Tasks:         │  -35%           │  ↓ Declining    │
│  47% complete   │  (vs 82%)       │  Last 3 days    │
├─────────────────┼─────────────────┼─────────────────┤
│  Sentiment:     │  -0.65          │  ↓ More Negative│
│  -0.45          │  (vs +0.2)      │  Consistent     │
└─────────────────┴─────────────────┴─────────────────┘
```

---

## 7. Sleep Logging Modal

### Log Sleep Entry
```
┌────────────────────────────────────────┐
│  📊 Log Sleep Entry                    │
│                                        │
│  Date: [May 28, 2024] 📅               │
│                                        │
│  Bedtime: [11:30 PM] 🕛                │
│                                        │
│  Wake Time: [7:00 AM] ⏰                │
│                                        │
│  Duration: 7h 30m (auto-calculated)    │
│                                        │
│  Quality Rating:                       │
│  ☆ ☆ ☆ ☆ ☆ (1-10)                     │
│      👆 Click to rate                  │
│                                        │
│  Notes (optional):                     │
│  [ Woke up twice, bad dreams ] 📝      │
│                                        │
│  [Cancel]  [Save]                      │
└────────────────────────────────────────┘
```

---

## 8. Journal Entry Screen

### Write Journal Entry
```
┌────────────────────────────────────────────────────────────┐
│  📝 Journal Entry                                          │
│                                                            │
│  Date: [May 29, 2024] 📅                                   │
│                                                            │
│  ┌────────────────────────────────────────────────────┐   │
│  │ Today was really stressful. I couldn't focus on my  │   │
│  │ work because I was thinking too much about the      │   │
│  │ upcoming presentation. I only slept 5 hours last    │   │
│  │ night. Feeling overwhelmed and anxious.             │   │
│  │                                                     │   │
│  │                                                     │   │
│  │                                                     │   │
│  │                                                     │   │
│  │ Word count: 64  Sentiment: -0.45 (Negative)         │   │
│  │ Topics detected: stress, sleep, focus, anxiety      │   │
│  └────────────────────────────────────────────────────┘   │
│                                                            │
│  ℹ️  Your words are encrypted. Only you can read them.    │
│                                                            │
│  [Cancel]  [Save Draft]  [Publish]                         │
└────────────────────────────────────────────────────────────┘
```

---

## 9. Task Tracking View

### Today's Tasks
```
┌────────────────────────────────────────────────────────────┐
│  📋 Today's Tasks                                          │
│                                                            │
│  Completion: 5 / 8 (62%)  ████████░░░░░░░░ 62%            │
│                                                            │
│  ☑️  Morning routine                  (Done)               │
│  ☐  Respond to emails                 (Pending)            │
│  ☑️  Team meeting at 10am              (Done)               │
│  ☐  Report due by EOD                 (High Priority)      │
│  ☑️  Lunch break                       (Done)               │
│  ☐  Code review                       (Pending)            │
│  ☑️  Walk outside                      (Done)               │
│  ☑️  Dinner                            (Done)               │
│                                                            │
│  ⚠️  Incomplete tasks affect your productivity score       │
│      This might trigger an activity drift alert if it      │
│      continues                                             │
│                                                            │
│  [+ Add Task]  [View History]                              │
└────────────────────────────────────────────────────────────┘
```

---

## 10. Alert Details Modal

### Detailed Alert Breakdown
```
┌────────────────────────────────────────────────────────────┐
│  Alert Details: Sleep Drift                               │
│  [🔴 HIGH SEVERITY]                                        │
│                                                            │
│  🎯 What This Means:                                       │
│  Your sleep duration has dropped significantly below your  │
│  normal baseline. This has been consistent for 5 days,     │
│  which suggests a pattern rather than a one-off event.     │
│                                                            │
│  📊 The Numbers:                                           │
│  • Your normal sleep: 7.5 hours/night                      │
│  • Recent average: 4.2 hours/night                         │
│  • Difference: -47%                                        │
│  • Days in drift: 5                                        │
│  • Pattern confidence: 91%                                 │
│                                                            │
│  ⚠️  Why This Matters:                                      │
│  Sleep is foundational to mental health. When sleep drops  │
│  significantly, it often correlates with:                  │
│  • Increased stress and anxiety                            │
│  • Reduced ability to handle challenges                    │
│  • Lower mood and motivation                               │
│                                                            │
│  💡 Suggestions:                                           │
│  1. What's changed? New deadline, relationship issue, etc?│
│  2. Try sleep hygiene: consistent bedtime, no screens      │
│  3. Share this with your healthcare provider               │
│                                                            │
│  ⚠️  This is NOT a diagnosis. Mental health professionals  │
│     can help you address the root causes.                  │
│                                                            │
│  [Share with Provider]  [Acknowledge]  [Close]             │
└────────────────────────────────────────────────────────────┘
```

---

## 11. Settings Page

### Data Privacy Controls
```
┌────────────────────────────────────────────────────────────┐
│  ⚙️  Settings                                              │
│                                                            │
│  📊 Data Management                                        │
│  ──────────────────────────────────────────────────────── │
│  ☑️  Track Sleep Logs                                      │
│      Collect and analyze your sleep data                   │
│  ☑️  Track Task Completion                                 │
│      Monitor productivity patterns                         │
│  ☑️  Track Journal Entries                                 │
│      Analyze sentiment and topics                          │
│  ☐  Disable ML Analysis                                    │
│      Keep data but don't run drift detection               │
│                                                            │
│  🔒 Privacy & Export                                       │
│  ──────────────────────────────────────────────────────── │
│  [📥 Download My Data]                                     │
│    Export all your data in JSON format                     │
│                                                            │
│  [🗑️  Delete My Account]                                   │
│    Permanently delete all data (30-day recovery window)    │
│                                                            │
│  🔔 Notifications                                          │
│  ──────────────────────────────────────────────────────── │
│  ☑️  Email alerts (High-risk only)                         │
│  ☑️  In-app alerts (All severity levels)                   │
│  ☐  SMS alerts (Coming soon)                               │
│                                                            │
│  [View Privacy Policy]  [View Terms of Service]            │
│  [Contact Support]      [About Mental Drift]               │
└────────────────────────────────────────────────────────────┘
```

---

## 12. Crisis Support Modal

### High-Risk Alert with Resources
```
┌────────────────────────────────────────────────────────────┐
│  ⚠️  URGENT SUPPORT NEEDED                                 │
│  (Triggered when risk_score > 75)                          │
│                                                            │
│  Your patterns suggest you might be struggling.            │
│  Professional support can help.                            │
│                                                            │
│  📞 Crisis Resources (Available 24/7):                     │
│                                                            │
│  🆘 [CALL 988 - Suicide & Crisis Lifeline]                 │
│     Available 24/7 for anyone in crisis                    │
│                                                            │
│  💬 [TEXT 741741 - Crisis Text Line]                       │
│     Text HOME to get support                               │
│                                                            │
│  🏥 [EMERGENCY: 911 or Go to ER]                           │
│     If you're in immediate danger                          │
│                                                            │
│  🧑‍⚕️  Find a Mental Health Provider:                        │
│  [Psychology Today] [SAMHSA Locator] [BetterHelp]          │
│                                                            │
│  ✉️  Share with Someone You Trust:                         │
│  [📧 Email Alert to Friend/Family Member]                  │
│                                                            │
│  💡 Remember:                                              │
│  • This app is NOT a crisis service                        │
│  • A human can help more than an algorithm                 │
│  • You deserve professional support                        │
│  • Your struggles are valid and fixable                    │
│                                                            │
│  [I'm Safe, Close]  [Share with Provider]                  │
└────────────────────────────────────────────────────────────┘
```

---

## 13. Component Library

### Reusable Components

```typescript
// Button Variants
<Button variant="primary">Primary Action</Button>
<Button variant="secondary">Secondary Action</Button>
<Button variant="danger">Destructive Action</Button>
<Button variant="ghost">Ghost Action</Button>

// Alert Cards
<AlertCard severity="low" title="Low Risk" />
<AlertCard severity="medium" title="Medium Risk" />
<AlertCard severity="high" title="High Risk" />

// Metrics Display
<MetricCard label="Sleep" value="6.5h" trend="down" />
<MetricCard label="Tasks" value="65%" trend="stable" />
<MetricCard label="Sentiment" value="-0.3" trend="down" />

// Progress Bars
<ProgressBar value={65} label="Baseline Collection" />
<ProgressBar value={42} label="Risk Score" color="warning" />

// Modal Components
<Modal title="Log Sleep" open={isOpen} onClose={handleClose}>
  {children}
</Modal>

// Form Inputs
<Input type="email" placeholder="Enter email" />
<Input type="password" placeholder="Enter password" />
<DatePicker label="Select date" />
<RatingPicker min={1} max={10} />

// Data Visualization
<LineChart data={sleepData} xAxis="date" yAxis="duration" />
<BarChart data={taskData} />
<DonutChart data={riskComponents} />

// Loading States
<Skeleton height="200px" /> // For placeholders
<LoadingSpinner /> // For async operations
```

---

## 14. Design System

### Color Palette
```
Primary (Actions):
- Light: #3B82F6 (Blue)
- Dark: #1E40AF

Alert Levels:
- Low:    #10B981 (Green)
- Medium: #F59E0B (Amber)
- High:   #EF4444 (Red)

Neutral:
- Background: #FFFFFF
- Surface:    #F9FAFB
- Border:     #E5E7EB
- Text:       #1F2937
- Muted:      #6B7280

Status:
- Success:    #10B981
- Warning:    #F59E0B
- Error:      #EF4444
- Info:       #3B82F6
```

### Typography
```
Headings:
- H1: 32px, bold, dark gray
- H2: 24px, bold, dark gray
- H3: 20px, semibold, dark gray

Body:
- Default: 16px, regular, dark gray
- Small:   14px, regular, medium gray
- Tiny:    12px, regular, light gray

Mono (for data):
- Baseline: Monaco, 14px
- Alert Values: Monaco, 16px
```

### Spacing Scale
```
4px  - xs
8px  - sm
12px - md
16px - lg
24px - xl
32px - 2xl
48px - 3xl
```

### Shadow Depths
```
sm:  0 1px 2px 0 rgba(0, 0, 0, 0.05)
md:  0 4px 6px -1px rgba(0, 0, 0, 0.1)
lg:  0 10px 15px -3px rgba(0, 0, 0, 0.1)
xl:  0 20px 25px -5px rgba(0, 0, 0, 0.1)
```

---

## 15. Responsive Breakpoints

```
Mobile:    320px - 640px
Tablet:    640px - 1024px
Desktop:   1024px - 1536px
Widescreen: 1536px+
```

### Mobile-First Strategy
- Stack cards vertically
- Single-column layout
- Larger touch targets (48px)
- Simplified navigation

### Tablet Optimization
- Two-column layouts
- Side navigation drawer
- Expanded data tables

### Desktop Enhancement
- Three+ column layouts
- Fixed sidebar
- Full data visualization
- Advanced filtering

---

## 16. Accessibility Requirements

### WCAG 2.1 AA Compliance
- ✅ Color contrast ratio ≥ 4.5:1 for text
- ✅ Keyboard navigation support
- ✅ Screen reader compatibility (aria labels)
- ✅ Focus indicators on all interactive elements
- ✅ Alt text for all images
- ✅ Semantic HTML structure
- ✅ Error messages clearly marked

### Key Practices
```jsx
// Example: Accessible Button
<button 
  aria-label="Log sleep entry"
  className="focus:outline-none focus:ring-2 focus:ring-blue-500"
>
  + Log Sleep
</button>

// Example: Accessible Alert
<div role="alert" aria-live="polite" aria-label="High risk alert">
  Your sleep is 47% below baseline
</div>

// Example: Accessible Form
<label htmlFor="sleep-quality">
  Sleep Quality Rating (1-10)
</label>
<input 
  id="sleep-quality"
  type="number"
  min="1"
  max="10"
  aria-describedby="quality-help"
/>
<small id="quality-help">Rate your sleep quality from 1 (poor) to 10 (excellent)</small>
```

---

## 17. Interaction Patterns

### Loading States
- Skeleton screens for data lists
- Spinners for async operations
- Progress bars for long operations
- Disabled buttons during submission

### Error Handling
- Inline error messages next to fields
- Toast notifications for global errors
- Retry buttons for failed requests
- Clear explanations of what went wrong

### Success Feedback
- Toast confirmation messages
- Checkmarks on completed actions
- Page transitions with animations
- Updated data immediately

### Empty States
- Helpful illustrations
- Clear call-to-action
- Tips for getting started
- Example data if applicable

---

## 18. Security & Privacy in UI

### What NOT to Display
- ❌ Never show passwords
- ❌ Never show unencrypted journal text in lists
- ❌ Never show email in plain text (use masking)
- ❌ Never cache sensitive data in browser

### Privacy Indicators
- 🔒 Display lock icon next to encrypted fields
- 🔐 Show encryption status in settings
- 📋 Clear data retention policy in help
- ⚠️ Consent reminders before sensitive actions

---

**Frontend wireframes are complete and ready for implementation!**