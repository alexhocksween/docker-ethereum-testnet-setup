# docker-ethereum-testnet-setup

This is a guide on how to use docker containers to get mist up and running as per the Udemy course: https://www.udemy.com/blockchain-developer


# Diclaimer

This runs a few services that will be accessible to anyone else on your network. It is suggested that you only do this when you're on a secure network (e.g. home wifi).


# The Details

## Images

* [akhs/geth](https://hub.docker.com/r/akhs/geth/): An Ubuntu 16.04 image with the Ethereum C++ eth binaries installed.
* [akhs/swarm](https://hub.docker.com/r/akhs/geth/): An Alpine 3.5 image with swarm.


## Getting started

First step is to install docker for mac:

https://www.docker.com/docker-mac


Once you've installed that and it's running on your system, open terminal and go into the directory where you want to create your chain data.

In that directory, create the genesis.json file that you want to use.


## Initializing the chain

Next, we're going to boot a geth docker container and use it to initialize our chain data. We do that by running this command

```
docker container run --rm -v "$(pwd):/root/chaindata" -ti akhs/geth bash

```
The `--rm` option removes the container when we're done.

The `-v "$(pwd):/root/chaindata"` option binds your current directory to the /root/chaindata folder in the container.

The `-ti` option connects you to the container.

The `akhs/geth` specifies the image.

The `bash` executes instead of the default command, which allows you to connect and execute commands on the container.



If all goes according to plan, after executing the command, you should be presented with a prompt that says `root@(random lettes/ numbers)`.

Initialize the chain by executing this command
```
geth init /root/chaindata/genesis.json --datadir /root/chaindata/
```

You should see some folders appear in your local folder (on your mac).


Now we will create an account, by executing this command:
```
geth --datadir=/root/chaindata account new
```


This will prompt you for a passphrase, which you'll need to remember for the next step. Also take note of the the address output, you'll need this as well:
```
Address: {7...}
```


You can now type `exit` to exit out of the container.



Now that we've set things up, we can start geth on our new network, by running this:
```
docker container run --rm -v "$(pwd):/root/chaindata" -p "8545:8545" akhs/geth
```


## Getting swarm up and running

Now we neeed to boot the swarm image.

```
docker container run --rm -v "$(pwd):/root/chaindata" -p "8500:8500" -ti -e SWARM_ACCOUNT=(the account number from above - remove brackets) akhs/swarm
```

The `-ti` option allows you to interact with the output so you can type in your password

The `-e SWARM_ACCOUNT=7...` specifies the account number and is picked up by swarm


## After completing the above steps, you shuold be able to start Mist using:

```
cd /Applications/Mist.app/Contents/MacOS
./Mist --rpc http://127.0.0.1:8545 --swarmurl http://127.0.0.1:8500
```


## Geth console
If you want to use the attach command, you can boot another geth image:
```
docker container run --rm -v "$(pwd):/root/chaindata" -ti akhs/geth bash
```


Then you can connect to geth with this command:
```
geth attach http://(the ip address of your computer on whatever network you're using):8545

```
We have enabled all of the APIs over the http rpc, so this will allow you to do whatever you want.



