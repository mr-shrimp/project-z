# 🛠️ Development Guide – Mini-Game Platform

This document outlines the **step-by-step development process** to build the platform from scratch to a working V1.

The goal is to **ship early**, not overbuild.

---

# 🚀 Phase 0 – Project Setup

## 0.1 Create Repositories / Structure

```id="p0s1"
project-root/
- client/
- server/
```

---

## 0.2 Backend Setup (FastAPI)

Install dependencies:

```bash id="p0s2"
pip install fastapi uvicorn python-dotenv sqlalchemy psycopg2-binary
```

Create entry point:

```python id="p0s3"
# server/main.py
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def root():
    return {"status": "ok"}
```

Run server:

```bash id="p0s4"
uvicorn main:app --reload
```

---

## 0.3 Frontend Setup (React + TS)

```bash id="p0s5"
npx create-react-app client --template typescript
cd client
npm install
```

---

# 🔐 Phase 1 – Authentication

## 1.1 Database Setup

Create PostgreSQL database and connect via SQLAlchemy.

---

## 1.2 User Model

```python id="p1s1"
User {
  id
  email
  password_hash
}
```

---

## 1.3 Auth Features

* Register endpoint
* Login endpoint
* JWT token generation
* Middleware for protected routes

---

## 1.4 Frontend

* Login page
* Register page
* Store JWT in localStorage

---

# 🏠 Phase 2 – Lobby System

## 2.1 Backend Models

```python id="p2s1"
Lobby {
  id
  host_id
}

LobbyPlayer {
  lobby_id
  user_id
}
```

---

## 2.2 API Endpoints

```text id="p2s2"
POST /lobby/create
POST /lobby/join
GET  /lobby/{id}
```

---

## 2.3 Invite System

* Generate unique lobby ID
* Share link:

```
/lobby/{id}
```

---

## 2.4 Frontend

* Create lobby button
* Join via link
* Lobby screen:

  * Player list
  * Start game button

---

# 🔌 Phase 3 – WebSockets (Real-Time Core)

## 3.1 WebSocket Endpoint

```python id="p3s1"
from fastapi import WebSocket

@app.websocket("/ws/{lobby_id}")
async def websocket_endpoint(websocket: WebSocket, lobby_id: str):
    await websocket.accept()
```

---

## 3.2 Connection Manager

Track:

* Connected users
* Lobby rooms

---

## 3.3 Basic Events

```text id="p3s2"
- join_lobby
- leave_lobby
- chat_message
```

---

## 3.4 Frontend

* Connect to WebSocket on lobby join
* Handle incoming events
* Maintain live player list

---

# 💬 Phase 4 – Chat System

## 4.1 Backend

* Handle "chat_message"
* Broadcast to lobby

---

## 4.2 Frontend

* Chat panel component
* Input + message list

---

# 🎮 Phase 5 – Game Engine Core

## 5.1 Game Interface

```python id="p5s1"
class GameModule:
    def init(self, players): pass
    def handle_action(self, player_id, action): pass
    def get_state(self): pass
    def is_finished(self): pass
    def get_winner(self): pass
```

---

## 5.2 Game Manager

Responsibilities:

* Load game modules
* Store game sessions
* Route player actions to correct game

---

## 5.3 Start Game Flow

```text id="p5s2"
Host clicks "Start Game" →
Server creates GameSession →
Game module initialized →
Broadcast initial state
```

---

# 🎲 Phase 6 – First Game Implementation

## 6.1 Create Module

```text id="p6s1"
server/game_modules/guessing_game.py
```

---

## 6.2 Implement Logic

* Store player numbers
* Handle turns
* Validate guesses
* Determine winner

---

## 6.3 WebSocket Events

```text id="p6s2"
player_action → server
state_update → clients
```

---

## 6.4 Frontend Game UI

Inside:

```text id="p6s3"
client/src/games/guessing-game/
```

Build:

* Number input (setup phase)
* Guess UI
* Turn indicator
* Results display

---

# 🔄 Phase 7 – Game Integration

## 7.1 Game Container Component

Responsibilities:

* Receive state from server
* Render correct game UI
* Send player actions

---

## 7.2 Flow

```text id="p7s1"
Lobby →
Start Game →
GameContainer loads →
Game UI renders →
Play →
Return to lobby
```

---

# 👥 Phase 8 – Friends System

## 8.1 Backend

```python id="p8s1"
Friend {
  user_id
  friend_id
}
```

Endpoints:

```text id="p8s2"
POST /friends/add
GET  /friends/list
```

---

## 8.2 Frontend

* Friends list panel
* Invite friend to lobby

---

# 🧪 Phase 9 – Testing & Stability

## 9.1 Test Cases

* Multiple players joining
* Turn correctness
* Game ending conditions
* Reconnection handling

---

## 9.2 Edge Cases

* Player disconnects mid-game
* Invalid actions
* Duplicate events

---

# 🎨 Phase 10 – UI Polish

* Improve layout
* Add loading states
* Add notifications
* Improve responsiveness (mobile)

---

# 🚀 Phase 11 – Deployment (Later)

* Backend → cloud host
* Frontend → static hosting
* Configure environment variables
* Secure WebSocket connections (wss)

---

# 📌 Development Order Summary

```text id="summary"
1. Setup project
2. Authentication
3. Lobby system
4. WebSockets
5. Chat
6. Game engine
7. First game
8. Game integration
9. Friends system
10. Testing
11. Polish
12. Deploy
```

---

# 🧠 Key Principle

> Always keep the system playable.

At any stage:

* You should be able to **open → join lobby → test something**

Avoid long phases where nothing runs.

---

# ⚠️ Common Pitfalls

* Overengineering too early
* Mixing game logic with UI
* Not validating actions server-side
* Tight coupling between games

---

# ✅ Definition of Done (V1)

You’re finished when:

* Users can log in
* Create/join a lobby
* Invite friends via link
* Chat in lobby
* Start the guessing game
* Play full match
* Return to lobby

---

This is your **execution blueprint**. Follow it sequentially and you’ll have a working system without getting stuck in architecture loops.
