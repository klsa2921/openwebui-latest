# Open WebUI Setup Guide - Frontend & Backend

This document outlines all the steps and fixes required to successfully run both the frontend and backend servers for Open WebUI.

## Prerequisites

- Node.js and npm installed
- Python with Miniconda/Anaconda
- Conda environment named `spectra-lite` with Python dependencies

## Frontend Setup

### 1. Install Dependencies
```bash
npm install
```

### 2. Fix PostCSS Configuration (Critical Fix)
**Issue:** TailwindCSS v4 compatibility issue with PostCSS plugin configuration on Windows.

**File:** `postcss.config.js`

**Original Configuration:**
```javascript
export default {
  plugins: {
    '@tailwindcss/postcss': {},
    autoprefixer: {}
  }
}
```

**Fixed Configuration:**
```javascript
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {}
  }
}
```

**Why:** The `@tailwindcss/postcss` plugin format is not compatible with Windows environments. Using the traditional `tailwindcss` plugin resolves the compatibility issue.

### 3. Start Frontend Server
```bash
npm run dev
```
- Runs on: http://localhost:5173
- Uses Vite development server with SvelteKit
- Includes Pyodide integration for code execution

## Backend Setup

### 1. Activate Conda Environment
```bash
conda activate spectra-lite
```

### 2. Install Backend Dependencies
```bash
cd backend
pip install -r requirements.txt
```

### 3. Create Data Directory (Critical Fix)
**Issue:** Database initialization failed due to missing data directory.

```bash
cd backend
mkdir data
```

**Why:** The SQLite database requires the `backend/data` directory to exist for storing `webui.db` and other application data.

### 4. Configure CORS (Critical Fix)
**Issue:** Frontend couldn't communicate with backend due to CORS policy restrictions.

**File:** `.env`

**Added Configuration:**
```env
CORS_ALLOW_ORIGIN=http://localhost:5173
```

**Why:** The backend was using wildcard '*' for CORS, but when frontend makes requests with credentials, CORS requires a specific origin to be specified.

### 5. Start Backend Server
```bash
cd backend
set PYTHONPATH=.
python -m uvicorn open_webui.main:app --host 0.0.0.0 --port 8080
```

**PowerShell version:**
```powershell
cd backend
$env:PYTHONPATH = "."
C:\Users\aiuser9\Miniconda3\envs\spectra-lite\python.exe -m uvicorn open_webui.main:app --host 0.0.0.0 --port 8080
```

- Runs on: http://localhost:8080
- Uses FastAPI with uvicorn
- Automatically runs database migrations on startup

## Key Issues Resolved

### 1. PostCSS/TailwindCSS Compatibility
- **Problem:** Build process failed due to incompatible PostCSS plugin configuration
- **Solution:** Changed from `@tailwindcss/postcss` to `tailwindcss` plugin
- **Impact:** Enabled successful frontend build and development server startup

### 2. Database Initialization
- **Problem:** Backend crashed with "unable to open database file" error
- **Solution:** Created the required `backend/data` directory
- **Impact:** Allowed SQLite database creation and migration execution

### 3. CORS Configuration
- **Problem:** Frontend requests blocked by CORS policy
- **Solution:** Set specific origin in `CORS_ALLOW_ORIGIN` environment variable
- **Impact:** Enabled frontend-backend communication

### 4. Environment Setup
- **Problem:** Python path and conda environment issues
- **Solution:** Proper PYTHONPATH setting and conda environment activation
- **Impact:** Correct module resolution and dependency access

## Final Server Status

### Frontend Server
- **URL:** http://localhost:5173
- **Status:** ✅ Running
- **Features:** SvelteKit, TailwindCSS, Pyodide integration

### Backend Server
- **URL:** http://localhost:8080
- **Status:** ✅ Running
- **Features:** FastAPI, SQLite database, CORS enabled

### Health Check
```bash
curl http://localhost:8080/health
# Should return: HTTP 200 OK
```

## Troubleshooting

### Frontend Issues
- If build fails, check PostCSS configuration
- Ensure all npm dependencies are installed
- Verify Node.js version compatibility

### Backend Issues
- Ensure conda environment is activated
- Check if data directory exists
- Verify CORS configuration in .env file
- Confirm database permissions

### CORS Issues
- Ensure CORS_ALLOW_ORIGIN matches frontend URL exactly
- Restart backend server after changing CORS settings
- Check browser console for specific CORS error messages

## Development Workflow

1. Start backend server first (takes longer to initialize)
2. Start frontend server
3. Access application at http://localhost:5173
4. Backend API available at http://localhost:8080

## File Changes Summary

1. **postcss.config.js** - Fixed TailwindCSS plugin configuration
2. **.env** - Added CORS_ALLOW_ORIGIN setting
3. **backend/data/** - Created directory for database storage

These changes ensure both servers can run simultaneously and communicate properly for full application functionality.
