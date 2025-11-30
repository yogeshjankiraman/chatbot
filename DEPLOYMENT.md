# Render Deployment Guide

This guide will help you deploy your ChatBot AI application to Render.

## Prerequisites

1. A GitHub account with this repository
2. A Render account (sign up at https://render.com)
3. MongoDB database (MongoDB Atlas recommended)
4. Google Gemini API key

## Step 1: Deploy Backend (API Server)

1. Go to your Render dashboard: https://dashboard.render.com
2. Click **"New +"** and select **"Web Service"**
3. Connect your GitHub repository: `yogeshjankiraman/chatbot`
4. Configure the service:
   - **Name**: `chatbot-api` (or any name you prefer)
   - **Environment**: `Node`
   - **Build Command**: `cd chatBot-ai/server && npm install`
   - **Start Command**: `cd chatBot-ai/server && npm start`
   - **Plan**: Free (or choose a paid plan)

5. Add Environment Variables:
   - `MONGO_URI`: Your MongoDB connection string
     - Example: `mongodb+srv://username:password@cluster.mongodb.net/chatbot?retryWrites=true&w=majority`
   - `JWT_SECRET`: A random secret string for JWT tokens
     - Example: Generate one using: `openssl rand -base64 32`
   - `GEMINI_API_KEY`: Your Google Gemini API key
     - Get it from: https://makersuite.google.com/app/apikey
   - `NODE_ENV`: `production`
   - `FRONTEND_URL`: Will be set automatically after frontend deployment (or set manually to your frontend URL)
   - `PORT`: Leave this - Render will set it automatically

6. Click **"Create Web Service"**

7. Wait for deployment to complete and note the service URL (e.g., `https://chatbot-api.onrender.com`)

## Step 2: Deploy Frontend (React Client)

1. In your Render dashboard, click **"New +"** and select **"Static Site"**
2. Connect the same GitHub repository
3. Configure the service:
   - **Name**: `chatbot-client` (or any name you prefer)
   - **Build Command**: `cd chatBot-ai/client && npm install && npm run build`
   - **Publish Directory**: `chatBot-ai/client/build`

4. Add Environment Variable:
   - `REACT_APP_API_URL`: Your backend service URL from Step 1
     - Example: `https://chatbot-api.onrender.com`
     - **Important**: Don't include a trailing slash

5. Click **"Create Static Site"**

6. Wait for deployment to complete

## Step 3: Update CORS Settings (if needed)

If you encounter CORS errors, update `chatBot-ai/server/server.js`:

```javascript
app.use(cors({
  origin: ['https://your-frontend-url.onrender.com', 'http://localhost:3000'],
  credentials: true
}));
```

## Step 4: Test Your Deployment

1. Visit your frontend URL (from Step 2)
2. Try signing up a new user
3. Test the chat functionality

## Environment Variables Summary

### Backend (API Server):
- `MONGO_URI` - MongoDB connection string
- `JWT_SECRET` - Secret for JWT token signing
- `GEMINI_API_KEY` - Google Gemini API key
- `NODE_ENV` - Set to `production`
- `PORT` - Automatically set by Render

### Frontend (Static Site):
- `REACT_APP_API_URL` - Your backend API URL (e.g., `https://chatbot-api.onrender.com`)

## Troubleshooting

### Backend Issues:
- Check logs in Render dashboard
- Verify all environment variables are set correctly
- Ensure MongoDB connection string is correct
- Check that PORT is not hardcoded (should use `process.env.PORT`)

### Frontend Issues:
- Verify `REACT_APP_API_URL` is set correctly
- Check browser console for CORS errors
- Ensure backend URL doesn't have trailing slash
- Rebuild if environment variables were changed

### Common Errors:
- **"Cannot connect to API"**: Check `REACT_APP_API_URL` in frontend environment variables
- **"CORS error"**: Update CORS settings in server.js
- **"MongoDB connection failed"**: Verify `MONGO_URI` is correct and MongoDB Atlas allows connections from anywhere (0.0.0.0/0)

## Notes

- Free tier services on Render spin down after 15 minutes of inactivity
- First request after spin-down may take 30-60 seconds
- Consider upgrading to a paid plan for production use
- Keep your API keys and secrets secure - never commit them to GitHub

