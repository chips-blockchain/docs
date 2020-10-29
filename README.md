# Docker documentation for CHIPS Poker 

[Manual Installation](README.md#manual-installation)

[Docker](README.md#docker) 

[Compile the Docker image](README.md#compile-the-docker-image)

[FAQ](README.md#more-information-on-the-project)

[Thank you](README.md#thank-you)

> Any of the nodes which are used to play must have a publicly accessible IP.


## Manual Installation

[Compilation guide](https://github.com/chips-blockchain/bet/blob/master/compile.md)

## Docker

[Docker image lives here](https://hub.docker.com/r/piggydoughnut/bet)

Pull the image
    
    docker pull piggydoughnut/bet:v1.3

The Docker image has all the necessary libraries, dependencies and all have been installed, compiled, built, cloned, etc. You only need to start the processes needed for the game to work and do some configuration.

Everything lives in the home folder `cd ~`

You can:

- [Run the Docker container and run `chips` and `ln` inside of it](README.md#run-all-inside-the-container)

  Be aware, if you delete your Docker container by accident you can loose the synced LN, your wallet, etc.

OR

- [Plug in your `ln` and `chips` that will run outside of the container](README.md#plug-in-your-ln-and-chips-to-the-container)


---------------------
### Run all inside the container


1. Run Docker container

    `docker run --net=host --name bet -t -i piggydoughnut/bet:v1.3`

    > Note: the Docker container will use your VPS's host network so your player IP will be your VPS IP

2. Run Chips


    #### Create `chips.conf` file

    Create chips.conf file with random username, password, txindex and daemon turned on:
    
    ```shell
    cd ~
    mkdir .chips
    nano .chips/chips.conf
    ```

    Add the following lines into your `chips.conf` file

    ```JSON
    server=1
    daemon=1
    txindex=1
    rpcuser=chipsuser
    rpcpassword=passworddrowssap
    addnode=159.69.23.29
    addnode=95.179.192.102
    addnode=149.56.29.163
    addnode=145.239.149.173
    addnode=178.63.53.110
    addnode=151.80.108.76
    addnode=185.137.233.199
    rpcbind=127.0.0.1
    rpcallowip=127.0.0.1
    ```

    #### Symlinking the binaries
    ```shell
    sudo ln -sf /home/$USER/chips/src/chips-cli /usr/local/bin/chips-cli
    sudo ln -sf /home/$USER/chips/src/chipsd /usr/local/bin/chipsd
    sudo chmod +x /usr/local/bin/chips-cli
    sudo chmod +x /usr/local/bin/chipsd
    ```
    #### Run
    ```shell
    cd ~
    cd chips/src
    ./chipsd &
    ```

    #### Check
    ```shell
    chips-cli getinfo
    ```

    #### Preview block download status
    ```
    cd ~
    cd .chips
    tail -f debug.log
    ```

    #### Fund you address
    ```
    chips-cli getnewaddress
    ```

3. Run the lightning node

    It might take quite a while to sync.
    
    ```
    # Create a tmux session
    tmux new -s lightning
    # Then inside the tmux session you've just created
    ~/lightning/lightningd/lightningd --log-level=debug &
    # CTRL + B, then D to detach from the tmux session.
    ```

4. Run bet

    Depending on which part of the game you want to run see [the repo instructions](https://github.com/chips-blockchain/bet#configuring-the-table)

    If you want to play, you need to run the player node. The player node will automatically connect to an available dealer.

        cd ~/bet/privatebet

        ./bet player


    If you want to host the game, you need to run the dealer node.

        cd ~/bet/privatebet

        ./bet dcv <dcv-ip>

    If you have not allowed for the ln to sync the script will tell you that it is behind ln by a number of blocks.
You can either leave the script running until it syncs or come back later when ln has synched and run it again.


---------------------
### Plug in your `ln` and `chips` to the container

If you do not have the `ln` and `chips` running locally yet but would like to have that setup, see [Compilation guide](https://github.com/chips-blockchain/bet/blob/master/compile.md) for instructions on how to manually run `ln` and `chips`.
Your lightning node must be synced. If it is not, allow it to sync. 

1. Run Docker container

    `docker run --net=host --name bet -t -i -v /$HOME/.chips:/root/.chips:rw  -v /$HOME/.chipsln:/root/.chipsln:rw piggydoughnut/bet:v1.3`

2. Run bet

    Depending on which part of the game you want to run see [the repo instructions](https://github.com/chips-blockchain/bet#configuring-the-table)

    If you want to play, you need to run the player node. The player node will automatically connect to an available dealer.

        cd ~/bet/privatebet

        ./bet player


    If you want to host the game, you need to run the dealer node.

        cd ~/bet/privatebet

        ./bet dcv <dcv-ip>

    If you have not allowed for the ln to sync the script will tell you that it is behind ln by a number of blocks.
You can either leave the script running until it syncs or come back later when ln has synched and run it again.

<img src="https://media.giphy.com/media/jQWUkD7a4AWfkraBJa/giphy.gif" width="300" height="262" />

## Compile the Docker image

If you want to collaborate on the Dockerfile or feel the desire to recompile the image yourself.

    docker build .

You need the `image-id` of the built image

    docker images

Run the image

    docker run --net=host --name bet -dit image-id


## More information on the project

[CHIPS documentation](https://docs.chips.cash/en/latest/)

## Thank you

This guide uses information from the following repositories. Thank you very much for all the provided information.

- https://github.com/chips-blockchain/chips_docs

- https://github.com/NOCTLJRNE/CHIPS-tuto/blob/master/README.md

- https://github.com/chips-blockchain/bet

- [JCharming](https://github.com/Jcharming) for all the help testing the instructions
