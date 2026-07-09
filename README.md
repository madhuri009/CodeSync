<div align="center">

# CodeSync

**A real-time collaborative code editor for pairing, teaching, and mock interviews — in the browser.**

Write, run, and discuss code together in the same room, live — like Google Docs, but for code.

[![React](https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=white)](https://react.dev)
[![Node.js](https://img.shields.io/badge/Node.js-Express-339933?logo=node.js&logoColor=white)](https://nodejs.org)
[![MongoDB](https://img.shields.io/badge/MongoDB-Mongoose-47A248?logo=mongodb&logoColor=white)](https://www.mongodb.com)
[![Socket.IO](https://img.shields.io/badge/Socket.IO-realtime-010101?logo=socket.io&logoColor=white)](https://socket.io)
[![Yjs](https://img.shields.io/badge/Yjs-CRDT-orange)](https://yjs.dev)
[![License](https://img.shields.io/badge/license-ISC-lightgrey)](#license)

</div>

<p align="center">
  <img src="client/src/assets/hero.png" alt="CodeSync preview" width="600"/>
</p>

---

## Features

| Feature | Description |
|---|---|
| Live collaborative editing | Every keystroke syncs instantly across all participants using Yjs (CRDTs) — conflict-free even when everyone types at once |
| Multiplayer cursors | See each participant's cursor and selection in real time, color-coded per user |
| Rooms | Create a room, share the ID, and anyone can join and start coding together |
| Live chat | In-room chat panel to discuss without leaving the editor |
| Run code instantly | Execute code via Judge0 / Piston and see output in a built-in console |
| Session recording & playback | Record an entire session — edits, chat, everything — and replay it step-by-step later (useful for reviewing mock interviews) |
| Authentication | JWT-based sign up / login, so rooms and sessions are tied to real users |

---

## Tech Stack

<table>
<tr>
<td valign="top" width="50%">

**Frontend**
- React 19 + Vite
- Tailwind CSS
- Monaco Editor (`@monaco-editor/react`) — the editor behind VS Code
- Yjs + `y-monaco` + `y-websocket` — real-time CRDT sync
- Socket.IO client
- React Router

</td>
<td valign="top" width="50%">

**Backend**
- Node.js + Express
- Socket.IO — editor sync, cursors, chat
- MongoDB + Mongoose
- JWT + bcrypt — authentication
- Judge0 / Piston — sandboxed code execution

</td>
</tr>
</table>

**Infra:** Docker Compose spins up MongoDB and a local Piston execution engine for development.

---

## Project Structure

```
CodeSync/
├── client/                  React frontend (Vite)
│   └── src/
│       ├── components/      editor, chat, layout, output, common UI
│       ├── context/         Auth & Socket context
│       ├── hooks/           useEditor, useSocket
│       ├── pages/           Home, Login, Register, CreateRoom, JoinRoom, EditorPage
│       └── services/        api, socket, compiler clients
├── server/                  Express backend
│   ├── controllers/         auth, room, compiler logic
│   ├── models/               User, Room, Message, Recording
│   ├── sockets/               editorSocket, chatSocket
│   ├── recording/             session record + playback
│   └── services/compiler/     judge0.js, piston.js
├── shared/                  constants & socket event names shared by client/server
├── docs/                    architecture & API notes
└── docker-compose.yml
```

---

## Getting Started

### Prerequisites
- Node.js v18+
- MongoDB (local or Atlas)
- Docker (optional, for local Piston code execution)

### Setup

**1. Clone the repo**
```bash
git clone https://github.com/madhuri009/CodeSync.git
cd CodeSync
```

**2. Install dependencies**
```bash
npm install
npm install --prefix client
npm install --prefix server
```

**3. Configure environment**

Create a `.env` file inside `server/`:
```env
PORT=5001
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=your_jwt_secret
CLIENT_URL=http://localhost:5174
PISTON_URL=https://emkc.org/api/v2/piston
```

**4. (Optional) Start MongoDB & a local Piston engine**
```bash
docker-compose up -d
```

**5. Run the app**
```bash
npm run dev
```

| Service | URL |
|---|---|
| Frontend | http://localhost:5174 |
| Backend | http://localhost:5001 |

---

## How It Works

1. A user joins a room — the client connects via Socket.IO and syncs the editor's document using Yjs, merging every keystroke in real time without overwriting anyone else's changes.
2. Cursor position and selection broadcast separately via Yjs Awareness, so participants see each other moving around the file live.
3. Hitting Run sends the current code and language to the backend, which forwards it to Judge0/Piston and streams back stdout/stderr.
4. Session recording logs every edit and chat event with a timestamp per room, which can be replayed later step-by-step.

---

## License

ISC
