# MediaForge - AI-Powered Stock Media Platform

## Overview

MediaForge is a complete, automated stock media platform built on InsForge backend-as-a-service. It enables users to:
- Upload images and videos
- Download premium media content
- Earn revenue from their uploads
- Automatically discover and generate AI-based content from trending topics

## Architecture

### Backend Infrastructure (InsForge)

**Database Tables:**
- `users` - User accounts, wallet balance, profile info
- `media` - Media files metadata (images, videos)
- `downloads` - Download transactions and tracking
- `revenue` - Creator earnings from downloads
- `trends` - Trending topics discovered and analyzed
- `generated_media_queue` - AI content generation tasks

**Storage Buckets:**
- `media-uploads` - User-uploaded files
- `media-thumbnails` - Generated thumbnails
- `generated-media` - AI-generated content

**Edge Functions:**
1. **Auth Function** (`/functions/auth`)
   - `?action=signup` - User registration
   - `?action=login` - User authentication

2. **Upload Media Function** (`/functions/upload-media`)
   - Handles media file uploads
   - Saves metadata to database
   - Stores in cloud storage

3. **Download Media Function** (`/functions/download-media`)
   - Tracks downloads
   - Processes payments ($2.99 per download)
   - Distributes 70% to creators

4. **Analyze Trends Function** (`/functions/analyze-trends`)
   - `?action=analyze-trends` - Fetch and save trending topics
   - `?action=get-trends` - Get top trends

5. **Generate Media Function** (`/functions/generate-media`)
   - `?action=generate-media` - Process AI generation queue
   - Integrates with OpenAI and Replicate APIs

**Scheduled Tasks (Cron):**
- **Analyze Trends Daily** - 9 AM UTC
  - Automatically fetches trending topics
  - Creates AI generation tasks
  
- **Generate AI Content** - Every hour (0:00 UTC)
  - Processes pending generation tasks
  - Creates new media assets automatically

### Frontend Application

**Location:** `/frontend/index.html`

**Features:**
- Clean, minimal UI with no heavy frameworks
- Responsive design for mobile and desktop
- Pure HTML/CSS/JavaScript

**Pages:**
1. **Login/Signup** - User authentication
2. **Gallery** - Browse all available media
3. **Upload** - Submit user-created content
4. **Dashboard** - View statistics and earnings

## Setup Instructions

### 1. Prerequisites
- Node.js installed
- InsForge CLI (`npm install -g @insforge/cli`)
- API keys for external services (optional):
  - OpenAI API for image generation
  - Twitter API for trend fetching

### 2. Database Setup (Already Done)
All database tables have been created via:
```bash
insforge db query "CREATE TABLE ..."
```

### 3. Storage Setup (Already Done)
Storage buckets created via:
```bash
insforge storage create-bucket media-uploads
insforge storage create-bucket media-thumbnails
insforge storage create-bucket generated-media
```

### 4. Edge Functions (Already Done)
Functions deployed via:
```bash
insforge functions deploy auth
insforge functions deploy upload-media
insforge functions deploy download-media
insforge functions deploy analyze-trends
insforge functions deploy generate-media
```

### 5. Scheduled Tasks (Already Done)
Cron jobs created via:
```bash
insforge schedules create --name "Analyze Trends Daily" --cron "0 9 * * *" ...
insforge schedules create --name "Generate AI Content Hourly" --cron "0 * * * *" ...
```

## Configuration

### API Endpoint
```
https://qaq42uh5.ap-southeast.insforge.app
```

### Project Details
- **Project ID:** 80d73072-5736-45e4-8e7c-939b5b08c9f2
- **App Key:** qaq42uh5
- **Region:** ap-southeast
- **Org ID:** 6e428df9-eaca-41b1-892d-2b7ef59c9c8c

### Function URLs
- Auth: `https://qaq42uh5.ap-southeast.insforge.app/functions/auth`
- Upload: `https://qaq42uh5.ap-southeast.insforge.app/functions/upload-media`
- Download: `https://qaq42uh5.ap-southeast.insforge.app/functions/download-media`
- Trends: `https://qaq42uh5.ap-southeast.insforge.app/functions/analyze-trends`
- Generate: `https://qaq42uh5.ap-southeast.insforge.app/functions/generate-media`

## API Usage

### Authentication
**Signup:**
```bash
curl -X POST "${API_URL}/functions/auth?action=signup" \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"password","displayName":"User"}'
```

**Login:**
```bash
curl -X POST "${API_URL}/functions/auth?action=login" \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"password"}'
```

### Media Upload
```bash
curl -X POST "${API_URL}/functions/upload-media" \
  -F "userId=user-id" \
  -F "title=My Media" \
  -F "description=Description" \
  -F "mediaType=image" \
  -F "file=@path/to/file.jpg"
```

### Media Download
```bash
curl -X GET "${API_URL}/functions/download-media?mediaId=id&userId=user-id"
```

## Frontend Deployment

### Development Server
Simply open `frontend/index.html` in a browser.

### Production Deployment
Deploy using InsForge deployments:
```bash
cd frontend
insforge deployments deploy . --env '{"VITE_API_URL": "https://qaq42uh5.ap-southeast.insforge.app"}'
```

Alternatively, use any static hosting (Netlify, Vercel, GitHub Pages, AWS S3):
1. Host `frontend/index.html` and dependencies
2. Ensure Frontend can reach the InsForge API endpoint
3. Update `API_URL` in index.html if needed

## Revenue Model

**Download Price:** $2.99 per download

**Split:**
- Creator: 70% ($2.09)
- Platform: 30% ($0.90)

**Payment Tracking:**
- Each download creates a record in `downloads` table
- Revenue is calculated and stored in `revenue` table
- Creator's wallet is updated automatically

## Automation Workflow

### Daily Trend Analysis (9 AM UTC)
1. Fetch trending topics from:
   - Twitter (if API key configured)
   - Trending topics database
2. Save trends to `trends` table
3. Create AI generation tasks in `generated_media_queue`

### Hourly AI Generation (Every hour)
1. Check `generated_media_queue` for pending tasks
2. For each pending task:
   - Use OpenAI to generate images
   - Use Replicate for video generation
3. Save generated media to storage
4. Create media records in database
5. Update queue status

## Customization

### Add External API Integrations
Update function environment variables:
```bash
insforge secrets update OPENAI_API_KEY --value "your-key"
insforge secrets update TWITTER_BEARER_TOKEN --value "your-token"
insforge secrets update REPLICATE_API_KEY --value "your-key"
```

### Modify Pricing
Edit the `DOWNLOAD_PRICE` constant in edge functions:
```typescript
const DOWNLOAD_PRICE = 2.99;  // Change this
const CREATOR_SHARE = 0.7;    // Change split
```

### Adjust Schedules
Update cron jobs:
```bash
insforge schedules update <schedule-id> --cron "0 0 * * *"  # Midnight daily
```

## Monitoring

### View Logs
```bash
insforge logs insforge.logs --limit 50
insforge logs function.logs --limit 50  # Edge function logs
```

### Check Scheduled Task Status
```bash
insforge schedules list
insforge schedules logs <schedule-id>
```

### View Database Activity
```bash
insforge db query "SELECT COUNT(*) as users FROM users"
insforge db query "SELECT SUM(amount) as total_revenue FROM revenue"
```

## Troubleshooting

### Functions Not Responding
1. Check logs: `insforge logs function.logs`
2. Verify INSFORGE_BASE_URL and INSFORGE_ANON_KEY are set
3. Test function directly: `insforge functions invoke auth --data '{"action":"login"}'`

### Media Upload Fails
1. Check storage bucket exists: `insforge storage buckets`
2. Verify bucket is public: `insforge storage buckets | grep public`
3. Check file size limits

### Schedules Not Running
1. Verify cron syntax: 5-field format only (minute hour day month day-of-week)
2. Check schedule is active: `insforge schedules list`
3. View recent logs: `insforge schedules logs <schedule-id>`

## Next Steps

1. **Add Authentication Token** - Implement JWT for secure API calls
2. **Enhanced UI** - Add user profiles, media management, analytics
3. **Payment Integration** - Connect Stripe/PayPal for real payments
4. **Search & Filters** - Add search, filtering by category/price
5. **Social Features** - Likes, comments, sharing
6. **Admin Dashboard** - Content moderation, analytics
7. **Mobile App** - React Native or Flutter app

## Project Structure

```
automation/
├── .insforge/
│   └── project.json              # Project configuration
├── insforge/
│   └── functions/
│       ├── auth/index.ts         # Authentication
│       ├── upload-media/index.ts # Upload handler
│       ├── download-media/index.ts # Download handler
│       ├── analyze-trends/index.ts # Trend analysis
│       └── generate-media/index.ts # AI generation
├── frontend/
│   └── index.html                # Web application
└── README.md                      # This file
```

## Support

For issues or questions:
1. Check InsForge documentation: https://docs.insforge.dev
2. Review function logs: `insforge logs function.logs`
3. Test API endpoints directly
4. Check database schema: `insforge db tables`

---

**MediaForge** - Built with InsForge Backend-as-a-Service
