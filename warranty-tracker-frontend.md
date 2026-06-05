

# Phase 1 - Project Setup

## Step 1: Open Project in VS Code

<img width="70%" alt="image" src="https://github.com/user-attachments/assets/104f9b5d-ffb8-4617-aabc-bca6eaa4b0e7" />

---

## Step 2: Install Tailwind CSS

```bash
npm install tailwindcss @tailwindcss/vite
```

<img width="70%" alt="image" src="https://github.com/user-attachments/assets/6d88b235-1999-4ab4-8d74-69d99f1279b5" />

Update `vite.config.ts`


```text
[Change 1]
Add Tailwind import

[Change 2]
Register Tailwind plugin

[Change 3]
Add /api proxy for backend APIs
```

Or directly in code:

```ts
// [Change 1] Tailwind Import
import react from '@vitejs/plugin-react';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
    // [Change 2] Register Tailwind Plugin
    plugins: [react(), tailwindcss()],

    server: {
        proxy: {
            '^/weatherforecast': {
                target,
                secure: false
            },

            // [Change 3] API Proxy
            '^/api': {
                target,
                secure: false,
                changeOrigin: true
            }
        }
    }
});
```

---

## Step 3: Update index.css

Open:

```text
src/index.css
```

Remove everything and add:

```css
@import "tailwindcss";
```

---

## Step 4: Verify

Open:

```tsx
src/App.tsx
```

Replace with:

```tsx
function App() {
  return (
    <div className="min-h-screen bg-slate-100 flex items-center justify-center">
      <h1 className="text-5xl font-bold text-blue-600">
        Warranty Tracker
      </h1>
    </div>
  );
}

export default App;
```

---

## Step 5: Run

You should see:

```text
Warranty Tracker
```

in a large blue font centered on a light gray background.
<img width="90%" alt="image" src="https://github.com/user-attachments/assets/308e8de4-af6c-44c6-8004-cf98bd593f78" />

---

## Step 6: Install Additional Packages

For your Warranty Tracker UI:

```bash
npm install react-router-dom
npm install react-hook-form
npm install zod
npm install @hookform/resolvers
npm install lucide-react
```

Verify installation:

```bash
npm list tailwindcss
npm list react-router-dom
npm list react-hook-form
```

After Tailwind is working, the next step is to create the project structure:

```text
src/
├── components/
├── pages/
├── routes/
├── services/
├── types/
├── assets/
└── layouts/
```

Then we'll build the Navbar and Home Page first.

---

# Phase 2 - Create Folder Structure

Inside `src`

```text
src
│
├── assets
│
├── components
│   ├── common
│   └── layout
│
├── pages
│   ├── Home
│   ├── RegisterWarranty
│   ├── SearchWarranty
│   ├── RegistrationSuccess
│   └── WarrantyResults
│
├── routes
│
├── services
│
├── types
│
├── App.tsx
│
└── main.tsx
```

---


# Phase 3 - Setup Routing

Create:

```text
src/routes/AppRoutes.tsx
```

```tsx
import { Routes, Route } from "react-router-dom";

import HomePage from "../pages/Home/HomePage";
import RegisterWarrantyPage from "../pages/RegisterWarranty/RegisterWarrantyPage";
import SearchWarrantyPage from "../pages/SearchWarranty/SearchWarrantyPage";
import RegistrationSuccessPage from "../pages/RegistrationSuccess/RegistrationSuccessPage";
import WarrantyResultsPage from "../pages/WarrantyResults/WarrantyResultsPage";

export default function AppRoutes() {
  return (
    <Routes>
      <Route path="/" element={<HomePage />} />

      <Route path="/register" element={<RegisterWarrantyPage />} />

      <Route path="/search" element={<SearchWarrantyPage />} />

      <Route
        path="/success/:registrationId"
        element={<RegistrationSuccessPage />}
      />

      <Route
        path="/results"
        element={<WarrantyResultsPage />}
      />
    </Routes>
  );
}
```

---

Update `main.tsx`

```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import { BrowserRouter } from "react-router-dom";

import "./index.css";
import AppRoutes from "./routes/AppRoutes";

ReactDOM.createRoot(document.getElementById("root")!).render(
  <BrowserRouter>
    <AppRoutes />
  </BrowserRouter>
);
```

---

# Phase 4 - Build Layout

---

## Step 4: Create Navbar

```text
src/components/layout/Navbar.tsx
```

```tsx
import { Shield } from "lucide-react";
import { Link } from "react-router-dom";

export default function Navbar() {
  return (
    <nav className="border-b">
      <div className="max-w-7xl mx-auto px-6 py-4 flex justify-between">
        <div className="flex items-center gap-2">
          <Shield />
          <span className="font-bold">
            Warranty Tracker
          </span>
        </div>

        <div className="flex gap-8">
          <Link to="/">Home</Link>

          <Link to="/register">
            Register Warranty
          </Link>

          <Link to="/search">
            Search Warranty
          </Link>
        </div>
      </div>
    </nav>
  );
}
```

---

# Phase 5 - Create Home Page

Create:

```text
src/pages/Home/HomePage.tsx
```

Initially:

```tsx
import Navbar from "../../components/layout/Navbar";

export default function HomePage() {
  return (
    <>
      <Navbar />

      <div className="max-w-7xl mx-auto p-8">
        <h1 className="text-5xl font-bold">
          Warranty Tracker
        </h1>

        <p className="mt-4 text-gray-600">
          Register and track your warranties.
        </p>
      </div>
    </>
  );
}
```

Verify:

```bash
npm run dev
```

You should see:

```text
Navbar

Warranty Tracker Heading
```

---

# Phase 6 - Create Reusable Components

Before building forms, create:

```text
components/common
```

### Button

```text
Button.tsx
```

### Input

```text
Input.tsx
```

### Select

```text
Select.tsx
```

### Card

```text
Card.tsx
```

### StatusBadge

```text
StatusBadge.tsx
```

---

# Phase 7 - Build Home Screen Exactly Like UI

Next we build:

```text
Hero Section
```

```text
Action Cards

Register Warranty

Search Warranty
```

```text
Feature Section

Easy Registration

Track Anywhere

Secure & Reliable
```

This will complete Screen 1.

---

