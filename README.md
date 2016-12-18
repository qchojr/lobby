# lobby
- Things relating to the tripleA lobby
- Contains any scripts that are used on the server

## Deploying
- Likely to be an HTTPS git clone, ideally per user, but perhaps under the shared account
 
## Run Warclub Bots
To set up a bot:
- log into the server, (2961 is an example version, that may be changed) and run:

```
git clone https://github.com/triplea-game/lobby.git
cd lobby
./install_bot 2961 ~/bots_1_9/
```

That will then install the bot distribution to the `~/bots_1_9/` folder, and a run script. Now change user and launch the bot:
```
cd /home/triplea
su triplea
### type in triplea user password
cd /home/triplea/bots_1_9/bot_1.9.0.0.2961/
```

If not running on teh warclub server, edit the run_bot file and update the bot name (update it in both places..), otherwise now run:
```
nohup ./run_bot 4001 1 &
```

4001 is the port to run on, ensure that port has been opened in firewall with:
```
sudo ufw allow 4001
```

## Run Hosted Bots
Anyone who has the ability to host a game in the tripleA 1.9 Lobby can also host one or more bot's in the lobby for others to use. Here is how.

### Windows
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

### Mac + Linux

Script to launch bot host is at: https://github.com/triplea-game/lobby/blob/master/run_bot
First `cd` to the TripleA install directory.

Clone this repo (https://github.com/triplea-game/lobby) or wget the above script:
`wget https://raw.githubusercontent.com/triplea-game/lobby/master/run_bot`

Update the run_bot script as appropriate. Take care to note where the map folder is, and update that parameter in the script file. 

### Bot Maintenance

Maps need to be updated periodically to stay with the latest. The following script is meant to help and will download all maps: https://github.com/triplea-game/lobby/blob/master/download_all_maps  (future work: add configuration here for how to set this script up in a cronjob, and then copy-in and replace the old maps automatically)

