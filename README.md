# Bots

## Linux Bot install

As root on a brand new linode machine (Ubuntu 16.04):

### One time setup
```
bash <(curl -s https://raw.githubusercontent.com/triplea-game/lobby/master/install_triplea_user) 
bash <(curl -s https://raw.githubusercontent.com/triplea-game/lobby/master/install_maps) 
```

### Install bot Binaries
```
cd /root
rm -f setup_bot; wget https://raw.githubusercontent.com/triplea-game/lobby/master/setup_bot; chmod +x setup_bot

./setup_bot 1.9.0.0.7534 SG 30 4
./setup_bot 1.9.0.0.7534 DE 40 4
./setup_bot 1.9.0.0.7534 JP 50 4
./setup_bot 1.9.0.0.7534 GA_USA 60 4
./setup_bot 1.9.0.0.7534 NJ_USA 70 4
./setup_bot 1.9.0.0.7534 CA_USA 80 4
./setup_bot 1.9.0.0.7534 TX_USA 90 4
```

## Check Install

Check if service script has latest location:
```
cat /lib/systemd/system/triplea-bot@.service
```

Check binaries were installed:
```
ls /home/triplea/bots
```

## Start Bots

Log in as triplea user, run:
```
/home/triplea/start_all
```

Can check if bot java processes are running:
```
ps -ef | grep java
```

Check service status (replace 'N' with bot number):
```
sudo service triplea-bot@N status
```


# Lobby

- Documentation and install scripts for lobby and bots

## Lobby Install

```
rm -f setup_lobby; wget --no-cache https://raw.githubusercontent.com/triplea-game/lobby/master/setup_lobby; chmod +x setup_lobby
sudo ./setup_lobby -h
```

Install command example:

```
sudo ./setup_lobby 1.9.0.0.6520 3304 /home/triplea/lobby
```

Update properties:

```
sudo -u triplea vim /home/triplea/lobby/1.9.0.0.6520/config/lobby/lobby.properties
```

Change the value of the `postgres_password` property to the correct password for this server (refer to the Master Secrets document).

**NOTE:** If you are setting up a staging server, change the value of the `postgres_database` property to `ta_users_staging`.

Log in to DB and look at tables:

```
sudo -u postgres psql postgres
\l
\c ta_users
\d
```

## Lobby uninstall

```
$ sudo /home/triplea/lobby/1.9.0.0.6520/uninstall_lobby
```

## Lobby DB setup

```
sudo apt update
sudo apt install postgresql
```

### Set password

```
sudo -u postgres psql postgres
\password postgres
```

### Set up DB

#### Create database

```
$ bash <(curl https://raw.githubusercontent.com/triplea-game/lobby/master/files/lobby/create_db) ta_users
```

**NOTE:** If you are setting up a staging server, replace `ta_users` with `ta_users_staging`.

#### Initialize database schema

```
$ bash <(curl https://raw.githubusercontent.com/triplea-game/lobby/master/files/lobby/download_db_migration_scripts) ./db_migration_scripts
$ bash <(curl https://raw.githubusercontent.com/triplea-game/lobby/master/files/lobby/migrate_db) ./db_migration_scripts 0 ta_users
$ rm -rf ./db_migration_scripts
```

**NOTE:** If you are setting up a staging server, replace `ta_users` with `ta_users_staging`.

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

java -server -Xmx320m -Djava.awt.headless=true -classpath "bin/*" games.strategy.engine.framework.headlessGameServer.HeadlessGameServer -Ptriplea.game.host.console=false -Ptriplea.game= -Ptriplea.server=true -Ptriplea.port=%PORT% -Ptriplea.lobby.host=%LOBBY_HOST% -Ptriplea.lobby.port=%LOBBY_PORT% -Ptriplea.name=Bot%BOT_NUMBER%_WarClub -Ptriplea.lobby.game.hostedBy=Bot%BOT_NUMBER%_WarClub -Ptriplea.lobby.game.supportEmail=youremail@hotmail.com -Ptriplea.lobby.game.comments="automated_host" -Ptriplea.lobby.game.reconnection=172800 -PmapFolder=../maps

pause
```

### Bot Maintenance

Maps need to be updated periodically, run:
```
cd /home/triplea/maps; ./download_all_maps
```

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


