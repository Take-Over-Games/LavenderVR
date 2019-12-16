Run lavender's main game client on a server with one of several command line options to host a dedicated server.  
You can install the game and update it through steamcmd using appid of `886710`:

````
app_install 886710
app_update 886710
````

Starting a server should be easy. Please refer to [command-line flags](./command-line-flags.md) for more information on what these args do.
````
lavender.exe -server -world "d737e06c-39ac-4442-9ffd-1bec7cadb1d7" -nosteam -batchmode -nographics -user "email" -password "password" -logfile "log.txt"
````

**Note:** `world` argument points to the world GUID. If you'd like to start your server with a different world, replace "d737e06c-39ac-4442-9ffd-1bec7cadb1d7" with a desired world GUID.

In addition to the command-line arguments, one would have to set up a `Server` section in the settings.ini. Refer to the following snippet for example set up:

````ini
[Server]
APIPort = 10000
Port = 10000
MaxPlayers = 1024
Tick = 60
Public = true
ServerName = NotDefault
PropLimitPerPlayer = 100
CleanupPropsOnDisconnect = true````

Check out the [Linux Hosting guide](./Linux-Hosting.md).