# GearGuard Deployment Guide

## Problem
GitHub Pages only hosts **static files**. Your frontend (React) is deployed there successfully, but the **backend API needs to be hosted separately** on a service with compute capabilities.

## Solution Overview

### 1. Frontend (GitHub Pages) ✅ 
- Static files are built and located in `/docs/`
- Deployed automatically via GitHub Pages
- Frontend configured to use environment variables for API URL

### 2. Backend (Needs Deployment) ⚠️
The FastAPI backend must be deployed to one of these services:

## Recommended Options

### Option A: Railway (Recommended - Simplest)
Railway is the easiest option for deploying Python FastAPI backends.

**Steps:**
1. Go to [railway.app](https://railway.app)
2. Sign up with GitHub
3. Create a new project → Import from GitHub
4. Select your GearGuard repository
5. Configure the service:
   - Service: Python
   - Build command: `pip install -r requirements.txt`
   - Start command: `uvicorn main:app --host 0.0.0.0 --port $PORT`
6. Add PostgreSQL database plugin
7. Set environment variables:
   - `SQLALCHEMY_DATABASE_URL`: Connection string from PostgreSQL plugin
   - `FRONTEND_URL`: Your GitHub Pages URL

**Frontend Configuration:**
Update `.env.production` with your Railway backend URL:
```
VITE_API_BASE_URL=https://your-railway-app.up.railway.app
```

### Option B: Render
1. Go to [render.com](https://render.com)
2. Create account and connect GitHub
3. New → Web Service
4. Choose your repository
5. Configure:
   - Environment: Python 3
   - Build command: `pip install -r requirements.txt`
   - Start command: `uvicorn main:app --host 0.0.0.0 --port $PORT`
6. Add PostgreSQL
7. Set environment variables

### Option C: Heroku (Legacy - May Charge)
Heroku removed free tier as of November 2022.

## Database Setup

### For Railway/Render:
- These services provide managed PostgreSQL
- Copy your connection string to `SQLALCHEMY_DATABASE_URL`
- Database migrations run automatically on startup

### For Local Development:
```bash
createdb gearguard_db
export SQLALCHEMY_DATABASE_URL="postgresql://user:password@localhost/gearguard_db"
```

## Setting API URL Per Environment

### Local Development (.env):
```
VITE_API_BASE_URL=http://127.0.0.1:8000
```

### Production (.env.production):
```
VITE_API_BASE_URL=https://your-backend-url.com
```

Vite automatically uses `.env.production` when you run `npm run build`.

## Deployment Verification Checklist

- [ ] Backend runs on Railway/Render
- [ ] PostgreSQL database is created and accessible
- [ ] CORS enabled on backend (allow frontend origin)
- [ ] Frontend `.env.production` has correct backend URL
- [ ] Run `npm run build` locally to test
- [ ] Commit all changes to git
- [ ] Frontend builds deploy via GitHub Pages
- [ ] Can login from deployed website
- [ ] Backend API calls work from deployed frontend

## Troubleshooting

### "Backend not reachable" Error
- Check backend is running on Railway/Render
- Verify `VITE_API_BASE_URL` in `.env.production`
- Check CORS settings in backend
- Open browser DevTools → Network tab → see actual API URL being called

### Database Connection Error
- Verify PostgreSQL is running
- Check `SQLALCHEMY_DATABASE_URL` environment variable
- Ensure database exists and user has permission

### CORS Error
Backend CORS is configured to allow all origins. If restrictive, update backend `main.py` CORS settings.

## Quick Deploy Commands

```bash
# Build frontend
cd frontend
npm install
npm run build

# Test locally before committing
cd ../backend
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python -m uvicorn main:app --reload

# Git push
git add .
git commit -m "Deploy: Configure API for production"
git push origin main
```

## Next Steps

1. Choose deployment platform (Railway recommended)
2. Deploy backend to chosen platform
3. Update `.env.production` with backend URL
4. Run `npm run build`
5. Commit and push to GitHub
6. Access site at `https://username.github.io/GearGaurd-The-Ultimate-Maintenance-Tracker/`
