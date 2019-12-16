== Linux Server ==

** Note: Current linux builds are behind the windows builds, so this guide will not currently function. **

Required:

* Linux server (Recommended ubuntu server)
* sudo / root on your server

Recommended minimum specs:

* 2GB+ RAM
* Decent x86_64 CPU - i5-2400 / FX-6100 and above
* At least 2 cores

You'll need more for more people.

=== Installation ===
For newbies; the && just chains commands together with a "If command before runs okay, run command after"
Lavender is managed through Steam. Assuming apt is your package manager.
We'll create a service user for your lavender server, and install prereqs:

<code>sudo apt update && sudo apt install -y gdb curl lib32gcc1 && sudo useradd -m lavender</code>

Switch to the service user to install steam and lavender itself. this switches user and opens up bash.
You will likely have to do this every time you want to do something with the server.

<code> sudo su -l lavender -s /bin/bash </code>

Install steam. You can change the directories but there isn't much point.

<code>mkdir ~/Steam && cd ~/Steam && curl -sqL "https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz" | tar zxvf -</code>

Lavender server is not a seperate free instance so you'll need to login to steam via the CLI so that steam will download the game. Run the following to just login for the first time. Steam will save your credentials... somewhere.

<code> ~/Steam/steamcmd.sh +login <yourusername> </code>

You'll be prompted for a password. Steam will cache your credentials so you can automate server tasks.

Installing lavender:

<code>~/Steam/steamcmd.sh +login <yourusername> +force_install_dir ~/lavender +app_update 886710 validate +quit</code>

To install the nightly beta:

<code>~/Steam/steamcmd.sh +login <yourusername> +force_install_dir ~/lavender +app_update 886710 beta nightly validate +quit</code>

To remove it and install a new version, just delete the lavender folder and install the version you want. EG to delete entire install: <code> rm -rf ~/lavender</code>

== Initial Configuration ==

Config file will be found at the following. (Documentation later, same as the windows config)

<code> ~/.config/unity3d/Take Over Games/Lavender/settings.ini </code>

Running the server requires your Lavender website credentials (also what you use to login to lavender). There's no good way to do this, but the least bad is to save the username and pass to file, make it lavender-read only, then pull it out. Elsewise your user/pass will be in the process list and visible to all users on your server.

By default it won't show on the galaxy. Config that later. 

Save your lavender password to file with the following. The username will be an email address.

<code> printf <password> > ~/.lavender && chmod 400 ~/.lavender </code>

'''CAUTION''' - this file is extremely sensitive. Chmod 400 will make it '''read only''' to the lavender (service) user only. Allowing other users to edit or view this is a very bad idea. But it's better than showing your password on the process list.

== Running the server ==

Login as your lavender service user:

<code> sudo su lavender -s /bin/bash </code>

Running the lavender server itself: (For argument documentation; see <argument documentation> )

<code>~/lavender/Lavender -server -nosound -world <worldid> -nosteam -batchmode -nographics -user <lavenderusername> -password $(cat /home/lavender/.lavender) </code>

That will run the executable. If you lose SSH connection or close it then it'll die. Get the above working, get stuff configured, then use more production-ready commands. For example. the binary should usually be launched in a new process, either with & or with screen. (see administration below)


== Advanced Configuration ==

By default your server won't appear on the galaxy. To do this, in the config file, add 

<code> [Server]
Servername = <the server name you want>
Public = true
</code>

Other options include:

<code>
PropLimitPerPlayer = 100
CleanupPropsOnDisconnect = true
APIPort = 10000
Port = 10000
MaxPlayers = 1024
Tick = 60
</code>

(highly suggest leaving <code>CleanupPropsOnDisconnect = True</code> on because players cannot build right now anyway and I dont know if reconnecting players can clear their old props) t. Ram
<link to configuration documentation should go here>

Start the executable with <code>screen</code> so it runs in the background:

<code> screen -S lavenderserver -d -m ~/lavender/Lavender -server -nosound -world <worldid> -nosteam -batchmode -nographics -user <lavenderusername>
-password $(cat /home/lavender/.lavender) </code>

=== Command Line Documentation ===

<code> -settings <file> </code> - Manually pass a config file to the Lavender instance
<code> -server </code> - Run Lavender as a server
<code> -username <username></code> - Submit username to Lavender content API
<code> -password <username> </code> - Submit password to Lavender content API
<code> -world <world GUID> </code> - Load a world on startup
<code> -nosteam </code> - Do not use steam
 
==Scripts and such==

Two things you'll want to be doing when running a server are automatic updates, and autorestart. You can get yourself up and running with what is above, but this section will just contain a few scripts that may help you administer your server. One word of warning is that these are not pretty, but they'll do the job.

=== Run and save logs ===

This script will add logs and timestamps to the logs. Useful for diagnosing problems. If ran in a screen session, will also restart the server automatically if it can't detect the process. Will update this to be less awful in the future.
Lavender does have a -logfile option now but it does not produce timestamps.

<code>
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
</code>

== Update Script ==

This monstrosity checks if there's an update, and then updates if required. In lieu of an actual 'current version' API this downloads a second copy of the game, checks the version file, then deletes/copies as neccessary. It's terrible and if you can get anything better working then please let us know. I run mine every 6 hours, will probably change to every hour.

Note that this installs the '''nightly''' version of lavender. Change the branch as required. 

<code>
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
branch="beta nightly validate" # Non beta would just be "validate"
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
</code>
