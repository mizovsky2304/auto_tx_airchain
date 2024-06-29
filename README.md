<h1 align="center">Smart Automatic  Transactions Script [by_mizovsky2304]</h1>


### Install Dependecies

```
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip screen -y
```
```
sudo apt install -y curl git jq lz4 build-essential cmake perl automake autoconf libtool wget libssl-dev -y
```
```
sudo apt-get remove nodejs
```
```
sudo apt-get purge nodejs
sudo apt-get autoremove
```
```
sudo dpkg -i --force-overwrite /var/cache/apt/archives/nodejs_18.20.3-1nodesource1_amd64.deb
```
```
sudo rm /etc/apt/keyrings/nodesource.gpg
sudo rm /etc/apt/sources.list.d/nodesource.list
```
```
NODE_MAJOR=18
```
```
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
```
```
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
```
```
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_${NODE_MAJOR}.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list
```
```
sudo apt-get update
sudo apt-get install -y nodejs
```
```
node --version
```
```
npm install -g npm@10.8.1
```
```
npm install web3@1.5.3
```

### Config Script
```
nano smart_auto_tx.js
```
* Copy & Paste the entire code below in the file
* No need to change anything
```console
const readline = require('readline');
const Web3 = require('web3');

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

rl.question('Enter your private key: ', (privateKey) => {
  rl.question('Enter recipients address: ', (toAddress) => {
    rl.question('Enter range of amounts to transfer (e.g., 0.0001,0.002): ', (amountRange) => {
      rl.question('Enter range of intervals in seconds (e.g., 30,60): ', (intervalRange) => {

        const [minAmount, maxAmount] = amountRange.split(',').map(Number);
        const [minInterval, maxInterval] = intervalRange.split(',').map(Number);

        const rpcURL = "http://localhost:17545"; // or your server IP: "http://your-server-ip:17545"
        const web3 = new Web3(new Web3.providers.HttpProvider(rpcURL));

        function getRandomAmount(min, max) {
          return (Math.random() * (max - min) + min).toFixed(2);
        }

        function getRandomInterval(min, max) {
          return Math.floor(Math.random() * (max - min + 1) + min);
        }

        function sendTransaction() {
          const amount = getRandomAmount(minAmount, maxAmount);
          const interval = getRandomInterval(minInterval, maxInterval);

          const account = web3.eth.accounts.privateKeyToAccount(privateKey);
          web3.eth.accounts.wallet.add(account);
          web3.eth.defaultAccount = account.address;

          const tx = {
            from: web3.eth.defaultAccount,
            to: toAddress,
            value: web3.utils.toWei(amount, 'ether'),
            gas: 21000,
            gasPrice: web3.utils.toWei('1', 'gwei')
          };

          web3.eth.sendTransaction(tx)
            .then(receipt => {
              console.log(`Transaction successful with hash: ${receipt.transactionHash} | Amount sent: ${amount} ETH | Next transaction in: ${interval} seconds`);
            })
            .catch(err => {
              console.error('Error sending transaction:', err);
            });

          setTimeout(sendTransaction, interval * 1000);
        }

        sendTransaction();

        rl.close();
      });
    });
  });
});

```
> `Ctrl + X` `Y` `Enter`

### Run Script
```console
# Open screen
screen -S tx

# Run script
node smart_auto_tx.js
```
* Enter Private-key of your Evmos Station 
* Enter Receipt Address: a random 0x.. address
* Enter a range of Amount of the tokens you want to send: 0.0001,0.002
* Enter a range of delays between txs in seconds: 30,60

  ![image]([https://github.com/0xmoei/Airchain-testnet/assets/90371338/81f5220b-333a-4b1e-a2f5-0ca683d99590](https://i.ibb.co/nQ3HSGB/Capture-d-e-cran-2024-06-29-a-19-49-20.png))

#
