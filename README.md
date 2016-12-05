# go-cryptonote-pool

High performance CryptoNote mining stratum written in Golang.

**Stratum feature list:**

* Concurrent shares processing
* AES-NI enabled share validation code with fallback to slow implementation provided by linking with [**Monero**](https://github.com/monero-project/monero) libraries
* Integrated NewRelic performance monitoring plugin

### Installation

Dependencies:

  * go-1.6
  * Everything required to build monero
  * Monero *v0.10.0*

#### Mac OS X

Install required packages:

    brew update && brew install go
    export GOPATH=~/go
    go get github.com/yvasiyarov/gorelic

Download and compile [Monero](https://github.com/monero-project/monero) *v0.9.4*.

Now clone stratum repo and compile it:

    git clone https://github.com/sammy007/go-cryptonote-pool.git
    cmake .
    make

Notice that for share validation stratum requires monero source tree where .a libs already compiled. By default stratum will use <code>../monero</code> directory. You can override this behavior by passing <code>MONERO_DIR</code> env variable:

    MONERO_DIR=/path/to/monero cmake .
    make

Build stratum:

    go build -o pool main.go

#### Linux

I would recommend you to use Ubuntu 16.04 LTS.

Install required packages:

    sudo apt-get install golang
    export GOPATH=~/go
    go get github.com/yvasiyarov/gorelic

In order to successfully link with monero libs, recompile monero with:

    cmake .
    CXXFLAGS="-fPIC" CFLAGS="-fPIC" make

Build CGO extensions:

    MONERO_DIR=/opt/src/monero cmake .
    make

Build stratum:

    go build -o pool main.go

#### Running Stratum

    ./pool config.json

### Configuration

Configuration is self-describing, just copy *config.example.json* to *config.json* and run stratum with path to config file as 1st argument.

```javascript
{
    // Address to where mined blocks will rain
    "address": "46BeWrHpwXmHDpDEUmZBWZfoQpdc6HaERCNmx1pEYL2rAcuwufPN9rXHHtyUA4QVy66qeFQkn6sfK8aHYjA3jk3o1Bv16em",
    // Don't validate login, useful for other CN coins
    "bypassAddressValidation": false,
    // Don't validate shares for efficiency
    "bypassShareValidation": false,

    "threads": 2,

    // Mining endpoints
    "stratum": {
        // TCP timeout for miner, better keep default
        "timeout": "15m",
        // Interval to poll monero node for new jobs
        "blockRefreshInterval": "1s",

        "listen": [
            {
                "host": "0.0.0.0",
                "port": 1111,
                // Stratum port static difficulty
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

    // Monero daemon connection options
    "daemon": {
        "host": "127.0.0.1",
        // Monero RPC port, default is 18081
        "port": 18081,
        "timeout": "10s"
    }
}
```

### Private Pool Guidelines

For personal private pool you can use [DigitalOcean](https://www.digitalocean.com/?refcode=2a6767e6285f) droplet. With recent blockchain-db merged into Monero it's ok to run it even on 5 USD plan. You will receive 10 USD free credit there.

### TODO

In-RAM stats with a simple self hosted frontend.

### Donations

* **BTC**: [16bBz4wZPh7kV53nFMf8LmtJHE2rHsADB2](https://blockchain.info/address/16bBz4wZPh7kV53nFMf8LmtJHE2rHsADB2)
* **XMR**: 4Aag5kkRHmCFHM5aRUtfB2RF3c5NDmk5CVbGdg6fefszEhhFdXhnjiTCr81YxQ9bsi73CSHT3ZN3p82qyakHwZ2GHYqeaUr

### License

Released under the GNU General Public License v2.

http://www.gnu.org/licenses/gpl-2.0.html
