# Mental Drift: Frontend Project Structure & Setup

**Framework:** Next.js 14 (React 18)
**Styling:** Tailwind CSS
**Language:** TypeScript
**State Management:** React Context + Hooks
**UI Components:** Custom + Headless UI

---

## Project Structure

```
mental-drift-frontend/
├── app/
│   ├── layout.tsx              # Root layout
│   ├── page.tsx                # Home/landing
│   ├── (auth)/
│   │   ├── signup/page.tsx
│   │   ├── login/page.tsx
│   │   ├── forgot-password/page.tsx
│   │   └── layout.tsx          # Auth layout (no sidebar)
│   ├── (app)/
│   │   ├── dashboard/page.tsx
│   │   ├── dashboard/sleep/page.tsx
│   │   ├── dashboard/tasks/page.tsx
│   │   ├── dashboard/journal/page.tsx
│   │   ├── dashboard/alerts/page.tsx
│   │   ├── dashboard/risk-assessment/page.tsx
│   │   ├── settings/page.tsx
│   │   ├── settings/privacy/page.tsx
│   │   ├── settings/data-export/page.tsx
│   │   └── layout.tsx          # App layout (with sidebar)
│   └── api/
│       ├── auth/route.ts
│       ├── sleep/route.ts
│       ├── tasks/route.ts
│       ├── journal/route.ts
│       └── predictions/route.ts
├── components/
│   ├── ui/
│   │   ├── Button.tsx
│   │   ├── Input.tsx
│   │   ├── Modal.tsx
│   │   ├── Card.tsx
│   │   ├── Alert.tsx
│   │   ├── Badge.tsx
│   │   ├── Spinner.tsx
│   │   └── ...
│   ├── layout/
│   │   ├── Navbar.tsx
│   │   ├── Sidebar.tsx
│   │   ├── Footer.tsx
│   │   └── Header.tsx
│   ├── dashboard/
│   │   ├── RiskAssessment.tsx
│   │   ├── DriftAlerts.tsx
│   │   ├── QuickStats.tsx
│   │   ├── BaselineProgress.tsx
│   │   └── RecentEntries.tsx
│   ├── forms/
│   │   ├── SleepLogForm.tsx
│   │   ├── JournalForm.tsx
│   │   ├── TaskForm.tsx
│   │   └── LoginForm.tsx
│   └── charts/
│       ├── SleepChart.tsx
│       ├── TaskChart.tsx
│       ├── SentimentChart.tsx
│       └── RiskChart.tsx
├── hooks/
│   ├── useAuth.ts
│   ├── useSleep.ts
│   ├── useJournal.ts
│   ├── useTasks.ts
│   ├── usePredictions.ts
│   └── useLocalStorage.ts
├── context/
│   ├── AuthContext.tsx
│   ├── UserContext.tsx
│   └── NotificationContext.tsx
├── types/
│   ├── index.ts
│   ├── api.ts
│   ├── user.ts
│   └── alert.ts
├── utils/
│   ├── api.ts              # API client
│   ├── encryption.ts       # Client-side crypto
│   ├── validation.ts       # Form validation
│   ├── formatters.ts       # Date, number formatting
│   └── constants.ts
├── styles/
│   ├── globals.css         # Global styles
│   └── variables.css       # CSS custom properties
├── public/
│   ├── images/
│   ├── icons/
│   └── fonts/
├── tailwind.config.ts
├── tsconfig.json
├── next.config.js
├── package.json
└── .env.example
```

---

## Package Dependencies

### package.json
```json
{
  "name": "mental-drift-frontend",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "type-check": "tsc --noEmit",
    "test": "jest",
    "test:watch": "jest --watch"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "next": "^14.0.0",
    "typescript": "^5.2.0",
    "@heroicons/react": "^2.0.18",
    "@headlessui/react": "^1.7.14",
    "tailwindcss": "^3.3.0",
    "recharts": "^2.10.0",
    "axios": "^1.4.0",
    "zustand": "^4.4.0",
    "react-hook-form": "^7.45.0",
    "zod": "^3.22.0",
    "js-cookie": "^3.0.5",
    "date-fns": "^2.30.0",
    "crypto-js": "^4.1.1",
    "clsx": "^2.0.0",
    "tailwind-merge": "^1.14.0"
  },
  "devDependencies": {
    "@types/react": "^18.2.0",
    "@types/node": "^20.4.0",
    "@types/js-cookie": "^3.0.3",
    "@testing-library/react": "^14.0.0",
    "@testing-library/jest-dom": "^6.1.0",
    "jest": "^29.6.0",
    "jest-environment-jsdom": "^29.6.0",
    "autoprefixer": "^10.4.14",
    "postcss": "^8.4.25",
    "eslint": "^8.44.0",
    "eslint-config-next": "^14.0.0"
  }
}
```

---

## Key Component Examples

### components/ui/Button.tsx
```typescript
import { ButtonHTMLAttributes, ReactNode } from 'react';

interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  children: ReactNode;
  variant?: 'primary' | 'secondary' | 'danger' | 'ghost';
  size?: 'sm' | 'md' | 'lg';
  loading?: boolean;
}

export function Button({
  children,
  variant = 'primary',
  size = 'md',
  loading = false,
  disabled,
  className,
  ...props
}: ButtonProps) {
  const baseStyles = 'font-medium rounded-lg transition-colors focus:outline-none focus:ring-2 focus:ring-offset-2';
  
  const variantStyles = {
    primary: 'bg-blue-600 text-white hover:bg-blue-700 focus:ring-blue-500',
    secondary: 'bg-gray-200 text-gray-900 hover:bg-gray-300 focus:ring-gray-500',
    danger: 'bg-red-600 text-white hover:bg-red-700 focus:ring-red-500',
    ghost: 'bg-transparent text-blue-600 hover:bg-blue-50 focus:ring-blue-500'
  };
  
  const sizeStyles = {
    sm: 'px-3 py-2 text-sm',
    md: 'px-4 py-2 text-base',
    lg: 'px-6 py-3 text-lg'
  };
  
  return (
    <button
      disabled={loading || disabled}
      className={`${baseStyles} ${variantStyles[variant]} ${sizeStyles[size]} ${loading ? 'opacity-70 cursor-not-allowed' : ''} ${className}`}
      {...props}
    >
      {loading ? '⏳ Loading...' : children}
    </button>
  );
}
```

### components/dashboard/RiskAssessment.tsx
```typescript
'use client';

import { usePredictions } from '@/hooks/usePredictions';
import { Card } from '@/components/ui/Card';
import { Alert } from '@/components/ui/Alert';
import { RiskChart } from '@/components/charts/RiskChart';

export function RiskAssessment() {
  const { prediction, loading, error } = usePredictions();

  if (loading) return <div>Loading...</div>;
  if (error) return <Alert type="error" message={error} />;
  if (!prediction) return null;

  const getSeverity = (score: number): 'low' | 'medium' | 'high' => {
    if (score < 30) return 'low';
    if (score < 60) return 'medium';
    return 'high';
  };

  const severity = getSeverity(prediction.risk_score);

  return (
    <Card className="p-6 border-2" variant={severity}>
      <h2 className="text-2xl font-bold mb-4">⚠️ Risk Assessment</h2>
      
      <div className="mb-6">
        <p className="text-sm text-gray-600">Overall Risk Score</p>
        <p className="text-4xl font-bold text-blue-600">{prediction.risk_score} / 100</p>
        <p className="text-lg font-semibold mt-2">
          {severity === 'low' && '🟢 LOW RISK'}
          {severity === 'medium' && '🟡 MEDIUM RISK'}
          {severity === 'high' && '🔴 HIGH RISK'}
        </p>
      </div>

      <RiskChart data={prediction} />

      <div className="mt-6">
        <h3 className="font-semibold mb-2">Recovery Suggestions:</h3>
        <ul className="list-disc list-inside space-y-1">
          {prediction.recovery_suggestions.map((suggestion, i) => (
            <li key={i} className="text-sm text-gray-700">{suggestion}</li>
          ))}
        </ul>
      </div>

      {severity === 'high' && (
        <Alert type="warning" className="mt-4">
          ⚠️ Your risk score is high. Please reach out to a healthcare provider.
          Crisis hotline: 988
        </Alert>
      )}
    </Card>
  );
}
```

### hooks/usePredictions.ts
```typescript
'use client';

import { useEffect, useState } from 'react';
import { useAuth } from './useAuth';

interface Prediction {
  risk_score: number;
  confidence: number;
  sleep_component: number;
  activity_component: number;
  sentiment_component: number;
  social_component: number;
  collapse_probability_7days: number;
  collapse_probability_14days: number;
  risk_factors: string[];
  recovery_suggestions: string[];
}

export function usePredictions() {
  const [prediction, setPrediction] = useState<Prediction | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);
  const { token } = useAuth();

  useEffect(() => {
    const fetchPrediction = async () => {
      if (!token) return;

      try {
        const response = await fetch('/api/predictions/collapse-risk', {
          headers: { 'Authorization': `Bearer ${token}` }
        });
        
        if (!response.ok) throw new Error('Failed to fetch prediction');
        const data = await response.json();
        setPrediction(data);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'Unknown error');
      } finally {
        setLoading(false);
      }
    };

    fetchPrediction();
  }, [token]);

  return { prediction, loading, error };
}
```

---

## Environment Configuration

### .env.example
```bash
NEXT_PUBLIC_API_URL=http://localhost:8000/api/v1
NEXT_PUBLIC_APP_NAME=Mental Drift
NEXT_PUBLIC_ENCRYPTION_ENABLED=true
```

---

## tailwind.config.ts
```typescript
import type { Config } from 'tailwindcss';

const config: Config = {
  content: [
    './app/**/*.{js,ts,jsx,tsx}',
    './components/**/*.{js,ts,jsx,tsx}',
  ],
  theme: {
    extend: {
      colors: {
        risk: {
          low: '#10B981',
          medium: '#F59E0B',
          high: '#EF4444',
        },
      },
      animation: {
        pulse: 'pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite',
      },
    },
  },
  plugins: [],
};

export default config;
```

---

## Installation & Setup

```bash
# Clone repository
git clone https://github.com/khangxD1304/mental-drift.git
cd mental-drift/frontend

# Install dependencies
npm install

# Setup environment
cp .env.example .env.local

# Run development server
npm run dev

# Build for production
npm run build
npm run start
```

Frontend is ready for component implementation!