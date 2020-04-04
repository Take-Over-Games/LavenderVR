In some rare cases, you might want to use developer console to issue some commands to either your local client or a remote server. This page lists all valid console commands and explains their application.

# Console commands

- `stats`  
  *Admin-only*  
  Lists all the players bandwidth stats since the server was started (including disconnected ones) for total sumation of data in/out. Also displays total entity count and prop count.

- `players` or `playerlist`  
  *Admin-only*  
  Lists all current players. Adds `[Admin]` next to admin users' names.

- `quit` or `qqq`  
  Terminates the application.

- `login` (`login <email> <password>`)  
  Alternative way to login, mainly for developer use.

- `clear` or `cls`  
  Clears the console.

- `world` or `changelevel` / `world <guid>` or `changelevel <guid>`  
  *Admin-only*
  Issue a world change to a server. `<guid>` being the only argument, marks the target world/map by its ID. While an admin can change the map by clicking on a world in content list, one can change the map to any private world by supplying its GUID.

- `avatar` / `avatar <guid>`  
  Request an avatar change. The only argument is an avatar GUID.  
  **NOTE:** Currently avatar ownership is not checked, this will change in a future update.

- `kick` / `kick <playerId>`  
  *Admin-only*  
  Issue a kick by supplying an integer/player ID found in the output of `players`/`playerslist` command.  
  *Example: `kick 2` kicks a player associated with number 2 in `players` output.*

- `set` / `set section key=value`
  *Admin-only*  
  Changes a variable identified by `key` argument under a specified `section` section of your .ini file to a value given using the `value` argument. Also writes the changes to the configuration file.  
  *Example: `set Server PropLimitPerPlayer=10` will change `$.Server.PropLimitPerPlayer` to 10.*

- `cleanup`  
  *Admin-only*  
  Removes *all spawned props* from the server.

- `connect` / `connect <ip>`  
  Connects to a specified IP.  
  *Example: `connect 192.168.1.5`

- `version` / `version <version>`  
  Allows changing your internal version id for joining servers. This is a developer feature for version mismatch bypass if the internal network structure hasn't changed too much.

- `vr`  
  Switch to vr, same as Pressing F9

- `desktop`  
  Switch to desktop, same as Pressing F9