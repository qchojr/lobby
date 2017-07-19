# lobby
- Documentationa and install scripts for lobby and bots


## Lobby Install
```
rm -f setup_lobby; wget --no-cache https://raw.githubusercontent.com/triplea-game/lobby/master/setup_lobby; chmod +x setup_lobby
./setup_lobby
```

## Lobby DB Setup

### Install DB - steps from: https://help.ubuntu.com/community/PostgreSQL

```
sudo apt-get install postgresql postgresql-contrib
sudo -u postgres psql postgres

### Set password
\password postgres

### Set up DB
export PGPASSWORD= $( grep password ../lobby.properties | sed 's/.*=//') 
echo "create database ta_users" | psql -h localhost -U postgres

wget https://raw.githubusercontent.com/triplea-game/triplea/master/config/lobby/db/001_create_tables
```

# Bots

## Linux Bot install

As root on a brand new linode machine (Ubuntu 16.04):
```
 rm setup_bot*; wget --no-cache https://raw.githubusercontent.com/triplea-game/lobby/master/setup_bot; chmod +x setup_bot

./setup_bot latest DE 40 4
./setup_bot latest JP 50 4
./setup_bot latest GA_USA 60 4
./setup_bot latest NJ_USA 70 4
./setup_bot latest CA_USA 80 4
./setup_bot latest TX_USA 90 4
```


## Running bots
Log in as triplea user, start/stop scripts are in `/home/triplea`

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

Maps need to be updated periodically, run:
```
cd /home/triplea/maps; ./download_all_maps
```



## Adding Linode servers

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

### Update host name
- Edit `/etc/hostname`, put the hostname in the that file and save
- Update `/etc/hosts`, add the hostname to the first line, like so:
```
127.0.0.1       localhost BotServer_NJ_70
```
- Restart the linode


## More on Bots
```
sudo service triplea-bot@<bot_number> start|stop|status|restart
```
_bot_number_ is used to make multiple bots possible. Use unique bot numbers across all servers to avoid confusion. Bots currently use up a lot of RAM, probably too much, this is why only a limited amount of bots can be run on a single server.

Every bot needs its own opened port. We recommend ufw as an easy-to-use tool for managing firewall rules.
The default port is `400${BOT_NUMBER}`, e.g. bot 10 uses port 40010.

### Easy restarting
If we now want to restart all default bots, we run:
```
cd /home/triplea/; ./restart_all
```
This launches all default bots we defined in our starter service file.
Depending on how many maps are loaded, it may take up to 10 minutes until the bots are online.

### Updating

Re-run the install_bot script


### Log files
Currently TripleA creates log files on its own without relying on stdout.
If anything ever goes wrong, or we just want to check the log files for another reason, we need to look inside the logs directory of the installation folder.
The log folder is located at `/home/triplea/bots/logs/` by default.

