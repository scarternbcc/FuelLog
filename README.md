# FuelLog 🚗⛽

A mobile-first PWA for tracking fuel economy across multiple vehicles. Stores data securely in Supabase with per-user authentication.

---

## Setup Guide

### Step 1 — Create a Supabase Project

1. Go to [supabase.com](https://supabase.com) and sign up for a free account
2. Click **New Project**, give it a name (e.g. `fuellog`), set a database password, choose a region
3. Wait ~2 minutes for provisioning

---

### Step 2 — Run the Database Setup Script

1. In your Supabase project, click **SQL Editor** in the left sidebar
2. Click **New Query** and paste the following SQL, then click **Run**:

```sql
-- Create vehicles table
create table vehicles (
  id uuid default gen_random_uuid() primary key,
  user_id uuid references auth.users not null,
  nickname text,
  year text,
  make text,
  model text,
  fuel text default 'regular',
  created_at timestamptz default now()
);

-- Create fillups table
create table fillups (
  id uuid default gen_random_uuid() primary key,
  user_id uuid references auth.users not null,
  vehicle_id uuid references vehicles(id) on delete cascade,
  date date not null,
  odometer numeric not null,
  gallons numeric not null,
  price numeric,
  notes text,
  created_at timestamptz default now()
);

-- Enable Row Level Security (keeps each user's data private)
alter table vehicles enable row level security;
alter table fillups enable row level security;

-- Policies: users can only access their own data
create policy "users own vehicles" on vehicles
  for all using (auth.uid() = user_id)
  with check (auth.uid() = user_id);

create policy "users own fillups" on fillups
  for all using (auth.uid() = user_id)
  with check (auth.uid() = user_id);
```

---

### Step 3 — Get Your API Credentials

1. In Supabase, go to **Project Settings** → **API**
2. Copy:
   - **Project URL** (looks like `https://xxxxxxxxxxxx.supabase.co`)
   - **anon public** key (long JWT string under "Project API Keys")

---

### Step 4 — Deploy to GitHub Pages

1. Create a new **public** repository on GitHub (e.g. `fuellog`)
2. Upload `index.html` and `manifest.json` to the repo root
3. Go to **Settings** → **Pages**
4. Under **Source**, select `main` branch and `/ (root)`, click **Save**
5. Your app will be live at: `https://yourusername.github.io/fuellog`

> **Note:** You'll want app icons too. Add `icon-192.png` and `icon-512.png` (192×192 and 512×512 pixel PNG images) to the repo root. You can use any image editor or a free tool like [favicon.io](https://favicon.io) to generate them.

---

### Step 5 — Connect the App

1. Open your GitHub Pages URL in **Safari on your iPhone**
2. The app will show a **Supabase Setup** screen on first launch
3. Paste in your Project URL and anon key, tap **Connect**
4. Create accounts for both users via the **Create Account** tab
5. Each user signs in with their own email/password — data is completely separate

---

### Step 6 — Install on iPhone

1. In Safari, tap the **Share** button (box with arrow)
2. Scroll down and tap **"Add to Home Screen"**
3. Tap **Add**

FuelLog will appear on your home screen and run like a native app!

---

## Features

- **Multiple vehicles** — add as many as you need
- **Per-user data** — each account sees only their own vehicles and fill-ups
- **MPG tracking** — calculated automatically between consecutive fill-ups
- **Fuel economy gauge** — visual average MPG display
- **MPG trend chart** — see how your economy changes over time
- **CSV export** — download all your data any time
- **PWA** — installs on iPhone home screen, works offline for UI

## Data Privacy

- All data is stored in **your own Supabase project** — Supabase never shares it
- Row Level Security ensures users can **never access each other's data**, even if they tried
- Your Supabase anon key is safe to use client-side — it has no special privileges beyond what the RLS policies allow

## File Structure

```
fuellog/
├── index.html      ← The entire app
├── manifest.json   ← PWA configuration
├── icon-192.png    ← App icon (you provide)
├── icon-512.png    ← App icon (you provide)
└── README.md       ← This file
```
