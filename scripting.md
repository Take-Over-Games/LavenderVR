
# Scripting

Currently all scripting is done in lua, through text assets inside of your project.
multiple includes is allowed, there will be a gamemode structure to work from.

[Code Reference](#)

[Uploading Content](./uploading-content.md)

Temp export of the api, will be updated more soon

````LuaAPI
Core lua api that can be accessed under the global table called "api"
Exported Type:LuaAPI
Void Print ( message  )
Logs a message to the lua console, also has a short hand to just print()
Example:

GameObjectRef CreateGameObject ( name  )
Creates a new gameobject on the server and returns a gameobject refrence to it
Example:

Void BindEvent ( name function  )
Bind a Lavender event to a lua function
Example:

Void ClearEvent ( name  )
Remove all bound lua functions from the named event
Example:

Void RPCLuaFunction ( PlayerID functionName data  )

Example:

Void CommandLuaFunction ( functionName data  )

Example:

EntityRef
Safe handle to a Unity gameobject returned by various api methods
Exported Type:GameObjectRef
DynValue GetName (  )
Gets the name of this gameobject
Example:

Void SetName ( name  )
Sets the name of this gameobject
Example:

Void SetPos ( x y z  )
Sets the world position of this gameobject
Example:

DynValue GetPos (  )
Gets the world position of this gameobject
Example:

Void Enable (  )
Enables this object to be processed
Example:

Void Disable (  )
Disables this object, lua scripts and all unity components on it wont be processed
Example:

ComponentRef GetComponent ( name  )
Get a component on this gameobject if it exists
Example:

ComponentRef
Safe handle to a Component on a gameobject returned by various api methods
Exported Type:ComponentRef
````