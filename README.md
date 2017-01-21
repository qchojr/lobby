# lobby
- Script repo for lobby and bot setup

## Run Hosted Bots
Anyone who has the ability to host a game in the tripleA 1.9 Lobby can also host one or more bot's in the lobby for others to use. Here is how.


## Linux Bot install

Insructions below assume you are root on a very brand new linode machine (Ubuntu 16.04):
```
sudo apt-get install -y git
git clone https://github.com/triplea-game/lobby.git
cd lobby

./setup_new_server <new_user_name>
./install_all <latest_version_number, eg: 1.9.0.0.5500>
cd /home/triplea/bots/bot_<latest_version_number>

# next, edit run_bot and make sure settings look
vi run_bot 

./run_bot_number 01
./run_bot_number 02
./run_bot_number 03
```

The above will create a `triplea` user account, and another one of your choosing.
Install all will download maps and bot binary files. It will then move it all
to `/home/triplea/bots/`


### Hosting Bots on Windows

Create this text file on your computer for each bot you want to create and save it with the extension .bat
For each bot batch file you create, change the SET PORT= and SET BOT_NUMBER= to a new port number and name. This file must be saved to the triplea game folder on your pc.

You will also need to have the Java SE Development Kit 8u111 installed on your pc for the java command to execute. This is an easy download at http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html

Batch File Commands Below
```
@echo off
SET PORT=3350
SET BOT_NUMBER=01

SET LOBBY_HOST=45.79.144.53
SET LOBBY_PORT=3304

java -server -Xmx320m -Djava.awt.headless=true -classpath bin/triplea.jar games.strategy.engine.framework.headlessGameServer.HeadlessGameServer triplea.game.host.console=false triplea.game= triplea.server=true triplea.port=%PORT% triplea.lobby.host=%LOBBY_HOST% triplea.lobby.port=%LOBBY_PORT% triplea.name=Bot%BOT_NUMBER%_WarClub triplea.lobby.game.hostedBy=Bot%BOT_NUMBER%_WarClub triplea.lobby.game.supportEmail=youremail@hotmail.com triplea.lobby.game.comments="automated_host" triplea.lobby.game.reconnection=172800 mapFolder=../maps

pause
```

### Bot Maintenance

Maps need to be updated periodically to stay with the latest. The following script is meant to help and will download all maps: https://github.com/triplea-game/lobby/blob/master/download_all_maps  (future work: add configuration here for how to set this script up in a cronjob, and then copy-in and replace the old maps automatically)

