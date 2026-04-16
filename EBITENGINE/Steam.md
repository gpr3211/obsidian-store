```go
package puregosteamworks

import (
	"log"
	"net"
)

type SteamGameServer struct {
	IP            uint32
	GamePort      uint16
	QueryPort     uint16
	ServerMode    EServerMode
	VersionString string
	IsSecure      bool
	SteamID       string

	players       map[string]PlayerSession
	activeSessions map[string]GameSession
}

type PlayerSession struct {
	SteamID     string
	Username    string
	IsConnected bool
}

type GameSession struct {
	SessionID    string
	Player1      PlayerSession
	Player2      PlayerSession
	CurrentState string
	LastUpdated  int64
}

func NewSteamGameServer(
	ip uint32, 
	gamePort uint16, 
	queryPort uint16, 
	serverMode EServerMode, 
	versionString string,
) *SteamGameServer {
	return &SteamGameServer{
		IP:            ip,
		GamePort:      gamePort,
		QueryPort:     queryPort,
		ServerMode:    serverMode,
		VersionString: versionString,
		players:       make(map[string]PlayerSession),
		activeSessions: make(map[string]GameSession),
	}
}

func (s *SteamGameServer) Initialize() bool {
	success := GamseServerInit(
		s.IP, 
		s.GamePort, 
		s.QueryPort, 
		s.ServerMode, 
		s.VersionString,
	)

	if !success {
		log.Println("Failed to initialize Steam Game Server")
		return false
	}

	// Check server security status
	s.IsSecure = GameServerBSecure(0, nil, 0)

	// Retrieve Steam ID for the game server
	s.SteamID = GameServerGetSteamID("")

	log.Printf("Steam Game Server initialized. Secure: %v, SteamID: %s", 
		s.IsSecure, s.SteamID)

	return true
}

func (s *SteamGameServer) CreateGameSession(player1, player2 string) *GameSession {
	sessionID := generateUniqueSessionID()
	
	session := &GameSession{
		SessionID: sessionID,
		Player1: PlayerSession{SteamID: player1},
		Player2: PlayerSession{SteamID: player2},
		CurrentState: "LOBBY_CREATED",
	}

	s.activeSessions[sessionID] = *session
	return session
}

func (s *SteamGameServer) ValidatePlayerTurn(
	sessionID string, 
	playerSteamID string, 
	turnData []byte,
) bool {
	session, exists := s.activeSessions[sessionID]
	if !exists {
		return false
	}

	// Validate that the player is part of this session
	if session.Player1.SteamID != playerSteamID && 
	   session.Player2.SteamID != playerSteamID {
		return false
	}

	// Implement turn validation logic
	// This would include checking game rules, turn order, etc.
	return true
}

func (s *SteamGameServer) Shutdown() {
	var errMsg steamErrMsg
	GameServerShutdown(&errMsg)
	GameServerActive = false
	log.Println("Steam Game Server shut down")
}

// Helper function to generate unique session IDs
func generateUniqueSessionID() string {
	return uuid.New().String() // Assuming you're using a UUID library
}

// Example usage
func ExampleGameServerUsage() {
	// IP: Convert to uint32, e.g., net.ParseIP("127.0.0.1").To4()
	serverIP := uint32(net.ParseIP("127.0.0.1").To4())

	gameServer := NewSteamGameServer(
		serverIP, 
		7777,    // Game Port
		27015,   // Query Port 
		ServerModeAuthenticationAndSecure, 
		"1.0.0", // Version
	)

	if gameServer.Initialize() {
		// Server ready to accept connections
		session := gameServer.CreateGameSession(
			"steam_player1", 
			"steam_player2",
		)

		// Game session management would continue here
	}
}


```

## 2

```go
// TalonHandler manages the closing of the talon in the Marriage card game
func (g *GameService) HandleCloseTalon(ctx context.Context, player *Player, msg model.CloseTalon) {
    // Validation checks before closing the talon
    if err := g.validateTalonClose(player); err != nil {
        g.Send(ctx, player, map[string]string{"error": err.Error()})
        return
    }

    // Mark talon as closed
    g.Closed = true

    // Broadcast talon closure to all players
    g.Broadcast(ctx, model.TalonClosedMsg{
        Action:   "closed_talon",
        PlayerID: player.ID,
    })

    // Additional game state updates after talon closure
    g.updateGameStateAfterTalonClose(player)
}

// validateTalonClose performs comprehensive validation for talon closure
func (g *GameService) validateTalonClose(player *Player) error {
    // Check if it's the player's turn
    if player != g.CurrentPlayer {
        return fmt.Errorf("not your turn to close the talon")
    }

    // Ensure player has captured at least one hand
    if g.Santase.currentScore[player] == 0 {
        return fmt.Errorf("must have captured a hand to close talon")
    }

    // Check if talon is already closed
    if g.Closed {
        return fmt.Errorf("talon is already closed")
    }

    return nil
}

// updateGameStateAfterTalonClose handles post-closure game mechanics
func (g *GameService) updateGameStateAfterTalonClose(closingPlayer *Player) {
    // Potential additional logic after talon closure
    // Examples:
    // - Adjust turn order
    // - Trigger end-game conditions
    // - Update scoring rules

    // Log the talon closure
    log.Printf("Talon closed by player %s", closingPlayer.ID)

    // Potential Steamworks integration point
    // Send detailed game state update to all players
    g.syncGameStateWithSteam(closingPlayer)
}

// syncGameStateWithSteam ensures Steam lobby is updated with game state
func (g *GameService) syncGameStateWithSteam(closingPlayer *Player) {
    // Serialize game state
    gameStateData := g.serializeGameState()

    // Update Steam lobby data
    steamLobby := g.getSteamLobby()
    steamLobby.SetLobbyData("game_state", gameStateData)

    // Notify players of state change
    steamLobby.BroadcastGameState()
}

// Serialization helper for game state
func (g *GameService) serializeGameState() string {
    // Convert current game state to JSON or another serializable format
    stateData := map[string]interface{}{
        "closed":         g.Closed,
        "current_player": g.CurrentPlayer.ID,
        "scores":         g.Santase.currentScore,
        // Add other relevant game state information
    }

    jsonData, _ := json.Marshal(stateData)
    return string(jsonData)
}


```
