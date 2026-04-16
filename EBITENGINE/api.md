#### **Connection & Game Setup**

- `"join"` – Sent by a player to join the game.
- { "action": "join", "name": "Player1" }
{ "action": "joined", "player_id": 1 }
{ "action": "start_game", "trump_card": { "t": 2, "c": 5 } }
{ "action": "deal", "player_id": 1, "cards": [{ "t": 1, "c": 3 }, { "t": 2, "c": 7 }] }
{ "action": "your_turn", "player_id": 1 }

{ "action": "play_card", "card": { "t": 1, "c": 3 } }

{ "action": "card_played", "player_id": 1, "card": { "t": 1, "c": 3 } }  
- broadcast
{ "action": "next_turn", "player_id": 2 }

### special
{ "action": "declare_20_40", "player_id": 1, "card": { "t": 2, "c": 5 }, "points": 20 }

{ "action": "close_talon"}
### resolution
{ "action": "trick_result", "winner": 1, "points": 10 }
{ "action": "next_turn", "player_id": 2 }
{ "action": "game_over", "winner": 1, "score": 66 }
### **Game Loop Flow**

1. **Players Join** (`"join"`)
2. **Game Starts** (`"start_game"`)
3. **Cards Dealt** (`"deal"`)
4. **Turns Rotate** (`"your_turn" →` "play_card"`→`"trick_result"`→`"next_turn"`)
5. **Game Ends** (`"game_over"`)