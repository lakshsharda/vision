# Deploying Vision-to-Voice Assistant to Render.com

This guide will help you deploy your Vision-to-Voice Assistant to Render.com for your hackathon submission.

## 📋 Prerequisites

1. **GitHub Account** - Your code should be pushed to GitHub
2. **Render.com Account** - Sign up at [render.com](https://render.com) (free tier available)
3. **Demo Video** - Place a demo video file at `data/demo_video.mp4` for cloud deployment

## 🎥 Prepare Demo Video

Since Render doesn't have camera access, you need a demo video:

```bash
# Create data directory if it doesn't exist
mkdir -p data

# Record or download a demo video and save it as:
# data/demo_video.mp4
```

The video should show people walking, objects in different distances, etc.

## 🚀 Deployment Steps

### Option 1: Using Render Blueprint (Recommended)

1. **Push your code to GitHub**
   ```bash
   git add .
   git commit -m "Ready for Render deployment"
   git push origin main
   ```

2. **Connect to Render**
   - Go to [render.com/dashboard](https://dashboard.render.com)
   - Click "New" → "Blueprint"
   - Connect your GitHub repository
   - Render will detect `render.yaml` and create both services automatically

3. **Set Environment Variables** (in Render Dashboard)
   - `VERTEX_PROJECT_ID` - Your Google Cloud project ID
   - `ELEVENLABS_API_KEY` - Your ElevenLabs API key
   - `ELEVENLABS_VOICE_ID` - Your ElevenLabs voice ID

4. **Deploy!**
   - Click "Apply" and wait 5-10 minutes for deployment
   - Backend will be available at: `https://vision-backend.onrender.com`
   - Frontend will be available at: `https://vision-frontend.onrender.com`

### Option 2: Manual Deployment

#### Deploy Backend

1. Go to Render Dashboard → "New" → "Web Service"
2. Connect your GitHub repository
3. Configure:
   - **Name**: `vision-backend`
   - **Environment**: Docker
   - **Plan**: Free
   - **Build Command**: (leave empty, using Dockerfile)
   - **Start Command**: (leave empty, using Dockerfile CMD)

4. Add Environment Variables:
   ```
   USE_DEMO_VIDEO=true
   DEMO_VIDEO_PATH=data/demo_video.mp4
   YOLO_MODEL=yolo11l.pt
   YOLO_CONF=0.25
   USE_DEPTH_ESTIMATION=true
   VERTEX_PROJECT_ID=your-project-id
   VERTEX_LOCATION=us-central1
   VERTEX_MODEL=gemini-2.0-flash-exp
   ELEVENLABS_API_KEY=your-api-key
   ELEVENLABS_VOICE_ID=your-voice-id
   ```

5. Click "Create Web Service"

#### Deploy Frontend

1. Go to Render Dashboard → "New" → "Static Site"
2. Connect your GitHub repository
3. Configure:
   - **Name**: `vision-frontend`
   - **Build Command**: `cd ui && npm install && npm run build`
   - **Publish Directory**: `ui/build`

4. Add Environment Variable:
   ```
   REACT_APP_WS_URL=https://vision-backend.onrender.com
   ```

5. Click "Create Static Site"

## ⚙️ Configuration Files Created

### `Dockerfile`
- Containerizes the Python backend
- Installs all dependencies
- Pre-downloads YOLO model to speed up startup

### `render.yaml`
- Blueprint configuration for automatic deployment
- Defines both backend and frontend services
- Sets up environment variables

### Updated Files
- **`camera.py`** - Now supports demo video mode for cloud deployment
- **`ui/src/App.js`** - Automatically connects to Render backend URL

## 🔍 Troubleshooting

### Backend Not Starting
- Check logs in Render Dashboard
- Ensure demo video file exists in repository
- Verify environment variables are set correctly

### WebSocket Connection Failed
- Make sure backend URL uses `wss://` (not `ws://`)
- Check CORS settings
- Verify backend is fully deployed before accessing frontend

### Slow Performance
- Render free tier "spins down" after 15 minutes of inactivity
- First request after spin-down takes 30-60 seconds
- Consider upgrading to paid tier for better performance

## 💡 Tips for Hackathon Demo

1. **Keep the service warm**: Visit your app every 10 minutes before your demo
2. **Pre-record a video**: Use a high-quality demo video showing various scenarios
3. **Test WebSocket**: Ensure speech output is working before your presentation
4. **Have a backup**: Keep localhost version ready as fallback

## 📊 Resource Usage

**Free Tier Limits:**
- 750 hours/month of uptime
- Shared CPU/memory
- Automatic sleep after 15 min inactivity

**Your app uses:**
- ~512MB RAM for backend
- ~100MB for frontend
- YOLO11L model (~90MB download once)

## 🎯 Your Live URLs

After deployment, share these links for your hackathon:

- **Frontend (UI)**: `https://vision-frontend.onrender.com`
- **Backend (API)**: `https://vision-backend.onrender.com`

## 🆘 Need Help?

- Check [Render Documentation](https://render.com/docs)
- View deployment logs in Render Dashboard
- Test locally with: `USE_DEMO_VIDEO=true python ui_server.py`

Good luck with your hackathon! 🚀
