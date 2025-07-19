# 🚀 Full-Stack Deployment Manual (Vite + React, Express + Prisma, PostgreSQL)

This manual provides a **complete and reusable guide** for deploying a modern full-stack web application using the following technologies:

- **Frontend:** React + Vite + TailwindCSS
- **Backend:** Express + Prisma ORM
- **Database:** PostgreSQL (e.g., NeonDB)
- **Deployment:** Vercel (frontend), Render (backend), NeonDB (database)

It includes folder structure conventions, environment configurations, platform-specific deployment settings, and key development tips. Use this as a base for future full-stack apps.

---

## 📁 Project Structure (Generic)

```
project-root/
├── client/               # Frontend (Vite + React)
│   ├── public/           
│   ├── src/              # Components, pages, router
│   ├── .env              # Environment vars (dev only)
│   ├── package.json      
│   ├── vite.config.js    
│   ├── tailwind.config.js
│   ├── postcss.config.js
│   └── vercel.json       # SPA routing on Vercel
│
├── server/               # Backend (Express + Prisma)
│   ├── routes/           # Modular route handlers
│   ├── prisma/           
│   │   ├── schema.prisma
│   │   └── migrations/
│   ├── index.js          
│   ├── .env              # Contains DATABASE_URL
│   └── package.json      
│
├── .gitignore            
└── README.md             
```

---

## 🧠 Tech Stack

| Layer     | Technology                         |
|-----------|-------------------------------------|
| Frontend  | React, Vite, TailwindCSS, React Router |
| Backend   | Express, Prisma ORM, Multer         |
| Database  | PostgreSQL (e.g., NeonDB)           |
| Hosting   | Vercel (Frontend), Render (Backend) |

---

## 🗂️ Environment Variables

### `client/.env`

Used during **local development**:

```
VITE_API_URL=http://localhost:5000
```

> ⚠️ In production (e.g. Vercel), this file does **not exist**. You must set this variable manually in the platform's environment settings.

### `server/.env`

Required both locally and in production:

```
DATABASE_URL="postgresql://user:pass@host/dbname?sslmode=require"
```

---

## ⚙️ import.meta.env – How It Works

In **Vite projects**, `import.meta.env` exposes environment variables **at build time**. All variables must start with `VITE_`, e.g.:

```js
const API_BASE_URL = import.meta.env.VITE_API_URL;
```

- During development, it reads from `client/.env`
- In production (e.g., Vercel), variables must be defined in the **dashboard**
- If missing, `import.meta.env.VITE_API_URL` becomes `undefined`, which can break API calls — **always validate it**

---

## 📦 package.json – Configuration

### `client/package.json`

```json
{
  "name": "client",
  "private": true,
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.18.0"
  },
  "devDependencies": {
    "vite": "^5.0.0",
    "tailwindcss": "^3.4.0",
    "postcss": "^8.4.0",
    "autoprefixer": "^10.4.0"
  }
}
```

### `server/package.json`

```json
{
  "name": "server",
  "main": "index.js",
  "type": "module",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js"
  },
  "dependencies": {
    "express": "^4.18.2",
    "cors": "^2.8.5",
    "dotenv": "^16.3.1",
    "prisma": "^6.10.1",
    "@prisma/client": "^6.10.1",
    "multer": "^1.4.5-lts.1"
  },
  "devDependencies": {
    "nodemon": "^3.0.1"
  }
}
```

> ⚠️ For Render to run correctly, the `start` script must exist.

---

## 🛠️ Local Development

### Backend

```bash
cd server
npm install
npx prisma generate
npx prisma migrate dev --name init
npm run dev
```

### Frontend

```bash
cd client
npm install
npm run dev
```

- Frontend: http://localhost:5173  
- Backend: http://localhost:5000

---

## ☁️ Deployment Configuration

### 🌐 Frontend (Vercel)

- **Root directory:** `client`
- **Build command:** `npm run build`
- **Output directory:** `dist`
- **Environment Variables:**
  - `VITE_API_URL=https://your-backend.onrender.com`

#### vercel.json

```json
{
  "rewrites": [
    { "source": "/(.*)", "destination": "/" }
  ]
}
```

> Required to support client-side routing on refresh (`React Router`).

---

### 🖥️ Backend (Render)

- **Root directory:** `server`
- **Build command:** `npm install`
- **Start command:** `npm run start`
- **Environment Variable:** `DATABASE_URL`

> Choose “Web Service” in Render. Set port to `5000` or leave default (`PORT` is automatically injected).

---

### 🗄️ Database (NeonDB)

- **Platform:** https://neon.tech
- **Type:** PostgreSQL (serverless)
- **Used with:** Prisma
- **Connection string:** Goes into `DATABASE_URL`

> Ensure `sslmode=require` is added for platforms like Render.

---

## ✅ Summary Table

| Layer     | Platform   | Directory | Build Script       | Env Vars                       |
|-----------|------------|-----------|--------------------|--------------------------------|
| Frontend  | Vercel     | `/client` | `npm run build`     | `VITE_API_URL`                 |
| Backend   | Render     | `/server` | `npm install && npm run start` | `DATABASE_URL`       |
| Database  | NeonDB     | —         | —                  | used in backend `.env`         |

---

## 🧩 Additional Considerations

- Add `.env`, `/dist`, `/node_modules`, `/prisma/migrations` to `.gitignore`.
- Use `multer.memoryStorage()` if you do not want to persist image files on disk.
- Always test `VITE_API_URL` before using in fetch:
  
```js
if (!import.meta.env.VITE_API_URL) {
  throw new Error("API base URL is not defined.");
}
```

- Use `npx prisma studio` to visually browse your data.
- Prisma migrations must be committed if you're using CI/CD or team collaboration.
- In production, missing fields in the database schema (e.g., removed columns) may require manual DB adjustments (especially on NeonDB).

---

## 📝 About This Manual

This document was written and compiled based on a real deployment session, with the support of an AI assistant. It combines practical experience with structured explanation, aiming to help developers efficiently deploy and manage full-stack applications in production environments.

Feel free to customize and evolve it for your future projects.
