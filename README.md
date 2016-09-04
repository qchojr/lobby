# lobby
- Things relating to the tripleA lobby
- Contains any scripts that are used on the server

## Deploying
- Likely to be an HTTPS git clone, ideally per user, but perhaps under the shared account
 
## Bot Setup
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
