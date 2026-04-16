# REQUEST/FIND
## RequestLobbyList

SteamAPICall_t RequestLobbyList();

Get a filtered list of relevant lobbies.  
  
There can only be one active lobby search at a time. The old request will be canceled if a new one is started. Depending on the users connection to the Steam back-end, this call can take from 300ms to 5 seconds to complete, and has a timeout of 20 seconds.  
  
**NOTE:** To filter the results you MUST call the `AddRequestLobbyList*` functions before calling this. The filters are cleared on each call to this function.  
  
**NOTE:** If [AddRequestLobbyListDistanceFilter](https://partner.steamgames.com/doc/api/ISteamMatchmaking#AddRequestLobbyListDistanceFilter) is not called, [k_ELobbyDistanceFilterDefault](https://partner.steamgames.com/doc/api/ISteamMatchmaking#k_ELobbyDistanceFilterDefault) will be used, which will only find matches in the same or nearby regions.  
  
**NOTE:** This will only return lobbies that are not full, and only lobbies that are [k_ELobbyTypePublic](https://partner.steamgames.com/doc/api/ISteamMatchmaking#k_ELobbyTypePublic) or [k_ELobbyTypeInvisible](https://partner.steamgames.com/doc/api/ISteamMatchmaking#k_ELobbyTypeInvisible), and are set to joinable with [SetLobbyJoinable](https://partner.steamgames.com/doc/api/ISteamMatchmaking#SetLobbyJoinable).  
  
**Returns:** [SteamAPICall_t](https://partner.steamgames.com/doc/api/steam_api#SteamAPICall_t) to be used with a [LobbyMatchList_t](https://partner.steamgames.com/doc/api/ISteamMatchmaking#LobbyMatchList_t) call result.  
**NOTE:** This also returns as a callback for compatibility with older applications, but you should use the call result if possible.  
  
**See Also:** [AddRequestLobbyListStringFilter](https://partner.steamgames.com/doc/api/ISteamMatchmaking#AddRequestLobbyListStringFilter), [AddRequestLobbyListNumericalFilter](https://partner.steamgames.com/doc/api/ISteamMatchmaking#AddRequestLobbyListNumericalFilter), [AddRequestLobbyListNearValueFilter](https://partner.steamgames.com/doc/api/ISteamMatchmaking#AddRequestLobbyListNearValueFilter), [AddRequestLobbyListFilterSlotsAvailable](https://partner.steamgames.com/doc/api/ISteamMatchmaking#AddRequestLobbyListFilterSlotsAvailable), [AddRequestLobbyListDistanceFilter](https://partner.steamgames.com/doc/api/ISteamMatchmaking#AddRequestLobbyListDistanceFilter), [AddRequestLobbyListResultCountFilter](https://partner.steamgames.com/doc/api/ISteamMatchmaking#AddRequestLobbyListResultCountFilter)

# CREATE
## CreateLobby

SteamAPICall_t CreateLobby( ELobbyType eLobbyType, int cMaxMembers );

|Name|Type|Description|
|---|---|---|
|**eLobbyType**|[ELobbyType](https://partner.steamgames.com/doc/api/ISteamMatchmaking#ELobbyType)|The type and visibility of this lobby. This can be changed later via [SetLobbyType](https://partner.steamgames.com/doc/api/ISteamMatchmaking#SetLobbyType).|
|**cMaxMembers**|int|The maximum number of players that can join this lobby. This can not be above 250.|

  
Create a new matchmaking lobby.  
  
**Returns:** [SteamAPICall_t](https://partner.steamgames.com/doc/api/steam_api#SteamAPICall_t) to be used with a [LobbyCreated_t](https://partner.steamgames.com/doc/api/ISteamMatchmaking#LobbyCreated_t) call result.  
Triggers a [LobbyEnter_t](https://partner.steamgames.com/doc/api/ISteamMatchmaking#LobbyEnter_t) callback.  
Triggers a [LobbyDataUpdate_t](https://partner.steamgames.com/doc/api/ISteamMatchmaking#LobbyDataUpdate_t) callback.  
If the results returned via the [LobbyCreated_t](https://partner.steamgames.com/doc/api/ISteamMatchmaking#LobbyCreated_t) call result indicate success then the lobby is joined & ready to use at this point.  
  
The [LobbyEnter_t](https://partner.steamgames.com/doc/api/ISteamMatchmaking#LobbyEnter_t) callback is also received since the local user has joined their own lobby.

## JoinLobby

SteamAPICall_t JoinLobby( CSteamID steamIDLobby );

|Name|Type|Description|
|---|---|---|
|**steamIDLobby**|[CSteamID](https://partner.steamgames.com/doc/api/steam_api#CSteamID)|The Steam ID of the lobby to join.|

  
Joins an existing lobby.  
  
The lobby Steam ID can be obtained either from a search with [RequestLobbyList](https://partner.steamgames.com/doc/api/ISteamMatchmaking#RequestLobbyList), joining on a friend, or from an invite.  
  
**Returns:** [SteamAPICall_t](https://partner.steamgames.com/doc/api/steam_api#SteamAPICall_t) to be used with a [LobbyEnter_t](https://partner.steamgames.com/doc/api/ISteamMatchmaking#LobbyEnter_t) call result.  
Triggers a [LobbyDataUpdate_t](https://partner.steamgames.com/doc/api/ISteamMatchmaking#LobbyDataUpdate_t) callback.

## LeaveLobby

void LeaveLobby( CSteamID steamIDLobby );

|Name|Type|Description|
|---|---|---|
|**steamIDLobby**|[CSteamID](https://partner.steamgames.com/doc/api/steam_api#CSteamID)|The lobby to leave.|

  
Leave a lobby that the user is currently in; this will take effect immediately on the client side, other users in the lobby will be notified by a [LobbyChatUpdate_t](https://partner.steamgames.com/doc/api/ISteamMatchmaking#LobbyChatUpdate_t) callback.
# METADATA

## SetLobbyMemberData

void SetLobbyMemberData( CSteamID steamIDLobby, const char *pchKey, const char *pchValue );

|Name|Type|Description|
|---|---|---|
|**steamIDLobby**|[CSteamID](https://partner.steamgames.com/doc/api/steam_api#CSteamID)|The Steam ID of the lobby to set our metadata in.|
|**pchKey**|const char *|The key to set the data for. This can not be longer than [k_nMaxLobbyKeyLength](https://partner.steamgames.com/doc/api/ISteamMatchmaking#k_nMaxLobbyKeyLength).|
|**pchValue**|const char *|The value to set. This can not be longer than [k_cubChatMetadataMax](https://partner.steamgames.com/doc/api/ISteamFriends#k_cubChatMetadataMax).|

  
Sets per-user metadata for the local user.  
  
Each user in the lobby will be receive notification of the lobby data change via a [LobbyDataUpdate_t](https://partner.steamgames.com/doc/api/ISteamMatchmaking#LobbyDataUpdate_t) callback, and any new users joining will receive any existing data.  
  
There is a slight delay before sending the data so you can call this repeatedly to set all the data you need to and it will automatically be batched up and sent after the last sequential call.  
  
**Returns:** void  
Triggers a [LobbyDataUpdate_t](https://partner.steamgames.com/doc/api/ISteamMatchmaking#LobbyDataUpdate_t) callback.  
  
  
**See Also:** [GetLobbyMemberData](https://partner.steamgames.com/doc/api/ISteamMatchmaking#GetLobbyMemberData)
## GetLobbyMemberData

const char * GetLobbyMemberData( CSteamID steamIDLobby, CSteamID steamIDUser, const char *pchKey );

|Name|Type|Description|
|---|---|---|
|**steamIDLobby**|[CSteamID](https://partner.steamgames.com/doc/api/steam_api#CSteamID)|The Steam ID of the lobby that the other player is in.|
|**steamIDUser**|[CSteamID](https://partner.steamgames.com/doc/api/steam_api#CSteamID)|The Steam ID of the player to get the metadata from.|
|**pchKey**|const char *|The key to get the value of.|

  
Gets per-user metadata from another player in the specified lobby.  
  
This can only be queried from members in lobbies that you are currently in.  
  
**Returns:** const char *  
Returns **NULL** if `steamIDLobby` is invalid, or `steamIDUser` is not in the lobby.  
  
Returns an empty string ("") if `pchKey` is not set for the player.  
  
**See Also:** [SetLobbyMemberData](https://partner.steamgames.com/doc/api/ISteamMatchmaking#SetLobbyMemberData)

## DeleteLobbyData

bool DeleteLobbyData( CSteamID steamIDLobby, const char *pchKey );

|Name|Type|Description|
|---|---|---|
|**steamIDLobby**|[CSteamID](https://partner.steamgames.com/doc/api/steam_api#CSteamID)|The Steam ID of the lobby to delete the metadata for.|
|**pchKey**|const char *|The key to delete the data for.|

  
Removes a metadata key from the lobby.  
  
This can only be done by the owner of the lobby.  
  
This will only send the data if the key existed. There is a slight delay before sending the data so you can call this repeatedly to set all the data you need to and it will automatically be batched up and sent after the last sequential call.  
  
**Returns:** bool  
**true** if the key/value was successfully deleted; otherwise, **false** if `steamIDLobby` or `pchKey` are invalid.

# Get lobby member names

##
## GetNumLobbyMembers

int GetNumLobbyMembers( CSteamID steamIDLobby );

|Name|Type|Description|
|---|---|---|
|**steamIDLobby**|[CSteamID](https://partner.steamgames.com/doc/api/steam_api#CSteamID)|The Steam ID of the lobby to get the number of members of.|

  
Gets the number of users in a lobby.  
  
**NOTE:** The current user must be in the lobby to retrieve the Steam IDs of other users in that lobby.  
  
This is used for iteration, after calling this then [GetLobbyMemberByIndex](https://partner.steamgames.com/doc/api/ISteamMatchmaking#GetLobbyMemberByIndex) can be used to get the Steam ID of each person in the lobby. Persona information for other lobby members (name, avatar, etc.) is automatically received and accessible via the [ISteamFriends](https://partner.steamgames.com/doc/api/ISteamFriends) interface.  
  
**Returns:** int  
The number of members in the lobby, **0** if the current user has no data from the lobby.
# Chat
## SendLobbyChatMsg

bool SendLobbyChatMsg( CSteamID steamIDLobby, const void *pvMsgBody, int cubMsgBody );

|Name|Type|Description|
|---|---|---|
|**steamIDLobby**|[CSteamID](https://partner.steamgames.com/doc/api/steam_api#CSteamID)|The Steam ID of the lobby to send the chat message to.|
|**pvMsgBody**|const void *|This can be text or binary data, up to 4 Kilobytes in size.|
|**cubMsgBody**|int|The size in bytes of `pvMsgBody`, if it's a text message then this should be strlen( text ) + 1 to include the null terminator.|

  
Broadcasts a chat (text or binary data) message to the all of the users in the lobby.  
  
All users in the lobby (including the local user) will receive a [LobbyChatMsg_t](https://partner.steamgames.com/doc/api/ISteamMatchmaking#LobbyChatMsg_t) callback with the message.  
  
If you're sending binary data, you should prefix a header to the message so that you know to treat it as your custom data rather than a plain old text message.  
  
For communication that needs to be arbitrated (for example having a user pick from a set of characters, and making sure only one user has picked a character), you can use the lobby owner as the decision maker. [GetLobbyOwner](https://partner.steamgames.com/doc/api/ISteamMatchmaking#GetLobbyOwner) returns the current lobby owner. There is guaranteed to always be one and only one lobby member who is the owner. So for the choose-a-character scenario, the user who is picking a character would send the binary message 'I want to be Zoe', the lobby owner would see that message, see if it was OK, and broadcast the appropriate result (user X is Zoe).  
  
These messages are sent via the Steam back-end, and so the bandwidth available is limited. For higher-volume traffic like voice or game data, you'll want to use the [Steam Networking API](https://partner.steamgames.com/doc/features/multiplayer/networking).  
  
**Returns:** bool  
Triggers a [LobbyChatMsg_t](https://partner.steamgames.com/doc/api/ISteamMatchmaking#LobbyChatMsg_t) callback.  
**true** if the message was successfully sent. **false** if the message is too small or too large, or no connection to Steam could be made.