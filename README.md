# ℤ - The Groth Testnet!

### Rules

 - Participants are required to keep themselves updated regarding the announcements published on our Discord and Twitter.
 - You have to update your node software when asked. Outdated nodes will not receive their rewards.
 - In order to control the height progress of the testnet, we have put a hard-coded height limit on our software. The height limit is currently set to: **43200** blocks (About one month).
 - During the testnet, we may be required to resync our nodes or restart building blocks from height 0. Do not panic! We will keep track of all participants' contribution even after fresh starts!

### How to run a Bazuka node?

If you only want to run a Ziesha node and do not want to execute Zero Contract or
mine Ziesha, you will only need to install ![zoro](https://github.com/ziesha-network/bazuka). In case you also
want to mine Ziesha, there are two roads:

 1. Mine as a solo-miner: You will need to install ![zoro](https://github.com/ziesha-network/zoro) (The Main Payment Network executor) and also the ![uzi-miner](https://github.com/ziesha-network/uzi-miner) (A RandomX CPU miner).
 2. Mine in a mining pool: You will only need to install ![uzi-miner](https://github.com/ziesha-network/uzi-miner)

**How to install `bazuka`?**

 * Prepare a Linux machine.
 * Make sure you have installed `libssl-dev` and `cmake` packages.

    ```
    sudo apt-get update && sudo apt-get upgrade
    ```

    ```
    sudo apt install -y build-essential libssl-dev cmake
    ```

 * Install the Rust toolchain (https://rustup.rs/)
    ```
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
    ```
 * Clone the `bazuka` repo:
    ```
    git clone https://github.com/ziesha-network/bazuka
    ```
 * ***Warning:*** Make sure Rust binaries are present in your PATH before compiling:
    ```
    source "$HOME/.cargo/env"
    ```
 * Compile and install:
    ```
    cd bazuka
    cargo install --path .
    ```

Now if you want to join the `groth-4` testnet, you first have to initialize your
node. If you have already initialized bazuka in an older testnet, you first need
to remove your previous initialization by running:

```sh
rm ~/.bazuka.yaml
```

***IMPORTANT!*** *If you have participated in the Chaos-Testnet, you should know that we have had breaking change on the way our wallet seeds work. From now on, you won't need to enter the seed yourself, a random 12-word mnemonic code will be generated for you instead! Therefore, the old seed you used in the Chaos-Testnet is not valid anymore and **can be thrown away**. We have tracked your node activity in the old network based on your Discord-id and Ip address, but in this testnet, you will be authenticated based on the wallet-address derived from you 12-word mnemonic phrase.*

```sh
bazuka init [flags...]
```

Available flags:

 * `--bootstrap <bootstrap>...`: You can use the nodes introduced by the community as your bootstrap nodes through this flag. (E.g You can use  one of our nodes: `65.108.193.133:8765`)
 * `--db <db>`: Path of the node's database. Default: `~/.bazuka`.
 * `--external <external>`: Public ip/port of your node. Default: `YOUR_PUBLIC_IP:8765`.
 * `--listen <listen>`: Local socket. Default: `0.0.0.0:8765`.
 * `--mnemonic <mnemonic>`: If you already have a 12-word mnemonic phrase, you can pass it through this flag. If not provided, a new wallet will be generated for you. Keep the mnemonic word list somewhere safe!
 * `--network <network>`: The network your node will operate on. Default: `mainnet`

Example to initialize a node with 2 bootstrap nodes `65.108.193.133:8765` on the `groth-4` network:

```
bazuka init --network groth-4 --bootstrap 65.108.193.133:8765 --mnemonic "YOUR OLD MNEMONIC PHRASE"
```

If you don't have a mnemonic phrase and you would like `bazuka` to generate one for you:

```
bazuka init --network groth-4 --bootstrap 65.108.193.133:8765
```

***Make sure you write down the mnemonic phrase generated for you somewhere safe! All your rewards will go into this public-key, in the future mainnet!***

After initializing your node you can run it through:

```sh
bazuka node start --discord-handle "YOUR DISCORD HANDLE"
```

**IMPORTANT:** You are required to provide your Discord handle through the
`--discord-handle` flag. By providing your handle, we will be able to authenticate
your node, as a part of rewarding process.

### What is the Main Payment Network?

The Main Payment Network (MPN) is a special, builtin smart-contract that is
created in the genesis-block of the Ziesha Protocol. It manages a merkle-tree of
millions of accounts that can transfer ℤ with each other with nearly zero-cost
transactions. It uses the Groth16 proving system and has a fixed number of
transaction slots per update.

<p align="center">
    <img width="400" src="https://user-images.githubusercontent.com/4275654/188954000-450b32ad-c5e8-4714-9664-3afa40400508.png" alt="Deposit/Withdraw/Rsend/Zsend">
</p>

### Mine Ziesha (In a mining pool!)

(**NOTE:** If you want to build a mining pool, follow the guide here: https://github.com/ziesha-network/uzi-pool)

You need to follow the steps:

 1. Find out your wallet-address by running `bazuka wallet`.
 2. Install/update `uzi-miner` on your system:
    ```
    git clone https://github.com/ziesha-network/uzi-miner
    cd uzi-miner
    cargo update
    cargo install --path .
    ```
 3. Provide your wallet-address to the pool owner. He will give you **Miner Token** in return, which should be passed to `uzi-miner`.
 4. Run uzi-miner with **--pool** flag:
    ```
    uzi-miner --pool --node IP_OF_THE_POOL:8766 --threads 32 --miner-token MINER_TOKEN
    ```
    (Note: Change number of `--threads` based on the spec of your system)

### Mine Ziesha (As a solo-miner!)

In order to be a miner, besides working on a PoW puzzle, you will also need to
execute the MPN contract on each block you generate. The `zoro` software is
a CPU/GPU-executor of MPN contract. In order to run `zoro`, you'll need a machine
with at least 32GB of RAM. If you want to be competitive miner you'll also have to
competitive CPU. (In future versions, GPU will be used instead of CPU)

1. Make sure Bazuka is the latest version.

   ```
   cd bazuka
   git pull origin master
   cargo install --path .
   ```

   If you get a `DifferentGenesis` error, it means that the genesis block has changed
   in the updated software. So you need to start fresh. Remove the `~/.bazuka`
   folder by running: `rm -rf ~/.bazuka`

   Now run Bazuka again.

2. Install the MPN executor (`zoro`)

   Make sure you have your GPU drivers installed. You also have to install `ocl-icd-opencl-dev`! ***(Currently, only NVIDIA GPUs are supported!)***


   ```
   apt install ocl-icd-opencl-dev
   ```

   Then:

   ```
   git clone https://github.com/ziesha-network/zoro
   cd zoro
   cargo install --path .
   ```

3. Download the proving parameters

   ```
   wget https://api.rues.info/update.dat
   wget https://api.rues.info/withdraw.dat
   wget https://api.rues.info/deposit.dat
   ```

4. Run `zoro` beside your node: (This will use your Nvidia GPU for mining!)

   ```sh
   zoro --node 127.0.0.1:8765 --seed "SEED PHRASE FOR THE EXECUTOR ACCOUNT" --network groth-4 \
     --update-circuit-params update.dat --deposit-circuit-params deposit.dat \
     --withdraw-circuit-params withdraw.dat --db $HOME/.bazuka --gpu
   ```

   **Note:** You can switch to CPU by removing the `--gpu` flag, but your chances of mining a block
   will be very little with a CPU executor!

   (Note: The seed phrase for the executor account needs to be different from the
   seed you use for your node! The transaction fees of the Zero transactions processed
   by your executor will go to this account)

5. After a new block is generated, the `uzi-miner` should start working on the PoW
  puzzle, so you will also need to have `uzi-miner` running on your system:

   ```
   git clone https://github.com/ziesha-network/uzi-miner
   cd uzi-miner
   cargo install --path .
   uzi-miner --node 127.0.0.1:8765 --threads 32
   ```

   (Note: Change number of `--threads` based on the spec of your system)
