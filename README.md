# steamtinkerlaunch

steamtinkerlaunch or short stl - is a linux wrapper bashscript for steam.
With its help every proton game creates/reads a config file with its own custom launch settings.

# features:

* define any env variable you want for every single game, f.e PROTON* , DXVK* variables, MANGOHUD, RADV_PERFTEST, WINE* and so on* custom program launch before or instead the game itself
* custom program launch before or instead the game itself
* winetricks before game launch
* gamemoderun
* notifier
* strace
* optionally start editor with game-specific config before game launch via keypress

TODO:
* it doesn't seem to work to launch a windows exe as custom command via proton...
* more

the idea is that you just have to edit the steam command line once (imho the editor is not very usable)
and everything else can be changed easily using stl

# installation: 
this is one single bash script. just copy it wherever you want, f.e. /usr/local/bin/

just call the script without any arguments
to initially create a default config template
"$HOME/.stl/default_template.conf"
and a default global config
"$HOME/.stl/global.conf"


you might want to change the default configs to your needs before use!

requirements:
--------------
the script itself doesn't have any special dependencies 
(maybe "hexdump" from util-linux, if implementing windows exe as custom command succeeds)

for the optional features you need:
- strace
- zenity (optional, $STEAM_ZENITY is used)
- gamemoderun
- mangohud
- winetricks

usage: add this program to your steam game command line like 'stl %command%'
-----------------------------------

stl will check if a config file exists in "$HOME/.stl/$SteamAppId.conf" for the game and will source it if available
if it is not available it is created from the default config file (which is automatically created if not found)

most options shipped with the (autowritten) default config are commented out.
described are only the variables which come from stl, for all others please check their upstream project:

global settings:

* JUSTWRITECFG=1 							# set to 1 to exit after writing the default config for the selected game without starting the game - useful for quickly creating a configuration
* CREATESTLDXVKCFGTMPL						# create an empty $STLDXVKCFG_tmpl for easier editing when required
* STRACEDIR=/tmp/ 							# the base strace path used to optionally dump strace logs
* STLLOG=/tmp/$(basename "$0").log			# the stl logfile
* WRITELOG=1								# write logfile if enabled
* STLEDITOR=geany							# the texteditor to use for opening the gameconfig - f.e. with WAITEDITOR
* WAITEDITOR=2								# wait WAITEDITOR seconds for a keypress to open the texteditor STLEDITOR with the game config

if you do not want to start the editor requester on game launch just set WAITEDITOR=0 - it will be skipped then

#################

# loose function description:

custom game launch:
---------------------
when enabled you can also start custom programs easily with the following per-game config options:

set to 1 to enable the custom command STL_CMD:
* RUN_STL_CMD=1

start this custom command:
* STL_CMD=yourProgram

start STL_CMD command with following args:
* STL_CMD_ARGS=--some --args

set to 1 to only start above STL_CMD and not the game command itself:
* ONLY_STL_CMD=0

set to 1 to fork the custom STL_CMD into the background and continue with starting %command%
* FORK_STL_CMD=1

winetricks:
------------
* RUN_WINETRICKS=1							# set to 1 to start winetricks gui before game launch

gamemoderun:
-------------
* USEGAMEMODERUN=1							# set to 1 to start game with gamemoderun

notifier:
-----------
* NOTY=notify-send							# the notifier used

mangohud:
----------

* MANGOHUD=1								# set to 1 to enable mangohud - does nothing in stl itself, but just exports the upstream variable

radv aco:
----------
* #RADV_PERFTEST=aco						# this is mesa upstream default so will probably disappear in the default config here

strace:
----------
* STRACERUN=0 								# if set to one stl will write a strace log of the launched game
* STRACEOPTS=-f -t -e trace=file			# the strace options used for strace

when STRACERUN is enabled make sure
/proc/sys/kernel/yama/ptrace_scope is set to 0
else your user will get access denied when trying to attach a process
either "echo 0 > /proc/sys/kernel/yama/ptrace_scope" as root or enable it persistent in sysctl

