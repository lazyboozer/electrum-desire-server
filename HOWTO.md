### HOW TO (own how to by Chinafreak)

Tested on Ubuntu 17.04 Server. Other linux server (debian, etc.) or even windows/mac server might be a little different.

## Create a user for running desired and Electrum Desire server

This step is optional, but for better security and resource separation I
suggest you create a separate user just for running `desired` and Electrum.
We will also use the `~/bin` directory to keep locally installed files
(others might want to use `/usr/local/bin` instead). We will download source
code files to the `~/src` directory.

    $ sudo adduser desire --disabled-password
    $ sudo su - desire
    $ mkdir ~/bin ~/src
    $ echo $PATH

If you don't see `/home/desire/bin` in the output, you should add this line
to your `.bashrc`, `.profile`, or `.bash_profile`, then logout and relogin:

    $ PATH="$HOME/bin:$PATH"
    $ exit


## Install all necessary packages
    $ sudo dpkg --add-architecture i386
    $ sudo apt-get update
    $ sudo apt-get install
    $ sudo apt-get install git libssl-dev:i386 autoconf libtool libboost-system-dev libboost-filesystem-dev libboost-chrono-dev libboost-program-options-dev libboost-test-dev libboost-thread-dev autotools-dev libssl-dev pkg-config libevent-dev


## Get db4.8 source, compile and install

    $ wget http://download.oracle.com/berkeley-db/db-4.8.30.NC.tar.gz
    $ tar -xzvf db-4.8.30.NC.tar.gz
    $ cd db-4.8.30.NC/build_unix
    $ ../dist/configure --enable-cxx
    $ make
    $ sudo make install


## Tell your system where to find db4.8

    $ export BDB_INCLUDE_PATH="/usr/local/BerkeleyDB.4.8/include"
    $ export BDB_LIB_PATH="/usr/local/BerkeleyDB.4.8/lib"
    $ ln -s /usr/local/BerkeleyDB.4.8/lib/libdb-4.8.so /usr/lib/libdb-4.8.so


## (Optimal) Building Desire Wallet 
I decided to build directly instead downloading direct from website (http://desire-crypto.com) because I couldn't run compiled linux build on ubuntu server. So I'll compiled it. If you can run compiled wallet on your server, then skip this part.

    $ sudo su - desire
    $ cd ~/src
    $ git clone https://github.com/lazyboozer/Desire.git
    $ cd Desire
    $ sudo bash autogen.sh
    $ sudo bash configure CPPFLAGS="-I/usr/local/BerkeleyDB.4.8/include -O2" LDFLAGS="-L/usr/local/BerkeleyDB.4.8/lib"
    $ sudo make
    $ sudo make install # optional

If ```sudo make``` throws permission error about ```../share/genbuild.sh```, then enter this:

    $sudo chmod 777 share/genbuild.sh

And then try ```sudo make```  again.

## Configure and start desired
    $ mkdir ~/.desirecore
    $ nano ~/.desirecore/desire.conf

Write this in desire.conf:

    rpcuser=<rpc-username>
    rpcpassword=<rpc-password>
    daemon=1
    txindex=1

If you have an existing installation of ```desired``` and have not previously set txindex=1 you need to reindex the blockchain by running

    $ desired -reindex

If you already have a freshly indexed copy of the blockchain with txindex start ```desired```:

    $ desired

Allow some time to pass for desired to connect to the network and start downloading blocks. You can check its progress by running:

    $ desire-cli getblockchaininfo

Before starting the Electrum Desire server your desired should have processed all blocks and caught up to the current height of the network (not just the headers). You should also set up your system to automatically start desired at boot time, running as the 'desire' user. Check your system documentation to find out the best way to do this.

##  Install Electrum Desire dependencies manually
    $ sudo apt-get install python-setuptools python-openssl python-leveldb libleveldb-dev
    $ sudo easy_install jsonrpclib irc plyvel x11_hash

## Download and install Electrum Desire server

Coming soon...