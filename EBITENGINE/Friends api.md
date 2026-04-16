


## GetFriendPersonaName

const char * GetFriendPersonaName( CSteamID steamIDFriend );

| Name              | Type                                                                  | Description                     |
| ----------------- | --------------------------------------------------------------------- | ------------------------------- |
| **steamIDFriend** | [CSteamID](https://partner.steamgames.com/doc/api/steam_api#CSteamID) | The Steam ID of the other user. |

  
Gets the specified user's persona (display) name.  
  
This will only be known to the current user if the other user is in their friends list, on the same game server, in a chat room or lobby, or in a small Steam group with the local user.  
  
**NOTE:** Upon on first joining a lobby, chat room, or game server the current user will not known the name of the other users automatically; that information will arrive asynchronously via [PersonaStateChange_t](https://partner.steamgames.com/doc/api/ISteamFriends#PersonaStateChange_t) callbacks.  
  
To get the persona name of the current user use [GetPersonaName](https://partner.steamgames.com/doc/api/ISteamFriends#GetPersonaName).  
  
**Returns:** const char *  
The current user's persona name in UTF-8 format. Guaranteed to not be **NULL**.  
  
Returns an empty string (""), or "[unknown]" if the Steam ID is invalid or not known to the caller.




# MESSAGING
## GetFriendMessage

int GetFriendMessage( CSteamID steamIDFriend, int iMessageID, void *pvData, int cubData, EChatEntryType *peChatEntryType );

|Name|Type|Description|
|---|---|---|
|**steamIDFriend**|[CSteamID](https://partner.steamgames.com/doc/api/steam_api#CSteamID)|The Steam ID of the friend that sent this message.|
|**iMessageID**|int|The index of the message. This should be the `m_iMessageID` field of [GameConnectedFriendChatMsg_t](https://partner.steamgames.com/doc/api/ISteamFriends#GameConnectedFriendChatMsg_t).|
|**pvData**|void *|The buffer where the chat message will be copied into.|
|**cubData**|int|The size of `pvData`.|
|**peChatEntryType**|[EChatEntryType](https://partner.steamgames.com/doc/api/steam_api#EChatEntryType) *|Returns the type of chat entry that was received.|

  
Gets the data from a Steam friends message.  
  
This should only ever be called in response to a [GameConnectedFriendChatMsg_t](https://partner.steamgames.com/doc/api/ISteamFriends#GameConnectedFriendChatMsg_t) callback.  
  
**Returns:** int  
The number of bytes copied into `pvData`.  
Returns **0** and sets `peChatEntryType` to [k_EChatEntryTypeInvalid](https://partner.steamgames.com/doc/api/steam_api#k_EChatEntryTypeInvalid) if the current user is chat restricted, if the provided Steam ID is not a friend, or if the index provided in `iMessageID` is invalid.




## GetFriendCountFromSource

int GetFriendCountFromSource( CSteamID steamIDSource );

| Name              | Type                                                                  | Description                                                                |
| ----------------- | --------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| **steamIDSource** | [CSteamID](https://partner.steamgames.com/doc/api/steam_api#CSteamID) | The Steam group, chat room, lobby or game server to get the user count of. |

  
Get the number of users in a source (Steam group, chat room, lobby, or game server).  
  
**NOTE:** Large Steam groups cannot be iterated by the local user.  
  
**NOTE:** If you're getting the number of lobby members then you should use [ISteamMatchmaking::GetNumLobbyMembers](https://partner.steamgames.com/doc/api/ISteamMatchmaking#GetNumLobbyMembers) instead.  
  
This is used for iteration, after calling this then [GetFriendFromSourceByIndex](https://partner.steamgames.com/doc/api/ISteamFriends#GetFriendFromSourceByIndex) can be used to get the Steam ID of each person in the source.  
  
**Returns:** int  
**0** if the Steam ID provided is invalid or if the local user doesn't have the data available.


## GetFriendFromSourceByIndex

CSteamID GetFriendFromSourceByIndex( CSteamID steamIDSource, int iFriend );

|Name|Type|Description|
|---|---|---|
|**steamIDSource**|[CSteamID](https://partner.steamgames.com/doc/api/steam_api#CSteamID)|This MUST be the same source used in the previous call to [GetFriendCountFromSource](https://partner.steamgames.com/doc/api/ISteamFriends#GetFriendCountFromSource)!|
|**iFriend**|int|An index between 0 and [GetFriendCountFromSource](https://partner.steamgames.com/doc/api/ISteamFriends#GetFriendCountFromSource).|

  
Gets the Steam ID at the given index from a source (Steam group, chat room, lobby, or game server).  
  
**NOTE:** You must call [GetFriendCountFromSource](https://partner.steamgames.com/doc/api/ISteamFriends#GetFriendCountFromSource) before calling this.  
  
**Returns:** [CSteamID](https://partner.steamgames.com/doc/api/steam_api#CSteamID)  
Invalid indices return [k_steamIDNil](https://partner.steamgames.com/doc/api/steam_api#k_steamIDNil).
