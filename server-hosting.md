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