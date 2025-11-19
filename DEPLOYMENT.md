# Deployment Guide - Integrated SuperMarket Management System

## Overview
This guide covers deploying the mart1 project with:
- **Frontend (React)** → Vercel/Netlify
- **Backend (FastAPI)** → Railway/Render/Google Cloud
- **Database** → Already on Supabase ✅

---

## Prerequisites
- GitHub account (already done ✅)
- Vercel account (free tier)
- Railway/Render account (free tier)
- Your Supabase database credentials

---

## Option 1: Vercel (Frontend) + Railway (Backend) - RECOMMENDED 🚀

### **A. Deploy Backend to Railway**

1. **Sign up at Railway**: https://railway.app/
   - Login with GitHub

2. **Create New Project**:
   - Click "New Project"
   - Select "Deploy from GitHub repo"
   - Choose your `mart1` repository

3. **Configure Environment Variables**:
   ```env
   PGHOST=aws-1-ap-southeast-2.pooler.supabase.com
   PGDATABASE=postgres
   PGUSER=postgres.xfdnitgnewcjicojwpdp
   PGPASSWORD=your_supabase_password
   PGPORT=5432
   ```

4. **Railway will auto-detect** your Python app and:
   - Install dependencies from `requirements.txt`
   - Run the Procfile command
   - Assign a public URL like: `https://mart1-backend-production.up.railway.app`

5. **Copy your Railway backend URL** (you'll need it for frontend)

---

### **B. Deploy Frontend to Vercel**

1. **Sign up at Vercel**: https://vercel.com/
   - Login with GitHub

2. **Import Repository**:
   - Click "Add New" → "Project"
   - Select your `mart1` repository
   - Framework Preset: **Vite**
   - Root Directory: `frontend`

3. **Configure Build Settings**:
   ```
   Build Command: npm run build
   Output Directory: dist
   Install Command: npm install
   ```

4. **Add Environment Variable**:
   ```
   VITE_API_URL=https://your-railway-backend-url.up.railway.app
   ```

5. **Deploy** - Vercel will give you a URL like: `https://mart1.vercel.app`

---

## Option 2: Render (All-in-One)

### **A. Deploy Backend**

1. **Sign up at Render**: https://render.com/
   
2. **New Web Service**:
   - Connect GitHub repository
   - Name: `mart1-backend`
   - Environment: `Python 3`
   - Build Command: `pip install -r requirements.txt`
   - Start Command: `uvicorn api_server:app --host 0.0.0.0 --port $PORT`

3. **Environment Variables** (same as Railway)

4. **Free tier**: Auto-sleeps after 15 mins of inactivity

---

### **B. Deploy Frontend on Render**

1. **New Static Site**:
   - Connect repository
   - Name: `mart1-frontend`
   - Build Command: `cd frontend && npm install && npm run build`
   - Publish Directory: `frontend/dist`

2. **Environment Variable**:
   ```
   VITE_API_URL=https://mart1-backend.onrender.com
   ```

---

## Option 3: Google Cloud Platform (Production-Grade)

### **Backend on Cloud Run**

1. **Install Google Cloud SDK**:
   ```powershell
   # Download from: https://cloud.google.com/sdk/docs/install
   ```

2. **Create Dockerfile** (in mart1 root):
   ```dockerfile
   FROM python:3.11-slim
   
   WORKDIR /app
   
   COPY requirements.txt .
   RUN pip install --no-cache-dir -r requirements.txt
   
   COPY . .
   
   CMD ["uvicorn", "api_server:app", "--host", "0.0.0.0", "--port", "8080"]
   ```

3. **Deploy**:
   ```powershell
   gcloud init
   gcloud run deploy mart1-backend --source . --platform managed --region us-central1 --allow-unauthenticated
   ```

4. **Set Environment Variables** in Cloud Run console

---

### **Frontend on Firebase Hosting**

1. **Install Firebase CLI**:
   ```powershell
   npm install -g firebase-tools
   ```

2. **Initialize Firebase**:
   ```powershell
   cd frontend
   firebase login
   firebase init hosting
   ```

3. **Build and Deploy**:
   ```powershell
   npm run build
   firebase deploy
   ```

---

## Option 4: AWS (Enterprise-Grade)

### **Backend: Elastic Beanstalk**
### **Frontend: S3 + CloudFront**

*(More complex - recommended only if you need AWS ecosystem)*

---

## Quick Deploy Steps (Railway + Vercel) ⚡

### **1. Push Latest Changes**
```powershell
cd mart1
git add .
git commit -m "Add deployment configuration"
git push origin master
```

### **2. Railway (Backend) - 5 minutes**
- Go to railway.app → New Project → Deploy from GitHub
- Select `mart1` repo
- Add Supabase environment variables
- Copy backend URL

### **3. Vercel (Frontend) - 3 minutes**
- Go to vercel.com → New Project → Import Git Repository
- Root Directory: `frontend`
- Add environment variable: `VITE_API_URL=<railway-url>`
- Deploy

### **4. Update CORS in Backend**
Add your Vercel URL to allowed origins in `api_server.py`:
```python
app.add_middleware(
    CORSMiddleware,
    allow_origins=[
        "http://localhost:5000",
        "https://mart1.vercel.app",  # Add your Vercel URL
        "https://your-custom-domain.com"
    ],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

---

## Post-Deployment Checklist

- [ ] Backend URL accessible (test: `https://your-backend.railway.app/docs`)
- [ ] Frontend loads correctly
- [ ] Login works
- [ ] Database connection successful
- [ ] CORS configured properly
- [ ] Environment variables set correctly
- [ ] API calls working from frontend to backend

---

## Costs Breakdown

### **Free Tier (Recommended for Students)**
- **Vercel**: 100 GB bandwidth/month, unlimited deployments
- **Railway**: $5 free credits/month (~500 hours)
- **Supabase**: 500 MB database, 1 GB bandwidth
- **Total**: ~$0-5/month

### **Production (Paid)**
- **Vercel Pro**: $20/month
- **Railway**: $5-20/month (based on usage)
- **Supabase Pro**: $25/month
- **Total**: ~$50-65/month

---

## Troubleshooting

### **Backend not starting:**
- Check Procfile is correct
- Verify Python version (3.11)
- Check environment variables

### **Frontend 404 errors:**
- Verify `VITE_API_URL` is set
- Check CORS configuration
- Ensure backend is running

### **Database connection failed:**
- Verify Supabase credentials
- Check if IP is whitelisted (Supabase allows all by default)
- Test connection locally first

---

## Monitoring & Logs

### **Railway:**
- Dashboard → Your Project → Logs (real-time)

### **Vercel:**
- Dashboard → Your Project → Deployments → View Logs

### **Database:**
- Supabase Dashboard → Database → Query Performance

---

## Domain Configuration (Optional)

### **Custom Domain on Vercel:**
1. Go to Project Settings → Domains
2. Add your domain (e.g., `mysupermarket.com`)
3. Update DNS records as instructed

### **Custom Domain on Railway:**
1. Project Settings → Domains
2. Add custom domain
3. Update DNS CNAME record

---

## SSL/HTTPS

✅ **Automatic on all platforms!**
- Vercel: Free SSL via Let's Encrypt
- Railway: Automatic HTTPS
- Render: Free SSL included

---

## Scaling Tips

1. **Database Connection Pooling**: Already configured in Supabase ✅
2. **API Caching**: Add Redis for frequently accessed data
3. **CDN**: Vercel includes CDN automatically ✅
4. **Load Balancing**: Available on paid plans

---

## Recommended: Start with Railway + Vercel

**Why?**
- ✅ Completely free to start
- ✅ GitHub integration (auto-deploy on push)
- ✅ Easy environment variable management
- ✅ Built-in monitoring and logs
- ✅ Scale up as needed
- ✅ Custom domains supported

**Total Setup Time: 10-15 minutes** 🚀
