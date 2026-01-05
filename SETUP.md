# OnlyFrans Setup Guide

## Step 1: Create Supabase Project

1. Go to [supabase.com](https://supabase.com) and sign in
2. Click "New Project"
3. Choose your organization
4. Enter project details:
   - **Name**: `onlyfrans` (or whatever you prefer)
   - **Database Password**: Create a strong password (save this!)
   - **Region**: Choose closest to your users
5. Click "Create new project" and wait for it to initialize (~2 minutes)

## Step 2: Create Database Table

1. In your Supabase dashboard, click **SQL Editor** in the left sidebar
2. Click "New query"
3. Paste this SQL and click "Run":

```sql
-- Create the memories table
CREATE TABLE memories (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  name TEXT NOT NULL,
  year_met INTEGER NOT NULL,
  message TEXT NOT NULL,
  photo_url TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Enable Row Level Security
ALTER TABLE memories ENABLE ROW LEVEL SECURITY;

-- Policy: Allow anyone to insert
CREATE POLICY "Allow public insert" ON memories
  FOR INSERT TO anon
  WITH CHECK (true);

-- Policy: Allow anyone to read (for admin, we'll verify password server-side)
CREATE POLICY "Allow public read" ON memories
  FOR SELECT TO anon
  USING (true);
```

## Step 3: Create Storage Bucket for Photos

1. In Supabase dashboard, click **Storage** in the left sidebar
2. Click "New bucket"
3. Enter:
   - **Name**: `photos`
   - **Public bucket**: Toggle ON (checked)
4. Click "Create bucket"

5. Now set up storage policies. Click on the `photos` bucket, then click "Policies"
6. Click "New policy" and select "For full customization"
7. Create this policy for uploads:
   - **Policy name**: `Allow public uploads`
   - **Allowed operations**: INSERT
   - **Target roles**: anon
   - **Policy definition**: `true`
   - Click "Review" then "Save policy"

8. Create another policy for reading:
   - **Policy name**: `Allow public read`
   - **Allowed operations**: SELECT
   - **Target roles**: anon
   - **Policy definition**: `true`
   - Click "Review" then "Save policy"

## Step 4: Get Your Supabase Credentials

1. In Supabase dashboard, click **Settings** (gear icon) in the left sidebar
2. Click **API** under "Configuration"
3. Copy these values:
   - **Project URL** (looks like `https://xxxxx.supabase.co`)
   - **anon public** key (under "Project API keys")

## Step 5: Deploy to Vercel

1. Push your code to a GitHub repository:
   ```bash
   cd /Users/thomasprice/Documents/dev/Personal/onlyfrans
   git init
   git add .
   git commit -m "Initial commit"
   ```

2. Go to [github.com](https://github.com) and create a new repository called `onlyfrans`

3. Push to GitHub:
   ```bash
   git remote add origin https://github.com/YOUR_USERNAME/onlyfrans.git
   git branch -M main
   git push -u origin main
   ```

4. Go to [vercel.com](https://vercel.com) and sign in (use GitHub account)

5. Click "Add New..." → "Project"

6. Import your `onlyfrans` repository from GitHub

7. Before deploying, add Environment Variables:
   - Click "Environment Variables"
   - Add these three variables:

   | Name | Value |
   |------|-------|
   | `NEXT_PUBLIC_SUPABASE_URL` | Your Supabase Project URL |
   | `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Your Supabase anon key |
   | `ADMIN_PASSWORD` | `onlyfrans1` |

8. Click "Deploy"

## Step 6: Set Custom Domain

1. After deployment, go to your project in Vercel
2. Click "Settings" → "Domains"
3. You'll see your project has a `.vercel.app` domain
4. In the domain input, type: `onlyfrans.vercel.app`
5. Click "Add"
6. If `onlyfrans.vercel.app` is available, it will be assigned

**Note**: If `onlyfrans.vercel.app` is taken, try variations like `onlyfrans-app.vercel.app`

## Usage

- **Main form**: `https://onlyfrans.vercel.app/`
- **Admin panel**: `https://onlyfrans.vercel.app/admin` (password: `onlyfrans1`)

## Local Development

1. Copy `.env.example` to `.env.local`:
   ```bash
   cp .env.example .env.local
   ```

2. Edit `.env.local` with your Supabase credentials

3. Install dependencies and run:
   ```bash
   npm install
   npm run dev
   ```

4. Open [http://localhost:3000](http://localhost:3000)
