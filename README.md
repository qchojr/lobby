# lobby
- Script repo for lobby and bot setup

## Run Hosted Bots
Anyone who has the ability to host a game in the tripleA 1.9 Lobby can also host one or more bot's in the lobby for others to use. Here is how.




## Linux Bot install

Insructions below assume you are root on a very brand new linode machine (Ubuntu 16.04):
```
wget https://raw.githubusercontent.com/triplea-game/lobby/master/setup_bot
./setup_bot latest DE 40 5
```

## Running bots
Start/stop scripts are in `/home/triplea`
For reference, commands that can be run are in: `/home/triplea/README.txt`



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


To update maps, simply run:
```
cd /home/triplea/maps; ./download_all_maps
```


## Lobby Install

It is cloned on the triplea account on the server @ /home/triplea/lobby-scripts

To install, from the server with an account that has 'sudo -u triplea' access, run:
```
@tripleawarclub:/home/triplea/lobby-scripts$ git pull --rebase
@tripleawarclub:/home/triplea/lobby-scripts$ sudo -u triplea ./install_lobby 1.9.0.0.3096 ../lobby_1_9/
@tripleawarclub:/home/triplea/lobby-scripts$ sudo -u triplea ./install_bot 1.9.0.0.3096 ../bots_1_9/
@tripleawarclub:/home/triplea/lobby-scripts$ cd ../lobby_1.9.0.0.3096/
@tripleawarclub:/home/triplea/lobby_1_9/lobby_1.9.0.0.3096$ ps -ef | grep 3304
triplea   8095  8093  0 Sep05 ?        00:02:15 java -server -Xmx256m -classpath bin/triplea.jar:lib/derby-10.10.1.1.jar -Dtriplea.lobby.port=3304 games.strategy.engine.lobby.server.LobbyServer
@tripleawarclub:/home/triplea/lobby_1_9/lobby_1.9.0.0.3096$ sudo kill 8095
@tripleawarclub:/home/triplea/lobby_1_9/lobby_1.9.0.0.3096$ sudo -u triplea cp -r ../lobby_1.9.0.0.3074/derby_db/ ./
@tripleawarclub:/home/triplea/lobby_1_9/lobby_1.9.0.0.3096$ sudo chmod +x run_lobby
@tripleawarclub:/home/triplea/lobby_1_9/lobby_1.9.0.0.3096$ sudo -u triplea nohup ./run_lobby &
```

If you are running multiple lobby servers at the same time, you will need to edit the `run_lobby` file and change the port number (if this becomes common, we'll want to include it is a param).  Also assumed is that the port is enabled in ufw.


Download all maps for a robot, and run lobby bots:
```
@tripleawarclub:/home/triplea/lobby-scripts$ sudo -u triplea mkdir maps
@tripleawarclub:/home/triplea/lobby-scripts$ cd maps
## downloads all maps to the current folder
@tripleawarclub:/home/triplea/lobby-scripts/maps$ sudo -u triplea ../download_all_maps
@tripleawarclub:/home/triplea/lobby-scripts/maps$ cd ../
@tripleawarclub:/home/triplea/lobby-scripts$ sudo rm -rf ../bots_1_9/maps/
@tripleawarclub:/home/triplea/lobby-scripts$ sudo -u triplea mv maps/ ../bots_1_9/

## now start bots
@tripleawarclub:/home/triplea/lobby-scripts$ cd ../bots_1_9/bot_1.9.0.0.3096/

## launches a bot on port 4001, numbered: 1
@tripleawarclub:/home/triplea/bots_1_9/bot_1.9.0.0.3096$ sudo -u triplea nohup ./run_bot 4001 1 &

## view the logs after launching our bot, check for map errors or startup errors
@tripleawarclub:/home/triplea/bots_1_9/bot_1.9.0.0.3096$ tail -f logs/headless-game-server-Bot1_WarClub-log0.txt
```

If you run the bots and the bots say there are errors parsing any maps, then we need to stop and quit the bot, then go delete those maps.  

### Lobby DB Setup


## Install DB - steps from: https://help.ubuntu.com/community/PostgreSQL

```
sudo apt-get install postgresql postgresql-contrib
sudo -u postgres psql postgres

## Set password
\password postgres

## Set up DB
export PGPASSWORD= $( grep password ../lobby.properties | sed 's/.*=//') 
echo "create database ta_users" | psql -h localhost -U postgres

wget https://raw.githubusercontent.com/triplea-game/triplea/master/config/lobby/db/001_create_tables
```

## Add a linode server

### Select 'add a linode' link:
![add_a_linode](https://user-images.githubusercontent.com/12397753/28345142-0ac9347a-6bdd-11e7-84cf-b46028362afa.png)

### Choose the smallest one
We mainly need a running machine with RAM:
![choose_cheap](https://user-images.githubusercontent.com/12397753/28344981-f65df044-6bdb-11e7-88e2-98914c388459.png)

### Choose deploy an image
![deploy_an_image](https://user-images.githubusercontent.com/12397753/28344978-f64d41b8-6bdb-11e7-9f80-9ca93bdab10a.png)

### Deploy ubuntu image
Use defaults, set password, record it in the secret admin password registry:
![deploy_ubuntu](https://user-images.githubusercontent.com/12397753/28344980-f65d685e-6bdb-11e7-8740-b47a26784a27.png)

### Rename the server instance
![rename_it](https://user-images.githubusercontent.com/12397753/28344979-f64e37f8-6bdb-11e7-8161-cdd0c1628a8e.png)

### Once it is imaged, click boot button:
![boot_it_up](https://user-images.githubusercontent.com/12397753/28344977-f6491a34-6bdb-11e7-9b4e-e0545a9469fb.png)

Now you can log in as root, and run the bot setup script. 
TODO: set up the 'sudo' admin users
