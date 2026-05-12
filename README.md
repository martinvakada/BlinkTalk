# BlinkTalk 💬

A real-time group chat application built with the **MERN stack** (MongoDB, Express, React, Node.js) and **Socket.io**. Users can sign up, log in, and instantly chat in topic-based rooms with live online/offline presence indicators and unread message notifications.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
  - [1. Clone the Repository](#1-clone-the-repository)
  - [2. Configure Environment Variables](#2-configure-environment-variables)
  - [3. Install Dependencies](#3-install-dependencies)
  - [4. Run the Application](#4-run-the-application)
- [Available Chat Rooms](#available-chat-rooms)
- [API Endpoints](#api-endpoints)
- [Socket.io Events](#socketio-events)
- [Database Models](#database-models)
- [Scripts Reference](#scripts-reference)
- [Environment Variables](#environment-variables)

---

## Features

- **User Authentication** — Sign up and log in with email/password (passwords hashed with bcrypt)
- **Real-time Messaging** — Instant message delivery via Socket.io WebSockets
- **Topic-based Chat Rooms** — Pre-defined rooms: General, Tech, Finance, Crypto
- **Online/Offline Presence** — Live member list showing who is currently online
- **Unread Notifications** — Per-room unread message badge counts
- **Message History** — Messages grouped and displayed by date on room join
- **Persistent Sessions** — Redux Persist keeps you logged in across page refreshes
- **Profile Pictures** — Avatars stored per user account

---

## Tech Stack

### Frontend (`client/`)
| Technology | Purpose |
|---|---|
| React 17 | UI framework |
| React Router v6 | Client-side routing |
| Redux Toolkit + Redux Persist | Global state management with persistence |
| React Bootstrap + Bootstrap 5 | UI component library and styling |
| Socket.io Client | Real-time WebSocket communication |
| RTK Query (`appApi`) | REST API calls |

### Backend (`server/`)
| Technology | Purpose |
|---|---|
| Node.js + Express | HTTP server and REST API |
| Socket.io | WebSocket server for real-time events |
| MongoDB Atlas + Mongoose | Cloud database and ODM |
| bcrypt | Password hashing |
| dotenv | Environment variable management |
| validator | Email format validation |
| nodemon | Auto-restart in development |

---

## Project Structure

```
BlinkTalk/
├── .gitignore
├── README.md
│
├── client/                         # React frontend
│   ├── public/
│   │   └── index.html
│   └── src/
│       ├── App.js                  # Root component with routing
│       ├── store.js                # Redux store with redux-persist
│       ├── assets/                 # Static images/icons
│       ├── components/
│       │   ├── MessageForm.js      # Chat message input and display
│       │   ├── Navigation.js       # Top navbar
│       │   └── Sidebar.js          # Room list and member list
│       ├── context/
│       │   └── appContext.js       # Socket.io context provider
│       ├── features/
│       │   └── userSlice.js        # Redux slice for user auth state
│       ├── pages/
│       │   ├── Chat.js             # Main chat layout (Sidebar + MessageForm)
│       │   ├── Home.js             # Landing page
│       │   ├── Login.js            # Login page
│       │   └── Signup.js           # Registration page
│       └── services/
│           └── appApi.js           # RTK Query API service
│
└── server/                         # Node.js + Express backend
    ├── server.js                   # Entry point — Express + Socket.io setup
    ├── connection.js               # MongoDB Atlas connection
    ├── models/
    │   ├── User.js                 # User schema (name, email, password, status)
    │   └── Message.js              # Message schema (content, room, time, date)
    └── routes/
        └── userRoutes.js           # POST /users (register), POST /users/login
```

---

## Prerequisites

Make sure you have the following installed before running the project:

- [Node.js](https://nodejs.org/) v14 or higher
- [npm](https://www.npmjs.com/) v6 or higher
- A [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) account and cluster

---

## Getting Started

### 1. Clone the Repository

```cmd
git clone https://github.com/your-username/BlinkTalk.git
cd BlinkTalk
```

### 2. Configure Environment Variables

Create a `.env` file inside the `server/` folder:

```cmd
cd server
copy NUL .env
```

Open `server/.env` and add your MongoDB credentials:

```env
DB_USER=your_mongodb_username
DB_PW=your_mongodb_password
```

> **Never commit `.env` to Git.** It is already listed in `.gitignore`.

### 3. Install Dependencies

Open two separate terminals and install dependencies for both the server and the client.

**Terminal 1 — Server:**
```cmd
cd BlinkTalk\server
npm install
```

**Terminal 2 — Client:**
```cmd
cd BlinkTalk\client
npm install
```

### 4. Run the Application

**Start the backend server** (Terminal 1):
```cmd
cd BlinkTalk\server
npm run dev
```
The server will start on **http://localhost:5001**

**Start the React frontend** (Terminal 2):
```cmd
cd BlinkTalk\client
npm start
```
The app will open in your browser at **http://localhost:3000**

---

## Available Chat Rooms

The following rooms are pre-configured in `server/server.js`:

| Room | Topic |
|---|---|
| `general` | Open discussion |
| `tech` | Technology and programming |
| `finance` | Personal finance and markets |
| `crypto` | Cryptocurrency and blockchain |

---

## API Endpoints

Base URL: `http://localhost:5001`

| Method | Endpoint | Description | Body |
|---|---|---|---|
| `POST` | `/users` | Register a new user | `{ name, email, password, picture }` |
| `POST` | `/users/login` | Log in an existing user | `{ email, password }` |
| `DELETE` | `/logout` | Log out the current user | `{ _id, newMessages }` |

---

## Socket.io Events

| Event | Direction | Description |
|---|---|---|
| `new-user` | Client → Server | Fired on connect; server broadcasts updated member list |
| `new-user` | Server → Client | Delivers the full list of registered users |
| `join-room` | Client → Server | Client switches to a new room; receives room message history |
| `room-messages` | Server → Client | Full message history for the current room, grouped by date |
| `message-room` | Client → Server | Client sends a new message to a room |
| `notifications` | Server → Client | Notifies other clients of a new message in a room |

---

## Database Models

### User

| Field | Type | Notes |
|---|---|---|
| `name` | String | Required |
| `email` | String | Required, unique, validated |
| `password` | String | Required, hashed with bcrypt (salt rounds: 10) |
| `picture` | String | Optional profile picture URL |
| `status` | String | `"online"` or `"offline"` |
| `newMessages` | Object | Map of room → unread message count |

### Message

| Field | Type | Notes |
|---|---|---|
| `content` | String | The message text |
| `from` | Object | Sender user object |
| `to` | String | Target room name |
| `time` | String | Time the message was sent |
| `date` | String | Date the message was sent (used for grouping) |

---

## Scripts Reference

### Server (`server/`)
| Script | Command | Description |
|---|---|---|
| `start` | `npm start` | Run with Node.js (production) |
| `dev` | `npm run dev` | Run with nodemon (auto-restart on file change) |

### Client (`client/`)
| Script | Command | Description |
|---|---|---|
| `start` | `npm start` | Start React development server |
| `build` | `npm run build` | Build optimized production bundle |
| `test` | `npm test` | Run test suite |

---

## Environment Variables

| Variable | Location | Description |
|---|---|---|
| `DB_USER` | `server/.env` | MongoDB Atlas username |
| `DB_PW` | `server/.env` | MongoDB Atlas password |

---

## License

This project is open-source and available under the [ISC License](https://opensource.org/licenses/ISC).
