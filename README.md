# monero-stratum


High performance CryptoNote mining stratum with Web-interface written in Golang.

[![Go Report Card](https://goreportcard.com/badge/github.com/SmokinCache/monero-stratum)](https://goreportcard.com/report/github.com/SmokinCache/monero-stratum)
[![CircleCI](https://circleci.com/gh/SmokinCache/monero-stratum.svg?style=svg)](https://circleci.com/gh/SmokinCache/monero-stratum)

**Stratum feature list:**

* Be your own pool
* Rigs availability monitoring
* Keep track of accepts, rejects, blocks stats
* Easy detection of sick rigs
* Daemon failover list
* Concurrent shares processing
* Beautiful Web-interface


## Installation

Dependencies:

  * go-1.11.1 (Confirmed working, other may as well. 1.6 will not)
  * Everything required to build Monero
  * Monero >= **v0.14.0.2** (sometimes `master` branch required)

### Linux

Use Ubuntu 16.04 LTS.

Compile Monero source (with shared libraries option):

    apt-get install git cmake build-essential libssl-dev pkg-config libboost-all-dev
    git clone https://github.com/monero-project/monero.git
    cd monero
    git checkout tags/v0.14.0.2 -b v0.14.0.2
    cmake -DBUILD_SHARED_LIBS=1 .
    make

Install Golang and required packages:

    curl -O https://storage.googleapis.com/golang/go1.11.1.linux-amd64.tar.gz

    sudo tar -C /usr/local -xzf go1.11.1.linux-amd64.tar.gz
    mkdir -p ~/go; echo "export GOPATH=$HOME/go" >> ~/.bashrc
    echo "export PATH=$PATH:$HOME/go/bin:/usr/local/go/bin" >> ~/.bashrc
    source ~/.bashrc

Clone stratum:

    git clone https://github.com/SmokinCache/monero-stratum.git
    cd monero-stratum

Build stratum:

    MONERO_DIR=/path/to/monero cmake .
    make

`MONERO_DIR=/path/to/monero` is optional, not needed if both `monero` and `monero-stratum` is in the same directory like `/opt/src/`. By default make will search for monero libraries in `../monero`. You can just run `cmake .`.

### Mac OS X (I have not tested this build on OS X. I suspect go version installed below will not work)

Compile Monero source:

    git clone https://github.com/monero-project/monero.git
    cd monero
    git checkout tags/v0.14.0.2 -b v0.14.0.2
    cmake .
    make

Install Golang and required packages:

    brew update && brew install go

Clone stratum:

    git clone https://github.com/SmokinCache/monero-stratum.git
    cd monero-stratum

Build stratum:

    MONERO_DIR=/path/to/monero cmake .
    make

### Running Stratum

    ./build/bin/monero-stratum config.json

If you need to bind to privileged ports and don't want to run from `root`:

    sudo apt-get install libcap2-bin
    sudo setcap 'cap_net_bind_service=+ep' /path/to/monero-stratum

## Configuration

Configuration is self-describing, just copy *config.example.json* to *config.json* and run stratum with path to config file as 1st argument.

```javascript
{
  // Address for block rewards
  "address": "YOUR-ADDRESS-NOT-EXCHANGE",
  // Don't validate address
  "bypassAddressValidation": true,
  // Don't validate shares
  "bypassShareValidation": true,

  "threads": 2,

  "estimationWindow": "15m",
  "luckWindow": "24h",
  "largeLuckWindow": "72h",

  // Interval to poll daemon for new jobs
  "blockRefreshInterval": "1s",

  "stratum": {
    // Socket timeout
    "timeout": "15m",

    "listen": [
      {
        "host": "0.0.0.0",
        "port": 1111,
        "diff": 5000,
        "maxConn": 32768
      },
      {
        "host": "0.0.0.0",
        "port": 3333,
        "diff": 10000,
        "maxConn": 32768
      }
    ]
  },

  "frontend": {
    "enabled": true,
    "listen": "0.0.0.0:8082",
    "login": "admin",
    "password": "",
    "hideIP": false
  },

  "upstreamCheckInterval": "5s",

  "upstream": [
    {
      "name": "Main",
      "host": "127.0.0.1",
      "port": 18081,
      "timeout": "10s"
    }
  ]
}
```

You must use `anything.WorkerID` as username in your miner. Either disable address validation or use `<address>.WorkerID` as username. If there is no workerID specified your rig stats will be merged under `0` worker. If mining software contains dev fee rounds its stats will usually appear under `0` worker. This stratum acts like your own pool, the only exception is that you will get rewarded only after block found, shares only used for stats.

### Donations

***Sammy007***
**XMR**: `47v4BWeUPFrM9YkYRYk2pkS9CubAPEc7BJjNjg4FvF66Y2oVrTAaBjDZhmFzAXgqCNRvBH2gupQ2gNag2FkP983ZMptvUWG`
***SmokinCache***
**XMR**: `448AYGS4t2J2j8Cy1EtSb26HAsbx8tBnZ2JFKrAcsLFqiR3z7ejkn24CKTBEZ7R3AyJYSdU1a8SNwP7DpmRcR1kxKjARmo2`

![](https://cdn.pbrd.co/images/GP5tI1D.png)

Highly appreciated.

### License

Released under the GNU General Public License v2.

http://www.gnu.org/licenses/gpl-2.0.html
