# Matic CLI

🏗 A CLI to setup and manage Matic validator nodes

### Installation (Host Machine, Ubuntu for e.g.)

Please make sure you have installed following dependencies:

* Build Essentials
    ```bash
    sudo apt update
    sudo apt install build-essential
    ```
* Node v10.17.0
    ```bash
    curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
    nvm install 10.17.0
    ```
* Go 1.18+
    ```bash
    wget https://raw.githubusercontent.com/maticnetwork/node-ansible/master/go-install.sh
    bash go-install.sh --remove
    bash go-install.sh
    ```
* Docker (https://docs.docker.com/engine/install/ubuntu/, https://docs.docker.com/engine/install/linux-postinstall/)
* Solc v0.5.16
    ```bash
    sudo snap install solc
    ```
* Python 3
    ```bash
    sudo apt install python3
    alias python=python3
    ```

### Installation (Remote Machine, Ubuntu for e.g.)

Please make sure you have installed following dependencies:

* Go 1.18+
* Rabbitmq
    ```bash
    sudo apt install rabbitmq-server
    ```
* Npm (Only required on ganache remote machine)
    ```bash
    sudo apt update
    sudo apt install nodejs npm
    ```
* Ganache CLI (Only required on ganache remote machine)
    ```bash
    sudo npm install -g ganache-cli
    ```

### Usage

```bash
cd ~
git clone https://github.com/maticnetwork/matic-cli.git
cd matic-cli
npm i
mkdir devnet
cd devnet
```

#### To setup multi-node local network

```bash
../bin/matic-cli setup devnet -c ../configs/devnet/docker-setup-config.yaml
```

You can find more details about configuration options [here](configs/README.md).

Alternatively, setup a local/remote network interactively

```bash
../bin/matic-cli setup devnet -i
```

Notes:
1. We have provided the default values where ever so to ensure smooth functioning of the process

**After the setup is done, follow these steps for local docker deployment:**

Start ganache
```bash
bash docker-ganache-start.sh
```

Start all heimdall instances (it will run all services - rabbitmq, heimdall, bridge, server)
```bash
bash docker-heimdall-start-all.sh
```

Setup bor
```bash
bash docker-bor-setup.sh
```

Start bor
```bash
bash docker-bor-start-all.sh
```

Logs

Logs will be at `logs/` folder


#### To setup multi-node remote network

The following command will setup and run the whole stack  

```bash
../bin/matic-cli setup devnet -c ../configs/devnet/remote-setup-config.yaml
```

You can find more details about configuration options [here](configs/README.md).

Alternatively, setup a remote network interactively

```bash
../bin/matic-cli setup devnet -i
```

Notes:
1. The host machine and remote machine has to be an linux machine with ubuntu as user
2. The ganache URL hostname will be used for ganache - http://<1st machine IP>:9545
3. Make sure that the host machines has access to remote machines for transferring the data
    ###### Tip to persist ssh key for remote access: 
    - eval "$(ssh-agent -s)"
    - ssh-add <pem/cer file(key to all the nodes in devnet)>
4. We have provided the default values where ever so to ensure smooth functioning of the process

#### Clean Setup

Remove the devnet folder and you can start the process once again

## License

MIT



# express-cli 

Requirements:  
- install `terraform` on your local machine: https://learn.hashicorp.com/tutorials/terraform/install-cli
- `node` version v10.17.0. This is also documented in `.nvmrc` file
- generate a keypair on AWS EC2 and download its certificate locally (`.pem` or `.cer` file)
- copy `.env.example` to `.env` with command `cp .env.example .env`
- replace `TF_VAR_ACCESS_KEY` and `TF_VAR_SECRET_KEY` with your own keys (ask devops to generate one for you)
- (optional) replace `TF_VAR_VM_NAME` with your own identifier (it can be any string, default is "polygon-user")
- (optional) replace `TF_VAR_DISK_SIZE_GB` with your preferred disk size in GB (default is 500 GB)
- `VERBOSE=true` prints logs from the remote machines. If set to `false`, only `express-cli` logs - end errors - will be shown
- set `TF_VAR_DOCKERIZED` to `no`. Option `yes` runs the network on one VM only in a dockerized stack, but it's still a WIP (see POS-848)
- make sure `PEM_FILE_PATH` points to a correct AWS key certificate, the one you downloaded in the previous step
- (optional) source the `.env` file if your local system requires to, with command `source .env`  
- see other details of `.env` vars in the `.env.example` template
- `BLOCK_NUMBER` and `BLOCK_TIME` are working as expected most of the times, but a has been reported (see https://0xpolygon.slack.com/archives/C027FCE028P/p1664366701502579). Therefore, for the time being, it is recommended to use single values there. 
- install `express-cli` and `matic-cli` locally with command `npm i`

Run options:
- `./bin/express-cli --init`
  - Initializes terraform and creates some git-ignored files locally. This step is mandatory before running any other command.
- `./bin/express-cli --start` 
  - Creates the desired remote setup, based on the preferences defined in the `.env` file
- `./bin/express-cli --destroy`
  - Destroys the remote setup and delete the local `terraform` files 
- `./bin/express-cli ----update-all`
  - Fetches `heimdall` and `bor` branches defined as `HEIMDALL_BRANCH` and `BOR_BRANCH` in `.env` file, pulls relative changes and restarts those services on the remote machines
- `./bin/express-cli ----update-bor`
  - Fetches `bor` branch defined as `BOR_BRANCH` in `.env` file, pulls relative changes and restarts it on the remote machines
- `./bin/express-cli ----update-heimdall`
  - Fetches `heimdall` branch defined as `HEIMDALL_BRANCH` in `.env` file, pulls relative changes and restarts it on the remote machines
