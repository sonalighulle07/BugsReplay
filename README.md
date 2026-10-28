# BugReplay

A debugging workspace: record **sessions**, capture the **events** inside them (user actions, API calls, errors),
and export a bug report.

**Stack:** React 19 + Vite + React Router · Node.js + Express 5 · MySQL

## Setup

### 1. Database
```bash
mysql -u root -p < database/schema.sql
```
(Windows: open MySQL Workbench / the MySQL shell and run the contents of `database/schema.sql`.)

### 2. Backend
```bash
cd backend
cp .env.example .env      # Windows: copy .env.example .env   — then fill in DB_PASSWORD
npm install
npm run dev               # http://localhost:5000
```
Check it: http://localhost:5000/api/health → `{"status":"ok","database":"connected"}`

### 3. Frontend
```bash
cd frontend
npm install
npm run dev               # http://localhost:5173
```
To point at a different API, copy `.env.example` to `.env` and change `VITE_API_URL`.

## Features
- **Dashboard** — totals, error rate, events-by-type chart, recent errors, recent sessions
- **Sessions** — create, search, sort, rename, delete (events are removed with the session)
- **Session timeline** — filter by type, search title/endpoint, add sample or custom events, delete events
- **Live capture** — one click records this page's clicks, uncaught JS errors, unhandled rejections and `fetch()` calls into the session
- **Export** — full session as JSON or as a Markdown bug report
- **Privacy** — passwords, tokens, `Authorization`/`Cookie` values and `Bearer …` strings are redacted server-side before storage
- API status indicator in the header

## API
| Method | Route | Description |
|---|---|---|
| GET | `/api/health` | API + database check |
| GET | `/api/stats` | Dashboard numbers |
| GET | `/api/sessions?search=` | List sessions with `event_count` / `error_count` |
| POST | `/api/sessions` | `{ session_name }` |
| GET / PATCH / DELETE | `/api/sessions/:id` | Read, rename (`{ session_name }`), delete |
| GET | `/api/events?sessionId=&type=&search=` | List a session's events |
| POST | `/api/events` | `{ session_id, event_type, title, method?, endpoint?, status_code?, duration_ms?, details? }` |
| DELETE | `/api/events/:id` | Delete one event |

Event types: `user_action`, `api_request`, `api_response`, `error`, `info`.

## Project layout
```
backend/src   controllers/ routes/ middleware/sanitize.js db.js server.js
frontend/src  pages/ components/ context/ services/ (api.jsx, tracker.js) utils/
database/schema.sql
```

> Never commit `backend/.env` — it holds your database password (it is in `.gitignore`).
