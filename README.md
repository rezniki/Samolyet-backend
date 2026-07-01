# ✈️ Samolyet Backend (MVP)

Backend for the dating app **without sponges**. Users receive 3 profiles per day based on the goal (serious relationship / friendship / casual) and can write directly - without a preliminary match.

> 🎯 **MVP Goal**: Test hypothesis - will people post directly if you remove "match" mechanics?

[! [License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[! [Node.js](https://img.shields.io/badge/Node-18+-green.svg)](https://nodejs.org/)
[! [MongoDB](https://img.shields.io/badge/MongoDB-7.x-47A248.svg)](https://www.mongodb.com/)

---

## 📋 Table of Contents

- [Technical stack](#-technical stack)
- [Quick start](#-quick start)
- [Environment variables](#-environment variables)
- [Project structure](#-project structure)
- [Endpoints API](#-api-endpoints)
- [Deploy](#-deploy)
- [Development](#-development)
- [License](#-license)

---

## 🔧 Tech stack

| Component | Technology |
|-----------------------
| **Runtime** | Node.js 18+ |
| **Framework** | Express.js |
| **Language** | TypeScript |
| **Database** | MongoDB + Mongoose ODM |
| **Authentication** | JWT + bcryptjs |
| **Files** | Cloudinary API |
| **Chat** | REST polling (Socket.io - in plans) |
| **Validation** | Zod |
| **Logout** | console + morgan |
| **Deploy** | Render / Railway (free tier) |

---

## 🚀 Quick start

### Requirements
- Node.js 18+ (`node -v`)
- npm or pnpm
- MongoDB (local or Atlas URI)

### Install

```bash
# 1. Clone the repository
git clone https://github.com/your-username/samolyet-backend.git
cd samolyet-backend

# 2. Set dependencies
npm install

# 3. Create environment file
cp .env.example . env
# Edit . env, populating the necessary variables

# 4. Run in development mode
npm run dev

# 5. The server is running at http://localhost:3000
# Health check: http://localhost:3000/health
```

## Assembly for production

```bash
npm run build    # Компиляция TypeScript → dist/
npm start        # Запуск собранного сервера
```

## 🔐 Environment variables

Copy .env.example to . env and fill in the value:

```bash
# Server
NODE_ENV=development    # development | production
PORT=3000               # Порт сервера
FRONTEND_URL=http://localhost:5173  # URL фронтенда для CORS

# Database
MONGODB_URI=mongodb+srv://user:pass@cluster.mongodb.net/samolyet?retryWrites=true&w=majority

# Auth
JWT_SECRET=your_super_secret_jwt_key_here  # Мин. 32 символа
JWT_EXPIRES_IN=7d                          # Срок жизни токена

# Cloudinary (загрузка фото)
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret

# Logging
LOG_LEVEL=info          # debug | info | warn | error
```

## Project structure

```bash
backend/
├── src/
│ ├── config/          # Configuration: BD, env vars
│ ├── controllers/     # HTTP Request Handlers (Thin)
│ ├── models/          # Mongoose scheme (User, Chat, ActionLog)
│ ├── routes/          # API routs (Express Router)
│ ├── middleware/      # Auth, validation, mistakes, rate limiting
│ ├── services/        # Business logic (recommendations, chat, auth)
│ ├── utils/           # Helper-функции и утилиты
│ ├── types/           # TypeScript type declarations
│ └── index.ts         # Entry point
├── .env.example       # Environment variable template
├── package.json       # Dependencies and scripts
├── tsconfig.json      # TypeScript Settings
└── README.md          # This file
```

Architectural Pattern: Controller → Service → Model
Business logic is isolated in services/ for easy testing and reuse.

## 🔌 API Endpoints (MVP)

### Auth

| **Method** | **Endpoint** | **Description** | **Auth** |
|------------|--------------|-----------------|----------|
| Post | /api/auth/register | New user registration | ❌ |                     
| Post | /api/auth/login | Log in, get JWT token | ❌ |
| Get | /api/auth/me | Get current user data | ✅ |

### Profile

| **Method** | **Endpoint** | **Description** | **Auth** |
|------------|--------------|-----------------|----------|
| GET | /api/profile/:id | Get a public user profile | ❌ |                     
| PUT | /api/profile/me | Update your profile | ✅ |
| POST | /api/profile/me/photo | Upload photo (Cloudinary) | ✅ |

### Recommendations

| **Method** | **Endpoint** | **Description** | **Auth** |
|------------|--------------|-----------------|----------|
| Get | /api/recommendations?limit=3 | Get 3 profiles today | ✅ |                     
| Post | /api/actions/pass | Record the profile’s "skip" action | ✅ |

📌 Limit: 3 profiles per user per day. If exhausted, 429 Too Many Requests.

### Settings

| **Method** | **Endpoint** | **Description** | **Auth** |
|------------|--------------|-----------------|----------|
| PUT | /api/settings/remind-me | Enable/disable reminders | ✅ |

### Systems

| **Method** | **Endpoint** | **Description** | **Auth** |
|------------|--------------|-----------------|----------|
| GET | /health | Health check for deploy | ❌ |  


### Format of API answers

Success (2xx):

```bash
{
  "success": true,
  "data": { ... }
}
```

Error (4xx/5xx):

```bash
{
  "success": false,
  "error": "ERROR_CODE",
  "message": "Человекочитаемое описание"
}
```

### Authentication

All protected endpoints require a title:

```bash
Authorization: Bearer <jwt_token>
```

## 🌍 Deploy
Render (free rate)

    Connect the repository to the Render Dashboard
    Select Web Service Node.js
    Assembly settings:
        Build Command: npm install && npm run build
        Start Command: node dist/index.js
    Add environment variables from . env to Render
    Enable Auto-Deploy to disable when the main is blank

    ⚠️ Free tier "goes to sleep" after 15 minutes of inactivity. The first request can take ~30 seconds.
    Solution: Set up /health via UptimeRobot
    .

Railway
Render similarly:

    Detect: package.json
    Build: npm run build
    Start: node dist/index.js

## 💻 Development

Scripts npm

```bash
npm run dev   # Run with nodemon + ts-node (development)
npm run build   # TypeScript JavaScript compilation
npm start   # Run production build
npm run typecheck  # Checking types without compiling
```

## Code Style

    ESLint + Prettier - in plans for v0.2
    For now: follow the intuitive structure and comments in your code

## Testing

Manual testing is used for MVP:

    Postman/Insomnia collection: link-to-collection
     (in plans)
    Local tests via curl or frontend

Unit tests (Jest) - scheduled after validation of the hypothesis.

## 🗓️ Roadmap (MVP v1)

    Basic architecture + auth
    User profile + photo upload
    Recommendation behavior (3 profiles/day)
    Chat: Polling
    Rate limiting + basic spam protection
    Analytics: ActionLog
    WebSocket for real-time chat
    Email notifications (remindMe)
    Moderation: profile/post complaint
    
## 🤝 Participation in development

The project is being developed solo, but pull requests are welcome!

    Forkni Repository
    Create a branch feature/your-feature-name
    Make changes + test locally
    Open the PR with a description of the changes

Before PR:

    Make sure the code is running npm run typecheck
    Add/update comments in difficult places
    Don’t break existing API contracts

## 📄 License
MIT License - see LICENSE file for details.

## 🆘 Issues and questions

    🐛 Bug?   Open Issue
     with a description and steps of playback
    💡 Idea?   Write in Discussions or create an Issue with the enhancement tag
    ❓ Question?   Check this README first, then ask in Issues

    ✨ Made with ❤️ for meaningful connections
    Samolyet - because sometimes you just have to take a step forward.
