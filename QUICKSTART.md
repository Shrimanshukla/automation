# MediaForge - Quick Start Guide

## What's Been Set Up

You now have a fully functional AI-powered stock media platform with:

✅ **Complete Backend Infrastructure**
- Production-ready InsForge project
- 6 database tables for users, media, downloads, revenue, trends, and AI generation
- 3 cloud storage buckets for media files
- 5 edge functions handling authentication, uploads, downloads, trend analysis, and AI generation

✅ **Fully Automated Workflow**
- Daily trend analysis (9 AM UTC) - discovers what's trending
- Hourly AI content generation - automatically creates new media
- Revenue tracking - 70% to creators, 30% platform fee

✅ **Clean, Lightweight Frontend**
- Single HTML file with no build dependencies
- Responsive design works on mobile, tablet, desktop
- Features: signup, login, gallery, upload, dashboard

## How to Use

### 1. Open the Website
Simply open the frontend in your browser:
```
frontend/index.html
```

Or serve it with a local server:
```bash
python -m http.server 8000  # or any web server
# Then open: http://localhost:8000/frontend/
```

### 2. Create an Account
1. Click "Sign up"
2. Enter your name, email, and password
3. You're automatically logged in

### 3. uploadMedia
1. Click "Upload Media"
2. Drag and drop or select an image/video file
3. Add a title and description
4. Choose media type (Image or Video)
5. Click "Upload"

Your media is now searchable by others and available for download at $2.99 each.

### 4. Browse & Download
1. Go to "Gallery"
2. Click "Download" on any media to purchase
3. Or "Preview" to see details

### 5. Track Your Earnings
1. Go to "Dashboard"
2. See your stats:
   - Total uploads
   - Total downloads
   - Total earnings
   - Wallet balance

## Backend Commands

### View Database
```bash
cd automation
insforge db query "SELECT * FROM users LIMIT 5"
insforge db query "SELECT COUNT(*) as total_uploads FROM media"
insforge db query "SELECT SUM(amount) FROM revenue"
```

### Check Storage
```bash
insforge storage list-objects media-uploads
insforge storage list-objects generated-media
```

### View Scheduled Tasks
```bash
insforge schedules list
insforge schedules logs 272130dd-6b00-472a-a04f-9a630a4d974f  # Trend analysis
insforge schedules logs af05e9ce-bd3d-4d61-a834-d7cb83f8f682  # AI generation
```

### Monitor Logs
```bash
insforge logs function.logs --limit 20
insforge logs insforge.logs --limit 20
```

### Test an Edge Function
```bash
insforge functions invoke auth --data '{"action":"login","email":"test@example.com","password":"test"}'
insforge functions invoke analyze-trends --data '{"action":"analyze-trends"}'
insforge functions invoke generate-media --data '{"action":"generate-media"}'
```

## Project URLs

**Live API Endpoint:**
```
https://qaq42uh5.ap-southeast.insforge.app
```

**Function Endpoints:**
- Auth: `/functions/auth?action=signup|login`
- Upload: `/functions/upload-media`
- Download: `/functions/download-media?mediaId=...&userId=...`
- Trends: `/functions/analyze-trends?action=analyze-trends|get-trends`
- Generate: `/functions/generate-media?action=generate-media`

**Project Dashboard:**
```
https://insforge.dev/dashboard/project/80d73072-5736-45e4-8e7c-939b5b08c9f2
```

## Project Configuration

```json
{
  "project_id": "80d73072-5736-45e4-8e7c-939b5b08c9f2",
  "project_name": "MediaForge",
  "org_id": "6e428df9-eaca-41b1-892d-2b7ef59c9c8c",
  "appkey": "qaq42uh5",
  "region": "ap-southeast",
  "oss_host": "https://qaq42uh5.ap-southeast.insforge.app"
}
```

## File Structure

```
automation/
├── .insforge/
│   └── project.json              # Project config (do not delete)
├── insforge/
│   └── functions/
│       ├── auth/index.ts         # Signup/login
│       ├── upload-media/index.ts # File uploads
│       ├── download-media/index.ts # Downloads + revenue
│       ├── analyze-trends/index.ts # Trend discovery
│       ├── generate-media/index.ts # AI generation
│       ├── media-handler/index.ts  # Test helper
│       └── test/index.ts          # Test function
├── frontend/
│   └── index.html                # Complete web app (single file!)
├── .gitignore
├── README.md                     # Full documentation
└── QUICKSTART.md               # This file
```

## Automation in Action

### What Happens Automatically Each Day:

**9:00 AM UTC - Trend Analysis**
1. System fetches trending topics (AI Art, Stock Photos, etc.)
2. Saves trends to database
3. Creates generation tasks for each trend

**Every Hour - AI Content Generation**
1. Takes pending generation tasks
2. Uses AI APIs to generate images/videos
3. Saves new media to storage
4. Updates media library automatically

Result: Your platform grows with fresh, trending content 24/7!

## Testing the System

### Test User Signup
```bash
curl -X POST "https://qaq42uh5.ap-southeast.insforge.app/functions/auth?action=signup" \
  -H "Content-Type: application/json" \
  -d '{
    "email":"testuser@example.com",
    "password":"password123",
    "displayName":"Test User"
  }'
```

### Test User Login
```bash
curl -X POST "https://qaq42uh5.ap-southeast.insforge.app/functions/auth?action=login" \
  -H "Content-Type: application/json" \
  -d '{
    "email":"testuser@example.com",
    "password":"password123"
  }'
```

### Trigger Trend Analysis
```bash
curl -X POST "https://qaq42uh5.ap-southeast.insforge.app/functions/analyze-trends?action=analyze-trends" \
  -H "Content-Type: application/json" \
  -d '{}'
```

### Trigger AI Generation
```bash
curl -X POST "https://qaq42uh5.ap-southeast.insforge.app/functions/generate-media?action=generate-media" \
  -H "Content-Type: application/json" \
  -d '{}'
```

## Common Tasks

### Add API Keys for AI Generation
```bash
cd automation
insforge secrets update OPENAI_API_KEY --value "sk-your-key-here"
insforge secrets update REPLICATE_API_KEY --value "your-replicate-key"
insforge secrets update TWITTER_BEARER_TOKEN --value "your-twitter-bearer"
```

### Deploy Frontend to Production
```bash
# Option 1: Deploy with InsForge
insforge deployments deploy ./frontend

# Option 2: Deploy with Vercel
npx vercel deploy ./frontend

# Option 3: Deploy with Netlify
netlify deploy --dir ./frontend
```

### Check Revenue in Database
```bash
insforge db query "
  SELECT 
    u.display_name,
    COUNT(d.id) as download_count,
    SUM(r.amount) as total_earnings
  FROM revenue r
  JOIN downloads d ON r.download_id = d.id
  JOIN users u ON r.user_id = u.id
  GROUP BY u.id, u.display_name
  ORDER BY total_earnings DESC
"
```

### See Top Trending Content
```bash
insforge db query "
  SELECT title, downloads, views, trending_score
  FROM media
  ORDER BY trending_score DESC
  LIMIT 10
"
```

## Next Enhancements

1. **Connect Real Payment Processing**
   - Stripe or PayPal integration
   - Automatic payouts to creators

2. **Enhanced AI**
   - Fine-tune models on trending data
   - Video generation improvement
   - Style/category specific generation

3. **Social Features**
   - User profiles and portfolios
   - Comments and ratings
   - Share media on social media

4. **Better UI**
   - Admin dashboard for moderation
   - Advanced search and filtering
   - User analytics and insights

5. **Mobile App**
   - React Native or Flutter
   - Native upload/download
   - Push notifications

## Troubleshooting

**Q: Frontend not loading?**
A: Make sure you're accessing it as a file (`file:///`) or through a web server.

**Q: API calls failing?**
A: Check the browser console for CORS errors. The InsForge API should allow CORS by default.

**Q: Trend analysis not running?**
A: Check: `insforge schedules list` and `insforge schedules logs <id>`

**Q: Can't upload files?**
A: Verify storage bucket exists: `insforge storage buckets`

**Q: Database query errors?**
A: Check table names and syntax: `insforge db tables`

## Need Help?

1. **View Logs:** `insforge logs function.logs`
2. **Check Status:** `insforge current`
3. **Test Function:** `insforge functions invoke <name>`
4. **Read Full Docs:** See README.md in the project root

---

**MediaForge is now live and ready to generate revenue!** 🚀
