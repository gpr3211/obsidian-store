## Callbacks

These are callbacks which can be fired by calling [SteamAPI_RunCallbacks](https://partner.steamgames.com/doc/api/steam_api#SteamAPI_RunCallbacks). Many of these will be fired directly in response to the member functions of `ISteamMatchmaking`.

## FavoritesListAccountsUpdated_t

  
  

|Name|Type|Description|
|---|---|---|
|**m_eResult**|[EResult](https://partner.steamgames.com/doc/api/steam_api#EResult)||

## FavoritesListChanged_t

A server was added/removed from the favorites list, you should refresh now.  
  

|Name|Type|Description|
|---|---|---|
|**m_nIP**|[uint32](https://partner.steamgames.com/doc/api/steam_api#uint32)|An IP of 0 means reload the whole list, any other value means just one server. This is host order, i.e 127.0.0.1 == 0x7f000001.|
|**m_nQueryPort**|[uint32](https://partner.steamgames.com/doc/api/steam_api#uint32)|If `m_nIP` is set then this is the new servers query port, in host order.|
|**m_nConnPort**|[uint32](https://partner.steamgames.com/doc/api/steam_api#uint32)|If `m_nIP` is set then this is the new servers connection port, in host order.|
|**m_nAppID**|[uint32](https://partner.steamgames.com/doc/api/steam_api#uint32)|If `m_nIP` is set then this is the App ID the game server belongs to.|
|**m_nFlags**|[uint32](https://partner.steamgames.com/doc/api/steam_api#uint32)|If `m_nIP` is set then this returns whether the the server is on the favorites list or the history list. See [k_unFavoriteFlagNone](https://partner.steamgames.com/doc/api/ISteamMatchmaking#k_unFavoriteFlagNone) for more information.|
|**m_bAdd**|bool|If `m_nIP` is set then this is whether the server was added to the list (**true**) or removed (**false**) from it.|
|**m_unAccountId**|[AccountID_t](https://partner.steamgames.com/doc/api/steam_api#AccountID_t)||

## LobbyChatMsg_t

A chat (text or binary) message for this lobby has been received. After getting this you must use [GetLobbyChatEntry](https://partner.steamgames.com/doc/api/ISteamMatchmaking#GetLobbyChatEntry) to retrieve the contents of this message.  
  

|Name|Type|Description|
|---|---|---|
|**m_ulSteamIDLobby**|[uint64](https://partner.steamgames.com/doc/api/steam_api#uint64)|The Steam ID of the lobby this message was sent in.|
|**m_ulSteamIDUser**|[uint64](https://partner.steamgames.com/doc/api/steam_api#uint64)|Steam ID of the user who sent this message. Note that it could have been the local user.|
|**m_eChatEntryType**|[uint8](https://partner.steamgames.com/doc/api/steam_api#uint8)|Type of message received. This is actually a [EChatEntryType](https://partner.steamgames.com/doc/api/steam_api#EChatEntryType).|
|**m_iChatID**|[uint32](https://partner.steamgames.com/doc/api/steam_api#uint32)|The index of the chat entry to use with [GetLobbyChatEntry](https://partner.steamgames.com/doc/api/ISteamMatchmaking#GetLobbyChatEntry), this is not valid outside of the scope of this callback and should never be stored.|

  
**Associated Functions:** [SendLobbyChatMsg](https://partner.steamgames.com/doc/api/ISteamMatchmaking#SendLobbyChatMsg)

## LobbyChatUpdate_t

A lobby chat room state has changed, this is usually sent when a user has joined or left the lobby.  
  

|Name|Type|Description|
|---|---|---|
|**m_ulSteamIDLobby**|[uint64](https://partner.steamgames.com/doc/api/steam_api#uint64)|The Steam ID of the lobby.|
|**m_ulSteamIDUserChanged**|[uint64](https://partner.steamgames.com/doc/api/steam_api#uint64)|The user who's status in the lobby just changed - can be recipient.|
|**m_ulSteamIDMakingChange**|[uint64](https://partner.steamgames.com/doc/api/steam_api#uint64)|Chat member who made the change. This can be different from `m_ulSteamIDUserChanged` if kicking, muting, etc. For example, if one user kicks another from the lobby, this will be set to the id of the user who initiated the kick.|
|**m_rgfChatMemberStateChange**|[uint32](https://partner.steamgames.com/doc/api/steam_api#uint32)|Bitfield of [EChatMemberStateChange](https://partner.steamgames.com/doc/api/ISteamMatchmaking#EChatMemberStateChange) values.|

## LobbyCreated_t

Result of our request to create a Lobby. At this point, the lobby has been joined and is ready for use, a [LobbyEnter_t](https://partner.steamgames.com/doc/api/ISteamMatchmaking#LobbyEnter_t) callback will also be received (since the local user is joining their own lobby).  
  

|Name|Type|Description|
|---|---|---|
|**m_eResult**|[EResult](https://partner.steamgames.com/doc/api/steam_api#EResult)|The result of the operation.  <br>  <br>Possible values:  <br><br>- [k_EResultOK](https://partner.steamgames.com/doc/api/steam_api#k_EResultOK) - The lobby was successfully created.  <br>    <br>- [k_EResultFail](https://partner.steamgames.com/doc/api/steam_api#k_EResultFail) - The server responded, but with an unknown internal error.  <br>    <br>- [k_EResultTimeout](https://partner.steamgames.com/doc/api/steam_api#k_EResultTimeout) - The message was sent to the Steam servers, but it didn't respond.  <br>    <br>- [k_EResultLimitExceeded](https://partner.steamgames.com/doc/api/steam_api#k_EResultLimitExceeded) - Your game client has created too many lobbies and is being rate limited.  <br>    <br>- [k_EResultAccessDenied](https://partner.steamgames.com/doc/api/steam_api#k_EResultAccessDenied) - Your game isn't set to allow lobbies, or your client does haven't rights to play the game  <br>    <br>- [k_EResultNoConnection](https://partner.steamgames.com/doc/api/steam_api#k_EResultNoConnection) - Your Steam client doesn't have a connection to the back-end.|
|**m_ulSteamIDLobby**|[uint64](https://partner.steamgames.com/doc/api/steam_api#uint64)|The Steam ID of the lobby that was created, 0 if failed.|

  
**Associated Functions:** [CreateLobby](https://partner.steamgames.com/doc/api/ISteamMatchmaking#CreateLobby)

## LobbyDataUpdate_t

The lobby metadata has changed.  
  
If `m_ulSteamIDMember` is a user in the lobby, then use [GetLobbyMemberData](https://partner.steamgames.com/doc/api/ISteamMatchmaking#GetLobbyMemberData) to access per-user details; otherwise, if `m_ulSteamIDMember` == `m_ulSteamIDLobby`, use [GetLobbyData](https://partner.steamgames.com/doc/api/ISteamMatchmaking#GetLobbyData) to access the lobby metadata.  
  

|Name|Type|Description|
|---|---|---|
|**m_ulSteamIDLobby**|[uint64](https://partner.steamgames.com/doc/api/steam_api#uint64)|The Steam ID of the Lobby.|
|**m_ulSteamIDMember**|[uint64](https://partner.steamgames.com/doc/api/steam_api#uint64)|Steam ID of either the member whose data changed, or the room itself.|
|**m_bSuccess**|[uint8](https://partner.steamgames.com/doc/api/steam_api#uint8)|**true** if the lobby data was successfully changed, otherwise **false**.|

  
**Associated Functions:** [CreateLobby](https://partner.steamgames.com/doc/api/ISteamMatchmaking#CreateLobby), [JoinLobby](https://partner.steamgames.com/doc/api/ISteamMatchmaking#JoinLobby), [SetLobbyMemberData](https://partner.steamgames.com/doc/api/ISteamMatchmaking#SetLobbyMemberData), [RequestLobbyData](https://partner.steamgames.com/doc/api/ISteamMatchmaking#RequestLobbyData), [SetLobbyOwner](https://partner.steamgames.com/doc/api/ISteamMatchmaking#SetLobbyOwner)

## LobbyEnter_t

Recieved upon attempting to enter a lobby. Lobby metadata is available to use immediately after receiving this.  
  

|Name|Type|Description|
|---|---|---|
|**m_ulSteamIDLobby**|[uint64](https://partner.steamgames.com/doc/api/steam_api#uint64)|The steam ID of the Lobby you have entered.|
|**m_rgfChatPermissions**|[uint32](https://partner.steamgames.com/doc/api/steam_api#uint32)|Unused - Always 0.|
|**m_bLocked**|bool|If true, then only invited users may join.|
|**m_EChatRoomEnterResponse**|[uint32](https://partner.steamgames.com/doc/api/steam_api#uint32)|This is actually a [EChatRoomEnterResponse](https://partner.steamgames.com/doc/api/steam_api#EChatRoomEnterResponse) value. This will be set to [k_EChatRoomEnterResponseSuccess](https://partner.steamgames.com/doc/api/steam_api#k_EChatRoomEnterResponseSuccess) if the lobby was successfully joined, otherwise it will be [k_EChatRoomEnterResponseError](https://partner.steamgames.com/doc/api/steam_api#k_EChatRoomEnterResponseError).|

  
**Associated Functions:** [CreateLobby](https://partner.steamgames.com/doc/api/ISteamMatchmaking#CreateLobby), [JoinLobby](https://partner.steamgames.com/doc/api/ISteamMatchmaking#JoinLobby)

## LobbyGameCreated_t

A game server has been set via [SetLobbyGameServer](https://partner.steamgames.com/doc/api/ISteamMatchmaking#SetLobbyGameServer) for all of the members of the lobby to join. It's up to the individual clients to take action on this; the typical game behavior is to leave the lobby and connect to the specified game server; but the lobby may stay open throughout the session if desired.  
  

|Name|Type|Description|
|---|---|---|
|**m_ulSteamIDLobby**|[uint64](https://partner.steamgames.com/doc/api/steam_api#uint64)|The lobby that set the game server.|
|**m_ulSteamIDGameServer**|[uint64](https://partner.steamgames.com/doc/api/steam_api#uint64)|The Steam ID of the game server, if it's set.|
|**m_unIP**|[uint32](https://partner.steamgames.com/doc/api/steam_api#uint32)|The IP address of the game server in host order, i.e 127.0.0.1 == 0x7f000001, if it's set.|
|**m_usPort**|[uint16](https://partner.steamgames.com/doc/api/steam_api#uint16)|The connection port of the game server, in host order, if it's set.|

  
**Associated Functions:** [SetLobbyGameServer](https://partner.steamgames.com/doc/api/ISteamMatchmaking#SetLobbyGameServer)

## LobbyInvite_t

Someone has invited you to join a Lobby. Normally you don't need to do anything with this, as the Steam UI will also display a '<user> has invited you to the lobby, join?' notification and message.  
  
If the user outside a game chooses to join, your game will be launched with the parameter `+connect_lobby <64-bit lobby id>`, or with the callback [GameLobbyJoinRequested_t](https://partner.steamgames.com/doc/api/ISteamFriends#GameLobbyJoinRequested_t) if they're already in-game.  
  

|Name|Type|Description|
|---|---|---|
|**m_ulSteamIDUser**|[uint64](https://partner.steamgames.com/doc/api/steam_api#uint64)|Steam ID of the person that sent the invite.|
|**m_ulSteamIDLobby**|[uint64](https://partner.steamgames.com/doc/api/steam_api#uint64)|Steam ID of the lobby we're invited to.|
|**m_ulGameID**|[uint64](https://partner.steamgames.com/doc/api/steam_api#uint64)|Game ID of the lobby we're invited to.|

## LobbyKicked_t

Currently unused! If you want to implement kicking at this time then do it with a special packet sent with [SendLobbyChatMsg](https://partner.steamgames.com/doc/api/ISteamMatchmaking#SendLobbyChatMsg), when the user gets the packet they should call [LeaveLobby](https://partner.steamgames.com/doc/api/ISteamMatchmaking#LeaveLobby).  
  

|Name|Type|Description|
|---|---|---|
|**m_ulSteamIDLobby**|[uint64](https://partner.steamgames.com/doc/api/steam_api#uint64)||
|**m_ulSteamIDAdmin**|[uint64](https://partner.steamgames.com/doc/api/steam_api#uint64)||
|**m_bKickedDueToDisconnect**|[uint8](https://partner.steamgames.com/doc/api/steam_api#uint8)||

## LobbyMatchList_t

Result when requesting the lobby list. You should iterate over the returned lobbies with [GetLobbyByIndex](https://partner.steamgames.com/doc/api/ISteamMatchmaking#GetLobbyByIndex), from 0 to `m_nLobbiesMatching`-1.  
  

|Name|Type|Description|
|---|---|---|
|**m_nLobbiesMatching**|[uint32](https://partner.steamgames.com/doc/api/steam_api#uint32)|Number of lobbies that matched search criteria and we have Steam IDs for.|

  
**Associated Functions:** [RequestLobbyList](https://partner.steamgames.com/doc/api/ISteamMatchmaking#RequestLobbyList)

## PSNGameBootInviteResult_t

Deprecated - PS3 only.  
  

|Name|Type|Description|
|---|---|---|
|**m_bGameBootInviteExists**|bool||
|**m_steamIDLobby**|[CSteamID](https://partner.steamgames.com/doc/api/steam_api#CSteamID)||

## Enums

These are enums which are defined for use with ISteamMatchmaking.

## EChatMemberStateChange

Flags describing how a users lobby state has changed. This is provided from [LobbyChatUpdate_t](https://partner.steamgames.com/doc/api/ISteamMatchmaking#LobbyChatUpdate_t).  
  
