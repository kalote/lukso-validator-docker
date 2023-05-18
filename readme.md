# LUKSO validator docker files

This is a set of files used to run a LUKSO validator using docker-compose

## How to set it up

- connect to your node
- `git clone` this repo on your node
- create `lukso-mainnet/` folder
- create `lukso-mainnet/configs` folder
- get the needed files:

```bash
cd configs/
wget -O genesis.ssz https://raw.githubusercontent.com/lukso-network/network-configs/main/mainnet/shared/genesis_42.ssz
wget -O genesis.json https://raw.githubusercontent.com/lukso-network/network-configs/main/mainnet/shared/genesis_42.json
wget https://raw.githubusercontent.com/lukso-network/network-configs/main/mainnet/shared/config.yaml
```

- you should have 4 new files (config.yaml / genesis.json / genesis.ssz / deploy_block.txt) in the `/configs` folder
- generate JWT `openssl rand -hex 32 | tr -d "\n" > "./jwt.hex"`
- update the `.env` file with the correct values (YOURIP, YOURNODENAME, ETHSTATSPASSWORD, YOURWALLETADDR)
- copy the `docker-compose.yml` and `.env` files in the `lukso-mainnet/` folder
- you shoud have the following data structure:

```bash
- home/
  |_ lukso-validator-docker/
  |_ lukso_mainnet/
    |__ .env
    |__ docker-compose.yml
    |__ configs/
      |__ genesis.json
      |__ genesis.ssz
      |__ config.yaml
      |__ jwt.hex
```

## How to import your validator keys

- create a folder (e.g. `mkdir /home/ubuntu/secrets`)
- copy your keystores*.json files in it (1 per validator)
- create a file containing your password (e.g. `/home/ubuntu/secrets/password.txt`)
- run the docker-compose file (`docker-compose up -d`). (note: it will freeze on the `prysm_validator_import`, that's good sign, it imports the keys (it takes around 15min to import 1k keys))
- you can check that everything was run correctly with `docker-compose logs prysm_validator_import`. You should see a message like the following: `Successfully imported X keys`
- when it's done, you can stop your containers and remove the lines referring to the `prysm_validator_import` container (123-124 and also from line 97 to 115) from the docker-compose file

```bash
docker-compose down
# edit the docker-compose.yml to remove the lines then ...
docker-compose up -d
```

## How to run

```bash
cd lukso-mainnet
docker-compose up -d
```

## Check logs

```bash
cd lukso-mainnet
docker-compose logs -f prysm_validator # Validator logs (all good if you see "Validator activated")
docker-compose logs -f prysm_beacon # Consensus layer logs (all good if you see "msg="XhYmZs until chain genesis")
docker-compose logs -f geth # Execution layer logs (all good if you see "waiting for beacon client sync switch-over...")
```