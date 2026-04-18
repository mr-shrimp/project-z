# 🎮 Mini-Game Platform (Private Multiplayer System)

## 🧠 Overview

This project is a **private multiplayer mini-game platform** designed for a small group of friends (3–5 players). It supports:

* Persistent user accounts
* Lobby-based gameplay
* Turn-based (initial) and real-time (future) games
* Modular, plug-and-play game architecture
* Server-authoritative game logic

---

## 🏗️ Architecture

```
- client/
  - app (React shell)
  - lobby system
  - chat UI
  - game container
  - games/
    - guessing-game/

- server/
  - api (FastAPI)
  - realtime (WebSockets)
  - game engine
  - game modules/
    - guessing_game.py

- infrastructure/
  - postgres
  - redis (optional)
```

---

## ⚙️ Tech Stack

### Frontend

* React (TypeScript)
* Phaser (for game rendering when needed)
* WebSockets (real-time communication)

### Backend

* FastAPI (Python)
* WebSockets (FastAPI native)

### Database

* PostgreSQL

### Optional (Future)

* Redis (session caching, scaling)

---

## 🔁 Core Systems

### Lobby Model

```
Lobby {
  id: string
  host_id: string
  players: Player[]
  game_id: string | null
  state: "waiting" | "playing"
}
```

Players remain in the same lobby across multiple games.

---

### Game Session Model

```
GameSession {
  id: string
  lobby_id: string
  game_type: string
  state: object
  current_turn: player_id
  status: "active" | "finished"
}
```

---

## 🧩 Game Plugin System

Each game is a drop-in module.

### Interface (Python)

```python
class GameModule:
    def init(self, players: list):
        pass

    def handle_action(self, player_id: str, action: dict):
        pass

    def get_state(self) -> dict:
        pass

    def is_finished(self) -> bool:
        pass

    def get_winner(self):
        pass
```

---

### Dynamic Loader

```python
import importlib

def load_game(game_name: str):
    module = importlib.import_module(f"game_modules.{game_name}")
    return module.Game()
```

---

## 🔌 Real-Time System

### Event Flow

```
Client → player_action
Server → validate
Server → update state
Server → broadcast state_update
Clients → re-render
```

---

### WebSocket Message Example

```json
{
  "type": "player_action",
  "payload": {
    "action": "guess",
    "value": 42
  }
}
```

---

## 🎮 First Game: Number Guessing

### Rules

* Each player selects a secret number
* Players take turns guessing another player's number
* The target player responds:

  * "higher"
  * "lower"
  * "correct"
* First correct guess wins

---

### Game State

```python
{
  "players": [...],
  "numbers": {
    player_id: int
  },
  "guesses": [
    {
      "from": player_id,
      "target": player_id,
      "value": int,
      "result": str
    }
  ],
  "current_turn": player_id,
  "winner": None
}
```

---

### Actions

```python
# Set number
{ "type": "set_number", "value": 50 }

# Make guess
{ "type": "guess", "target": "player2", "value": 30 }

# Respond to guess
{ "type": "response", "result": "higher" }
```

---

### Important

* Server stores all numbers
* Server validates all responses
* Prevents cheating (server-authoritative)

---

## 💬 Chat System

### Model

```
ChatMessage {
  id
  lobby_id
  sender_id
  message
  timestamp
}
```

### Flow

```
Client sends "chat_message" → Server broadcasts to lobby
```

---

## 👤 Authentication

* Email + Password
* JWT-based authentication

---

## 🗄️ Database Schema (V1)

```sql
users
- id
- email
- password_hash

friends
- user_id
- friend_id

lobbies
- id
- host_id

lobby_players
- lobby_id
- user_id

game_sessions
- id
- lobby_id
- game_type
- state (JSON)

chat_messages
- id
- lobby_id
- sender_id
- message
```

---

## 🖥️ Frontend Structure

```
src/
- pages/
  - LobbyPage
  - GamePage

- components/
  - ChatPanel
  - PlayerList
  - GameContainer

- games/
  - guessing-game/
    - index.ts
    - ui.ts
```

---

## 🔄 User Flow

```
Login →
Home →
Create Lobby →
Invite Link →
Players Join →
Start Game →
Play →
Return to Lobby →
Start Next Game
```

---

## 🚧 Constraints

### Avoid

* Complex physics engines early
* Overengineering matchmaking
* Microservices architecture
* Heavy real-time complexity (initially)

### Focus On

* Modular game system
* Clean interfaces
* Server-authoritative logic
* Simple UX

---

## 🚀 Development Roadmap

### Phase 1: Foundation

* Authentication (login/register)
* Lobby creation + invite links
* WebSocket connection

### Phase 2: Gameplay Core

* Game engine system
* Guessing game implementation
* Turn handling

### Phase 3: Social Features

* Friends system
* Chat system

### Phase 4: Polish

* UI improvements
* Game switching within lobby

---

## 🧠 Key Principle

> Build a **platform**, not individual games.

All games should be:

* Modular
* Replaceable
* Independent of core systems

---

## 🔮 Future Possibilities

* Additional mini-games
* Real-time multiplayer games
* Ranking / leaderboards
* Replay system
* AI opponents

---

## 📌 Summary

This project is a **modular, server-driven multiplayer game platform** optimized for:

* Small groups
* Rapid iteration
* Long-term extensibility

The architecture prioritizes:

* Simplicity
* Scalability (when needed)
* Clean separation of concerns

---
