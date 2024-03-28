Penetration testing can lead us in many different directions and while we should definitely follow up on the many different paths based on our interactions, we should stick to our schedule/plan most of the time to keep a disciplined approach.

#### NetSessionEnum
According to the documentation for NetSessionEnum,3:1 there are five possible query levels: 0,1,2,10,502. Level 0 only returns the name of the computer establishing the session. Levels 1 and 2 return more information but require administrative privileges.

This leaves us with Levels 10 and 502. Both should return information such as the name of the computer and name of the user establishing the connection. By default, PowerView uses query level 10 with NetSessionEnum, which should give us the information we are interested in.

The permissions required to enumerate sessions with _NetSessionEnum_ are defined in the **SrvsvcSessionInfo** registry key, which is located in the **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanServer\DefaultSecurity** hive.