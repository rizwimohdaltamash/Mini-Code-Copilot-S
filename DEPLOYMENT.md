# Vercel Deployment Guide

## ✅ Pre-Deployment Checklist

- [x] `node_modules` in `.gitignore` (Backend & Frontend)
- [x] `.env` in `.gitignore` (Backend)
- [x] `vercel.json` created (Backend & Frontend)

## 🚀 Backend Deployment

### Step 1: Install Vercel CLI (Optional)
```bash
npm install -g vercel
```

### Step 2: Deploy Backend
```bash
cd backend
vercel
```

### Step 3: Set Environment Variables in Vercel Dashboard

1. Go to your Vercel project dashboard
2. Navigate to **Settings** → **Environment Variables**
3. Add the following variables:

| Variable Name | Value | Environment |
|--------------|-------|-------------|
| `DATABASE_URL` | Your PostgreSQL connection string | Production, Preview, Development |
| `GROQ_API_KEY` | Your Groq API key | Production, Preview, Development |

**Example DATABASE_URL:**
```
postgresql://username:password@host:port/database?sslmode=require
```

### Step 4: Redeploy After Adding Variables
```bash
vercel --prod
```

### Step 5: Note Your Backend URL
Your backend will be deployed at: `https://your-backend-name.vercel.app`

---

## 🎨 Frontend Deployment

### Step 1: Update API Base URL

Edit `frontend/src/api.ts`:
```typescript
const API_BASE = "https://your-backend-name.vercel.app";
```

### Step 2: Deploy Frontend
```bash
cd frontend
vercel
```

### Step 3: Deploy to Production
```bash
vercel --prod
```

Your frontend will be deployed at: `https://your-frontend-name.vercel.app`

---

## 🔧 Vercel Configuration Files

### Backend `vercel.json`
```json
{
  "version": 2,
  "builds": [
    {
      "src": "index.js",
      "use": "@vercel/node"
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "index.js"
    }
  ],
  "env": {
    "DATABASE_URL": "@database_url",
    "GROQ_API_KEY": "@groq_api_key"
  }
}
```

### Frontend `vercel.json`
```json
{
  "rewrites": [
    {
      "source": "/(.*)",
      "destination": "/index.html"
    }
  ]
}
```

---

## 🗄️ Database Setup

### Option 1: Neon (Recommended for Vercel)
1. Sign up at [neon.tech](https://neon.tech)
2. Create a new project
3. Copy the connection string
4. Add to Vercel environment variables

### Option 2: Supabase
1. Sign up at [supabase.com](https://supabase.com)
2. Create a new project
3. Get PostgreSQL connection string from Settings → Database
4. Add to Vercel environment variables

### Option 3: Railway
1. Sign up at [railway.app](https://railway.app)
2. Create PostgreSQL database
3. Copy connection string
4. Add to Vercel environment variables

---

## 🔄 Running Migrations on Vercel

After deployment, run migrations:

```bash
# Install Vercel CLI
npm install -g vercel

# Link to your project
vercel link

# Run migrations
vercel env pull .env.local
npx prisma migrate deploy
```

Or use Vercel's build command:

Update `package.json` in backend:
```json
{
  "scripts": {
    "build": "npx prisma generate && npx prisma migrate deploy"
  }
}
```

---

## 🐛 Troubleshooting

### CORS Issues
If you get CORS errors, ensure your backend `index.js` has:
```javascript
app.use(cors({
  origin: ['https://your-frontend-name.vercel.app', 'http://localhost:5173'],
  credentials: true
}));
```

### Database Connection Issues
- Ensure `?sslmode=require` is in your DATABASE_URL
- Check that your database allows connections from Vercel IPs
- Verify environment variables are set correctly

### Build Failures
- Check Vercel build logs
- Ensure all dependencies are in `package.json`
- Verify Node.js version compatibility

### API Not Working
- Check backend deployment logs in Vercel dashboard
- Verify environment variables are set
- Test API endpoints directly: `https://your-backend.vercel.app/api/history`

---

## 📊 Monitoring

### View Logs
```bash
vercel logs [deployment-url]
```

### Check Deployment Status
```bash
vercel ls
```

### View Environment Variables
```bash
vercel env ls
```

---

## 🔐 Security Best Practices

1. ✅ Never commit `.env` files
2. ✅ Use Vercel environment variables for secrets
3. ✅ Enable CORS only for your frontend domain
4. ✅ Use HTTPS for database connections
5. ✅ Rotate API keys regularly
6. ✅ Set up proper database access controls

---

## 🎯 Quick Deploy Commands

### Backend
```bash
cd backend
git add .
git commit -m "Deploy to Vercel"
git push origin main
vercel --prod
```

### Frontend
```bash
cd frontend
git add .
git commit -m "Deploy to Vercel"
git push origin main
vercel --prod
```

---

## 📝 Post-Deployment

1. Test all features on production
2. Verify theme toggle works
3. Test code generation
4. Check search functionality
5. Verify star/favorite feature
6. Test on mobile devices

---

## 🌐 Custom Domain (Optional)

1. Go to Vercel project settings
2. Navigate to **Domains**
3. Add your custom domain
4. Update DNS records as instructed
5. Wait for SSL certificate provisioning

---

## 💡 Tips

- Use Vercel's preview deployments for testing
- Set up automatic deployments from GitHub
- Monitor usage in Vercel dashboard
- Use Vercel Analytics for insights
- Enable automatic HTTPS

---

## 📞 Support

- Vercel Docs: [vercel.com/docs](https://vercel.com/docs)
- Prisma Docs: [prisma.io/docs](https://prisma.io/docs)
- Neon Docs: [neon.tech/docs](https://neon.tech/docs)
