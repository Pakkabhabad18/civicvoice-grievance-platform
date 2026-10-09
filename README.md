# CivicVoice: civic grievance management platform

Citizens report civic problems (potholes, garbage, water and power issues) with photos and a pinned location. An administrator verifies each complaint and routes it to the right municipal department, the department resolves it, and the citizen is kept informed by SMS at every step. Every complaint is visible on a public city map.

Team project for the Software Systems Development course at IIIT Hyderabad (Aug–Dec 2025).

**[Demo video](https://youtu.be/XP3ObswnLxI)** · **[Presentation](https://gamma.app/docs/CivicVoice-orq6xynmw7e701h?mode=doc)**

---

## The problem

Existing ways of reporting civic issues are slow and opaque. Citizens rarely hear back after they complain, and authorities get scattered, unverified reports with no exact location. CivicVoice closes that loop between citizens and the municipality.

## Who uses it

| User | What they do |
|---|---|
| **Citizen** | Signs in with mobile OTP, reports an issue with text, photos or video and a map pin, tracks its status, upvotes others' complaints and rates the resolution |
| **Administrator** | Reviews each complaint with an AI-generated summary, verifies it, assigns a department, escalates or resolves it, and watches a dashboard of department load and resolution time |
| **Department official** | Sees complaints assigned to the department, updates progress and uploads proof of resolution |

## How a complaint moves

```
Citizen reports ──► pending ──► verified ──► forwarded ──► in progress ──► resolved
                                                  │
                         unresolved for 7 days ───┴──► escalated (auto)
```

1. The citizen submits a complaint; a guided chatbot helps collect the details.
2. Gemini summarises the complaint and classifies it (water, waste, roads, electricity, other).
3. The administrator verifies it and assigns a department, which is notified by email.
4. The department works on it and uploads proof; the administrator marks it resolved.
5. The citizen gets an SMS at verification and resolution, and can rate the outcome.
6. A daily scheduled job escalates any complaint left unresolved for 7 days.

## Features

- **OTP sign-in** for citizens (Twilio) and JWT-based sessions; hashed passwords for department accounts
- **Complaint submission** with up to 5 photos or videos and an exact location on the map
- **Public complaint map** (Leaflet and OpenStreetMap) with geocoding and geospatial queries
- **AI summarisation and classification** of complaints with Gemini, with fallback across models
- **SLA escalation**: a daily job that escalates complaints unresolved for 7 days
- **Notifications**: SMS to citizens (Twilio) and email to departments (Nodemailer)
- **Admin dashboard**: totals by status and department, escalations and average resolution time
- **Citizen feedback**: upvotes on complaints and star ratings after resolution

## Tech stack

| Layer | Technology |
|---|---|
| Frontend | React, Vite, Tailwind CSS, Framer Motion, Leaflet / React-Leaflet, Axios |
| Backend | Node.js, Express, REST APIs, JWT |
| Database | MongoDB Atlas (Mongoose), 2dsphere index for map queries |
| Integrations | Twilio (OTP and SMS), Nodemailer (email), OpenStreetMap (tiles and geocoding), Google Gemini (AI) |
| Scheduling | node-cron (daily escalation job) |

## Repository layout

```
client/   React + Vite frontend (citizen app, admin dashboard, department view)
server/   Express API: routes, controllers, Mongoose models, escalation job, utilities
```

## Running it locally

**1. Backend**

```bash
cd server
npm install
node server.js          # runs on http://localhost:5001
```

Create `server/.env` with:

| Variable | Purpose |
|---|---|
| `MONGO_URI` | MongoDB Atlas connection string |
| `JWT_SECRET` | Secret used to sign JWTs |
| `TWILIO_SID`, `TWILIO_AUTH_TOKEN`, `TWILIO_PHONE` | OTP and SMS |
| `EMAIL_USER`, `EMAIL_PASS` | Email notifications (Gmail app password) |
| `GEMINI_API_KEY` | Complaint summarisation |
| `FRONTEND_ORIGIN` | Frontend URL for CORS (default `http://localhost:5173`) |
| `PORT` | Optional, default `5001` |

**2. Frontend**

```bash
cd client
npm install
npm run dev             # runs on http://localhost:5173
```

## Team

Prakash Bhabad, Deepti Koranga, Harsh Raj, Chaitanya Prabhu and Lalithamsh — IIIT Hyderabad.
