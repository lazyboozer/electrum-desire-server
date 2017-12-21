### Create a user for running desired and Electrum Desire server

This step is optional, but for better security and resource separation I
suggest you create a separate user just for running `desired` and Electrum.
We will also use the `~/bin` directory to keep locally installed files
(others might want to use `/usr/local/bin` instead). We will download source
code files to the `~/src` directory.

    $ sudo adduser desire --disabled-password
    $ sudo apt-get install git
    $ sudo su - desire
    $ mkdir ~/bin ~/src
    $ echo $PATH

If you don't see `/home/desire/bin` in the output, you should add this line
to your `.bashrc`, `.profile`, or `.bash_profile`, then logout and relogin:

    $ PATH="$HOME/bin:$PATH"
    $ exit


### Download db4.8


### Get db4.8 source, compile and install

    $ wget http://download.oracle.com/berkeley-db/db-4.8.30.NC.tar.gz
    $ tar -xzvf db-4.8.30.NC.tar.gz
    $ cd db-4.8.30.NC/build_unix
    $ ../dist/configure --enable-cxx
    $ make
    $ sudo make install


### Tell your system where to find db4.8

    $ export BDB_INCLUDE_PATH="/usr/local/BerkeleyDB.4.8/include"
    $ export BDB_LIB_PATH="/usr/local/BerkeleyDB.4.8/lib"
    $ ln -s /usr/local/BerkeleyDB.4.8/lib/libdb-4.8.so /usr/lib/libdb-4.8.so


### Building Desire Wallet
    $ sudo su - desire
    $ cd ~/src
    $ git clone https://github.com/lazyboozer/Desire.git
    $ cd Desire
    $ ./autogen.sh
    $ ./configure CPPFLAGS="-I/usr/local/BerkeleyDB.4.8/include -O2" LDFLAGS="-L/usr/local/BerkeleyDB.4.8/lib"
    $ make
    $ make install # optional

### Configure and start desired
    $ mkdir ~/.desirecore
    $ $EDITOR ~/.desirecore/desire.conf

Write this in desire.conf:

    rpcuser=<rpc-username>
    rpcpassword=<rpc-password>
    daemon=1
    txindex=1

start desired:

    $ desired


### Install Python Packages
    $ sudo apt-get install python-setuptools python-openssl python-leveldb libleveldb-dev
    $ sudo easy_install jsonrpclib irc plyvel x11_hash
