# LUKSO validator docker files

This is a set of files used to run a LUKSO validator using docker-compose

## Setup

- create `lukso-mainnet/` folder
- create `lukso-mainnet/configs` folder
- get the needed files:

```bash
cd configs/
wget https://raw.githubusercontent.com/lukso-network/network-configs/main/mainnet/lighthouse/deploy_block.txt
wget -O genesis.ssz https://raw.githubusercontent.com/lukso-network/network-configs/main/mainnet/shared/genesis_42.ssz
wget -O genesis.json https://raw.githubusercontent.com/lukso-network/network-configs/main/mainnet/shared/genesis_42.json
wget https://raw.githubusercontent.com/lukso-network/network-configs/main/mainnet/shared/config.yaml
```

- you should have 4 new files (config.yaml / genesis.json / genesis.ssz / deploy_block.txt) in the `/configs` folder
- generate JWT `openssl rand -hex 32 | tr -d "\n" > "./configs/jwt.hex"`

## How to import your validator keys

- create a folder (e.g. `/home/ubuntu/secrets`)
- copy your keystores*.json files in it (1 per validator)
- create a file containing your password (e.g. `/home/ubuntu/secrets/password.txt`)
- run the docker-compose file (`docker-compose up -d`). it will freeze on the `prysm_validator_import`, that's good sign, it imports the keys (it takes around 15min to import 1k keys)
- later, you can remove the `prysm_validator_import` container from the docker-compose file and re-run docker-compose

## Run

```bash
docker-compose up -d
```

## Check logs

In the `lukso-mainnet` folder:
```bash
docker-compose logs -f prysm_validator # Validator logs
docker-compose logs -f prysm_beacon # Consensus layer logs
docker-compose logs -f geth # Execution layer logs
```