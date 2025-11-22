# Render Deployment Guide

## ✅ Backend Deployment on Render

### What Was Fixed
- ✅ Updated `index.js` to use `process.env.PORT` instead of hardcoded port 5000
- ✅ This allows Render to dynamically assign the port

### Deployment Steps

1. **Push the updated code to GitHub**
   ```bash
   cd backend
   git add .
   git commit -m "Fix: Use PORT environment variable for Render"
   git push origin main
   ```

2. **Render will automatically redeploy** (if auto-deploy is enabled)
   - Or manually trigger a redeploy in Render dashboard

3. **Set Environment Variables in Render**
   - Go to your service dashboard
   - Navigate to **Environment** tab
   - Add these variables:
     - `DATABASE_URL`: Your PostgreSQL connection string
     - `GROQ_API_KEY`: Your Groq API key
     - `NODE_ENV`: production (optional)

4. **Your backend is now live at:**
   ```
   https://mini-code-copilot-backend.onrender.com
   ```

### Testing the Backend

Test your API endpoints:
```bash
# Test history endpoint
curl https://mini-code-copilot-backend.onrender.com/api/history

# Test with search
curl "https://mini-code-copilot-backend.onrender.com/api/history?search=function"
```

---

## 🎨 Frontend Deployment

### Step 1: Update API Base URL

Edit `frontend/src/api.ts`:
```typescript
const API_BASE = "https://mini-code-copilot-backend.onrender.com";
```

### Step 2: Deploy to Vercel/Netlify/Render

**Option A: Vercel**
```bash
cd frontend
vercel --prod
```

**Option B: Netlify**
```bash
cd frontend
netlify deploy --prod
```

**Option C: Render (Static Site)**
1. Create new Static Site on Render
2. Connect your GitHub repository
3. Build Command: `npm run build`
4. Publish Directory: `dist`

---

## 🔧 Important Configuration

### Backend Port Configuration
```javascript
// ✅ CORRECT - Works on Render
const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`Backend running on port ${PORT}`));

// ❌ WRONG - Doesn't work on Render
app.listen(5000, () => console.log("Backend running on port 5000"));
```

### CORS Configuration
Update `index.js` to allow your frontend domain:
```javascript
app.use(cors({
  origin: [
    'https://your-frontend.vercel.app',
    'https://your-frontend.netlify.app',
    'http://localhost:5173'
  ],
  credentials: true
}));
```

---

## 🗄️ Database Setup

### Recommended: Neon (Free PostgreSQL)
1. Sign up at [neon.tech](https://neon.tech)
2. Create a new project
3. Copy the connection string
4. Add to Render environment variables as `DATABASE_URL`

### Run Migrations
After deploying, run migrations:
```bash
# In Render Shell (or locally with production DB)
npx prisma migrate deploy
```

Or add to your build command in Render:
```bash
npm install && npx prisma generate && npx prisma migrate deploy
```

---

## 🐛 Common Issues & Solutions

### Issue 1: "Application failed to respond"
**Solution:** Make sure you're using `process.env.PORT`
```javascript
const PORT = process.env.PORT || 5000;
app.listen(PORT);
```

### Issue 2: Database connection fails
**Solution:** Ensure DATABASE_URL includes `?sslmode=require`
```
postgresql://user:pass@host:5432/db?sslmode=require
```

### Issue 3: CORS errors
**Solution:** Add your frontend domain to CORS whitelist
```javascript
app.use(cors({
  origin: ['https://your-frontend.vercel.app']
}));
```

### Issue 4: Environment variables not loading
**Solution:** 
- Check Render dashboard → Environment tab
- Ensure variables are set for the correct service
- Redeploy after adding variables

---

## 📊 Monitoring

### View Logs
- Go to Render dashboard
- Click on your service
- Navigate to **Logs** tab
- Monitor real-time logs

### Check Health
```bash
curl https://mini-code-copilot-backend.onrender.com/api/history
```

---

## 🚀 Complete Deployment Checklist

- [x] Update port to use `process.env.PORT`
- [ ] Push code to GitHub
- [ ] Set environment variables in Render
- [ ] Wait for deployment to complete
- [ ] Test API endpoints
- [ ] Update frontend API_BASE URL
- [ ] Deploy frontend
- [ ] Test end-to-end functionality

---

## 💡 Pro Tips

1. **Free Tier Limitations:**
   - Render free tier spins down after 15 minutes of inactivity
   - First request after spin-down takes ~30 seconds
   - Consider upgrading for production use

2. **Keep Services Alive:**
   - Use a service like UptimeRobot to ping your backend every 14 minutes
   - Or upgrade to paid tier

3. **Database:**
   - Use Neon's free tier (generous limits)
   - Enable connection pooling for better performance

4. **Monitoring:**
   - Set up Render's built-in monitoring
   - Use Sentry for error tracking
   - Monitor database usage

---

## 🔗 Useful Links

- Render Docs: [render.com/docs](https://render.com/docs)
- Neon Docs: [neon.tech/docs](https://neon.tech/docs)
- Your Backend: [https://mini-code-copilot-backend.onrender.com](https://mini-code-copilot-backend.onrender.com)

---

## 📝 Next Steps

1. Commit and push the port fix
2. Wait for Render to redeploy
3. Test your API endpoints
4. Update frontend with backend URL
5. Deploy frontend
6. Celebrate! 🎉
