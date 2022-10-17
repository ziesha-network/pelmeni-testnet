# ℤ - The Chaos Testnet!

The time has come! Zeeka's very first incentivized testnet is scheduled to start
on 9:30AM UTC 20th October!

### Rules

 - Participants are required to keep themselves updated regarding the announcements published on our Discord and Twitter.
 - You have to updat your node/executor/miner software when asked. Outdated miners/nodes will not receive their rewards.
 - In order to control the height progress of the testnet, we have put a hard-coded height limit on our software. The height limit is currently set to: **5000** blocks.
 - During the testnet, we may be required to resync our nodes or restart building blocks from height 0. Do not panic! We will keep track of all participants' contribution even after fresh starts!

### How to run a Bazuka node?

If you only want to run a Zeeka node and do not want to execute Zero Contract or
mine Zeeka, you will only need to install `bazuka` (This repo). In case you also
want to mine Zeeka, you will need to install ![zoro](https://github.com/zeeka-network/zoro)
(The Main Payment Network executor) and also the ![uzi-miner](https://github.com/zeeka-network/uzi-miner)
(A RandomX CPU miner).

**How to install `bazuka`?**

 * Prepare a Linux machine.
 * Make sure you have installed `libssl-dev` and `cmake` packages.
    ```
    sudo apt install -y libssl-dev cmake
    ```
 * Install the Rust toolchain (https://rustup.rs/)
    ```
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
    ```
 * Clone the `bazuka` repo:
    ```
    git clone https://github.com/zeeka-network/bazuka
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

Now if you want to join the `chaos` testnet, you first have to initialize your
node by running:

```sh
bazuka init --seed [your seed phrase] --network chaos --node 127.0.0.1:8765
```

(Example seed: `"A_RANDOM_STRING_THAT_IS_LONG_ENOUGH_783264dfhejfjcgefjkef"`)

The seed is a random string of characters, not mnemonic phrases. **You do not need to generate it using wallets! (E.g Metamask)**
The longer the seed is, the safer. We suggest a seed string of at least 80 random characters. Your regular and zero-knowledge private-keys are derived from the `--seed` value, so **don't forget to keep them somewhere safe and do not share it**!

Run your node:

```sh
bazuka node --listen 0.0.0.0:8765 --external [your external ip]:8765 \
  --network chaos --db ~/.bazuka-chaos --bootstrap [bootstrap node 1] --bootstrap [bootstrap node 2] ...
```

You can use the nodes introduced by the community as your `--bootstrap` nodes.
You either have to run your node on a machine with a static IP, or configure a NAT
Virtual Server in order to expose your node on a public IP. Specify your public IP
through the `--external` option.

Highly recommended to also provide your Discord handle through the
`--discord-handle` flag. By providing your handle, you will leave our bots a
way to contact you regarding the problems you may have in your node and its status.

### What is the Main Payment Network?

The Main Payment Network (MPN) is a special, builtin smart-contract that is
created in the genesis-block of the Zeeka Protocol. It manages a merkle-tree of
millions of accounts that can transfer ℤ with each other with nearly zero-cost
transactions. It uses the Groth16 proving system and has a fixed number of
transaction slots per update.

<p align="center">
    <img width="400" src="https://user-images.githubusercontent.com/4275654/188954000-450b32ad-c5e8-4714-9664-3afa40400508.png" alt="Deposit/Withdraw/Rsend/Zsend">
</p>

People who want to transfer their Zeeka tokens cheaply, would need to deposit
their funds to MPN through the `deposit` command.

### How to mine ℤeeka?

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
   in the updated software. So you need to start fresh. Remove the `~/.bazuka-chaos`
   folder by running: `rm -rf ~/.bazuka-chaos`

   Now run Bazuka again.

2. Install the MPN executor (`zoro`)

   ```
   git clone https://github.com/zeeka-network/zoro
   cd zoro
   cargo install --path .
   ```

3. Download the proving parameters

   - Payment parameters (~700MB): https://drive.google.com/file/d/1slabmFFr0Ct6fef09GOGHGqxYwB7YUMG/view?usp=sharing
   - Update parameters (~6GB): https://drive.google.com/file/d/1iVD2bpywWGHLB4cgasuhTEZwXQoGa2bj/view?usp=sharing

   Or if you want to download them through command-line:

   ```
   wget --load-cookies /tmp/cookies.txt "https://docs.google.com/uc?export=download&confirm=$(wget --quiet --save-cookies /tmp/cookies.txt --keep-session-cookies --no-check-certificate 'https://docs.google.com/uc?export=download&id=1slabmFFr0Ct6fef09GOGHGqxYwB7YUMG' -O- | sed -rn 's/.*confirm=([0-9A-Za-z_]+).*/\1\n/p')&id=1slabmFFr0Ct6fef09GOGHGqxYwB7YUMG" -O payment_params.dat && rm -rf /tmp/cookies.txt
   ```

   ```
   wget --load-cookies /tmp/cookies.txt "https://docs.google.com/uc?export=download&confirm=$(wget --quiet --save-cookies /tmp/cookies.txt --keep-session-cookies --no-check-certificate 'https://docs.google.com/uc?export=download&id=1iVD2bpywWGHLB4cgasuhTEZwXQoGa2bj' -O- | sed -rn 's/.*confirm=([0-9A-Za-z_]+).*/\1\n/p')&id=1iVD2bpywWGHLB4cgasuhTEZwXQoGa2bj" -O update_params.dat && rm -rf /tmp/cookies.txt
   ```

4. Run `zoro` beside your node

   ```sh
   zoro --node 127.0.0.1:8765 --seed [seed phrase for the executor account] --network chaos \
     --update-circuit-params [path to update_params.dat] --payment-circuit-params [path to payment_params.dat] \
     --db [absolute path to ~/.bazuka-chaos]
   ```

   (Note: The seed phrase for the executor account needs to be different from the
   seed you use for your node!)

5. After a new block is generated, the `uzi-miner` should start working on the PoW
  puzzle, so you will also need to have `uzi-miner` running on your system:

   ```
   git clone https://github.com/zeeka-network/uzi-miner
   cd uzi-miner
   cargo install --path .
   uzi-miner --node 127.0.0.1:8765 --threads 32
   ```

   (Note: Change number of `--threads` based on the spec of your system)
