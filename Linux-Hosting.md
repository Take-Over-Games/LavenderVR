# Linux Server Hosting Guide
**Note:** Current linux builds are behind the windows builds, so this guide will not currently function.

### OS Requirements:
* Linux distro (Recommended ubuntu server)
* sudo / root on your server

### Recommended minimum specs:
In general, the higher your target amount of people, the more resources you'll have to dedicate. These are the minimum specs.

* 2GB+ RAM
* Decent x86_64 CPU - i5-2400 / FX-6100 and above
* At least 2 cores

----

## Installation
* [Installing SteamCMD](#installing-steamcmd)
* [Installing Lavender Server](#installing-iavender-server)

### Installing SteamCMD
**Note for newbies:** the `&&` just chains commands together with a "If preceding command runs successfully, run the following command".  
Lavender's files are managed through Steam. Assuming `apt` is your package manager, we'll create a service user for your lavender server, and install prereqs:

````bash
sudo apt update && sudo apt install -y gdb curl lib32gcc1 && sudo useradd -m lavender
````

Now switch to the service user to install steam and lavender itself. This switches the current user and opens up bash in their context.  
You will likely have to do this every time you want to do something with the server:

````bash
sudo su -l lavender -s /bin/bash
````

Install steam. Optionally change the installation directory to suit your needs:

````bash
mkdir ~/Steam && cd ~/Steam && curl -sqL "https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz" | tar zxvf -
````

Lavender server is not a seperate free instance so you'll need to login to steam via the CLI so that steam will download the game. Run the following command to just login for the first time. Steam will save your credentials... somewhere.

````bash
~/Steam/steamcmd.sh +login <yourusername>
````

You'll be prompted for a password. Steam will cache your credentials so you can automate server tasks.

### Installing Lavender Server

````bash
~/Steam/steamcmd.sh +login <yourusername> +force_install_dir ~/lavender +app_update 886710 validate +quit
````

To install the nightly beta:

````bash
~/Steam/steamcmd.sh +login <yourusername> +force_install_dir ~/lavender +app_update 886710 beta nightly validate +quit
````

To remove it and install a new version, just delete the lavender folder and install the version you want. EG to delete entire install: `rm -rf ~/lavender`.

## Initial Configuration

Config file can be found at the following location: `~/.config/unity3d/Take Over Games/Lavender/settings.ini`. Don't forget to escape spaces if you're copying the string :-)

Running the server requires your Lavender platform credentials (the ones you use to login to lavender). There's no good way to do this, but the least bad is to save the username and pass to file, make it lavender-read only, then pull it out. Elsewise your email & password will be in the process list and visible to all users on your server. Optionally, use environment variables.

Unless you have already changed it, your server will run in private mode, not being displayed in public the server list.

Save your lavender password to file with the following command. The username will be an email address.

````bash
printf <password> > ~/.lavender && chmod 400 ~/.lavender
````

**Security Note:** setting permission mask of 400 switches the file into read-only mode, only available to your current user. Leaving file exposed with 664/665/666 means **anyone** using your system can read your credentials, therefore running the above command is highly recommended.

## Running the server
Login as your lavender service user:

````bash
sudo su lavender -s /bin/bash
````

Running the lavender server itself:
````bash
~/lavender/Lavender -server -nosound -world <worldid> -nosteam -batchmode -nographics -user <lavenderusername> -password $(cat /home/lavender/.lavender)
````

That will run the executable in your session. However, if you lose or close the connection, the server process will be terminated. Get the above working, get stuff configured, then use more production-oriendet methods of running the process in background. For example, the binary should usually be launched in a new host process, either with & or with screen/tmux (see administration section below).


## Advanced Configuration
By default your server won't appear on the galaxy list. If you want to change this, add the following section to your config file: 

````ini
[Server]
Servername = <the server name you want>
Public = true
````

Other options include:

````ini
PropLimitPerPlayer = 100
CleanupPropsOnDisconnect = true
APIPort = 10000
Port = 10000
MaxPlayers = 1024
Tick = 60
````
**Note:** leaving `CleanupPropsOnDisconnect = True` is highly recommended because players cannot really at the time of writing anyway, and it is not clear wether reconnected players are able to clear own previously spawned items.

Start the executable under `screen` so it runs in the background:

````bash
screen -S lavenderserver -d -m ~/lavender/Lavender -server -nosound -world <worldid> -nosteam -batchmode -nographics -user <lavenderusername>
-password $(cat /home/lavender/.lavender)
````

### Command Line Documentation

* <kbd>-settings <file> </kbd> - Manually pass a config file from `<file>` location to the Lavender instance
* <kbd>-server </kbd> - Run Lavender as a server
* <kbd>-username <username></kbd> - Submit `<username>` to Lavender content API
* <kbd>-password <username> </kbd> - Submit `<password>` to Lavender content API
* <kbd>-world <world GUID> </kbd> - Load a `<world>` on startup
* <kbd>-nosteam </kbd> - Do not use steam integration

More flags available at [command-line flags page](./command-line-flags.md).
 
### Scripts and such

Two things you'll want to be doing when running a server are automatic updates and autorestart. You can get yourself up and running with what is above, but this section will just contain a few scripts that may help you with administering your server. A word of warning, though: these are not pretty, but they'll do the job.

### Run and save logs

This script will add logs and timestamps to the logs. Useful for diagnosing problems. If ran in a screen session, will also restart the server automatically if it can't detect the process. Will update this to be less awful in the future.

````bash
#!/bin/bash

# As this contains credentials, chmod 700 it so other users can't read it!

# full path pls
# Change these to what is correct.
lavenderexe="/home/lavender/lavender/Lavender"
#world="233e09aa-8f31-43fb-9e62-316677c5f837"
world="d737e06c-39ac-4442-9ffd-1bec7cadb1d7" # Maebbie's atoll of ether
user="email@email.com" # Lavender.com credentials
password="hardcodedpassword" # Going to ask june for an alternative to this.
logloc="/home/lavender/logs"
tempfile="~/.lavendertemp"
lavpid=""
trap ctrl_c INT

function ctrl_c() {
        echo $lavpid
        kill $lavpid

        exit 0
}

mkdir "$logloc"
while [ 1 = 1 ]; do
        updatestatus=$(cat "$tempfile")
        if ps aux | grep -v grep | grep -q "$lavenderexe"; then
                # Lavender Running! Don't mess with it!
                echo "Lavender running"
        elif [ "$updatestatus" = "updating" ]; then
                # Lavender updating, also don't mess with it
                echo "Lavender updating, pls not do things"
        else
                echo "Lavender not running, panic!"
                # Lavender not running, starting
                cd ${lavenderexe%/*}
                echo "$lavenderexe" -server -nosound -world $world -nosteam -batchmode -nographics -user $user -password $password
                "$lavenderexe" -server -nosound -world $world -nosteam -batchmode -nographics -user $user -password $password | ts '[%Y-%m-%d %H:%M:%S]' | tee -a "${logloc}$(date +%d-%M-%Y_%H-%M)_lavserver.log" &
                lavpid=$(echo $! | grep -o "[0-9]*")
        fi
        sleep 120s
done
````

### Update Script

The following monstrosity checks if there's an update, then updates if required. In lieu of an actual 'current version' API this downloads a second copy of the game, checks the version file, then deletes/copies as neccessary. It's terrible and if you can get anything better working then please let us know. I run mine every 6 hours, will probably change to every hour.


````bash
#!/bin/bash

# Usage: ./update_check.sh "steam username" "lavender installation dir"

# This script is a hack to check if there's an update available...
# and then updates if neccessary. ATM it deletes your server files so don't store shit in there.
# This script places a text file containing "updating" to tempfile that can be checked by autorestart scripts so it doesn't try and restart it while updating.

# Fix this if anything is different
timestamp=$(date +%s)
tmpinst="/tmp/${timestamp}"
steamusername="$1"
steamcmd="/home/lavender/Steam/steamcmd.sh"
branch="beta nightly validate" #Non beta would just be "validate"
real_install="$2"
lavenderexe="Lavender"
tempfile="/home/lavender/.lavendertemp"

if [ "$#" -lt 2 ]; then
        echo 'Usage: ./update_check.sh "steam username" "real install dir"'
        exit 1
fi

if test -f "${real_install}/version"; then
        echo "Found the version file"
else
        echo "Did not find the version file at ${real_install}"
        exit 1
fi


"$steamcmd" +login "$steamusername" +force_install_dir "$tmpinst" +app_update 886710 $branch +quit
if test -f "${tmpinst}/version"; then
        echo "New version downloaded"
else
        echo "Temp copy download FAILED"
        exit 1
fi

version=$(cat ${tmpinst}/version)

realversion=$(cat "${real_install}/version")
if [ "$realversion" = "$version" ]; then
        # No updates needed.
        echo "No updates needed, $realversion matches $version"
        rm -rf "$tmpinst"

else
        echo "Update needed!"
        echo "updating" > "$tempfile"
        pkill $lavenderexe
        sleep 20s # Should be a sleep loop where it checks for the Lavender process being ded
        if test -f "${tmpinst}/version"; then
                echo "Found the version file"
        else
                echo "Did not find the version file at ${tmpinst}"
                exit 1
        fi

        rm -rf "${real_install}"
        cp -r "$tmpinst" "$real_install"
        rm "$tempfile"
        rm -rf "$tmpinst"
fi
# Your responsibility to restart it
````
