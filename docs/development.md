# Setting up Development Environment

## Install Node.js

Install Node.js by your favorite method, or use Node Version Manager by following directions at https://github.com/creationix/nvm

```bash
nvm install v4
```

## Fork and Download Repositories

To develop unitedcore-node:

```bash
cd ~
git clone git@github.com:<yourusername>/unitedcore-node.git
git clone git@github.com:<yourusername>/unitedcore-lib.git
```

To develop unitedcoin or to compile from source:

```bash
git clone git@github.com:<yourusername>/unitedcoin.git
git fetch origin <branchname>:<branchname>
git checkout <branchname>
```
**Note**: See unitedcoin documentation for building unitedcoin on your platform.


## Install Development Dependencies

For Ubuntu:
```bash
sudo apt-get install libzmq3-dev
sudo apt-get install build-essential
```
**Note**: Make sure that libzmq-dev is not installed, it should be removed when installing libzmq3-dev.


For Mac OS X:
```bash
brew install zeromq
```

## Install and Symlink

```bash
cd unitedcore-lib
npm install
cd ../unitedcore-node
npm install
```
**Note**: If you get a message about not being able to download unitedcoin distribution, you'll need to compile unitedcoind from source, and setup your configuration to use that version.


We now will setup symlinks in `unitedcore-node` *(repeat this for any other modules you're planning on developing)*:
```bash
cd node_modules
rm -rf unitedcore-lib
ln -s ~/unitedcore-lib
rm -rf bitcoind-rpc
ln -s ~/bitcoind-rpc
```

And if you're compiling or developing unitedcoin:
```bash
cd ../bin
ln -sf ~/unitedcoin/src/unitedcoind
```

## Run Tests

If you do not already have mocha installed:
```bash
npm install mocha -g
```

To run all test suites:
```bash
cd unitedcore-node
npm run regtest
npm run test
```

To run a specific unit test in watch mode:
```bash
mocha -w -R spec test/services/bitcoind.unit.js
```

To run a specific regtest:
```bash
mocha -R spec regtest/bitcoind.js
```

## Running a Development Node

To test running the node, you can setup a configuration that will specify development versions of all of the services:

```bash
cd ~
mkdir devnode
cd devnode
mkdir node_modules
touch unitedcore-node.json
touch package.json
```

Edit `unitedcore-node.json` with something similar to:
```json
{
  "network": "livenet",
  "port": 3001,
  "services": [
    "unitedcoind",
    "web",
    "insight-api",
    "insight-ui",
    "<additional_service>"
  ],
  "servicesConfig": {
    "unitedcoind": {
      "spawn": {
        "datadir": "/home/<youruser>/.unitedcoin",
        "exec": "/home/<youruser>/unitedcoin/src/unitedcoind"
      }
    }
  }
}
```

**Note**: To install services [insight-api](https://github.com/bitpay/insight-api) and [insight-ui](https://github.com/bitpay/insight-ui) you'll need to clone the repositories locally.

Setup symlinks for all of the services and dependencies:

```bash
cd node_modules
ln -s ~/unitedcore-lib
ln -s ~/unitedcore-node
ln -s ~/insight-api
ln -s ~/insight-ui
```

Make sure that the `<datadir>/unitedcoin.conf` has the necessary settings, for example:
```
server=1
whitelist=127.0.0.1
txindex=1
addressindex=1
timestampindex=1
spentindex=1
zmqpubrawtx=tcp://127.0.0.1:29332
zmqpubhashblock=tcp://127.0.0.1:29332
rpcallowip=127.0.0.1
rpcuser=unitedcoin
rpcpassword=local321
```

From within the `devnode` directory with the configuration file, start the node:
```bash
../unitedcore-node/bin/unitedcore-node start
```
