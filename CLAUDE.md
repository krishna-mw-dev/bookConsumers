# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Backend (from `Backend/`)
```bash
npm install          # install dependencies
npm start            # start dev server with nodemon (default port 4000)
```

### Frontend (from `Frontend/`)
```bash
npm install          # install dependencies
npm run dev          # start Vite dev server
npm run build        # production build
npm run lint         # ESLint (js/jsx, zero warnings)
npm run preview      # preview production build
```

### Environment
Backend requires a `Backend/.env` file with:
- `PORT` — server port (the frontend hardcodes API calls to `http://localhost:4001`, so use 4001)
- `MongoDBURI` — MongoDB connection string

## Architecture

MERN stack monorepo split into `Backend/` and `Frontend/` — each has its own `package.json` and `node_modules`. Both use ESM (`"type": "module"`).

### Backend
Express server with Mongoose. Follows route → controller → model layering:
- `route/` — Express routers mounted at `/book` and `/user`
- `controller/` — request handlers (book CRUD, user signup/login with bcryptjs hashing)
- `model/` — Mongoose schemas (Book: name/price/category/image/title; User: fullname/email/password)

API endpoints:
- `GET /book` — list all books
- `POST /user/signup` — create user (bcrypt-hashed password)
- `POST /user/login` — authenticate user

No JWT or session middleware — login returns user data directly and the frontend stores it in localStorage.

### Frontend
React 18 + Vite, styled with Tailwind CSS + DaisyUI. Key patterns:
- **Routing**: react-router-dom with three routes: `/` (Home), `/course` (auth-gated), `/signup`
- **Auth**: React Context (`AuthProvider`) backed by localStorage key `"Users"`. The `/course` route redirects unauthenticated users to `/signup`.
- **Forms**: react-hook-form for login/signup
- **API calls**: axios to `http://localhost:4001` (hardcoded in components)
- **UI**: Login is a DaisyUI modal dialog; dark mode toggle via Tailwind `dark:` classes with localStorage persistence; sticky navbar on scroll; book carousel via react-slick

### Important Quirks
- The frontend API base URL is hardcoded in individual components (`Login.jsx`, `Signup.jsx`, etc.), not centralized.
- No test framework is configured in either package.
