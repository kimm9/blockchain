# Build Your First Hello World Voting Ethereum Dapp Lesson

---

Agenda: 

1. Setting up development enviornment

2. Learn how to write, compile, and deploy a contract in your development environment

3. Use Node.js console to interact with the contract on the blockchain

4. Create a simple web page to interact with that contract and display the vote counts and vote for candidates through the page

---

# Setting up Development environment

For this lesson, we will be using an in-memory blockchain called ganache (a blockchain simulator). On the second lesson, we will interact with the real blockchain.

### On your terminal
- Install npm:  `npm install npm@4.0.5`
- Install node: `brew install node`
- Check if npm is installed: `npm -v` 
- Check if node is installed: `node -v`
- Make a directory and go into it: `mkdir hello_world_voting && cd hello_world_voting`
- Make a directory: `mkdir node_modules`
- Install ganache client: `npm install ganache-cli web3@0.20.2`
- Run ganache client: `node_modules/.bin/ganache-cli`

```
hello_world_voting $node_modules/.bin/ganache-cli
Ganache CLI v6.1.0 (ganache-core: 2.1.0)

Available Accounts
==================
(0) 0xe5574da4e9ec524ff8d230cb1f0130dd96296b10
(1) 0xf3a4a1e7a48f7165c0b6dd8598fb0d3da84b1c88
(2) 0xe04ceb70c0b63de60cf49453cbe2e1a2d6f0135d
(3) 0x087465e4dd179a3cb115e192714952535257fd47
(4) 0x5b62f3433eb5c2deca8a5a66ea10a0df9fa60058
(5) 0x0ef59d30f19ec005974341c6cea81e3df186561d
(6) 0xc26e6c5ba13c0be176fe944bf30bd9171f8e5407
(7) 0xd8465c2957b4af574bf827ee5fd92a6c6eb91286
(8) 0x49a123e32484b0148233ae1f61c7f92fc8657837
(9) 0x72844cb7c7c4eca325e0f799844a09d515a417c6

Private Keys
==================
(0) 2b2b2604be447f4382c42da4871923c1e08e3fe186afba2003e0914eb58032c5
(1) 142c298fa8f5532ec172c32c8373b91671561cdcff93e304494f098607cae833
(2) ebc32abb182a54f6086bb44a82d8a6ae1e56bedec4a8b3dc8d0c76a6c33c66e0
(3) f95581521a8f720cda63a51ac269f88bc2fd4f73f14f42968876ec3cd377c573
(4) fd207ec67d9e4a50634bbdc5f6ec1281970cf756d938e8f8c9cd6496b2e8a015
(5) 1be2ba07d7babb378dbc175600ec6ddc9b268e99b8ab0c2f19eee9a53ee8cb0b
(6) 6db2022ea869370fadd2a832b2dd84f69820d75d619721ce8aac69dbf9a00a5a
(7) 613fe36fb86da5af6914eea35d6709604b0a1049f533e27fcdd89fbddc787d8d
(8) d2474118ce97887b0ff92cf2292d882bd05e722a9a2b7f28ea2c414ce4a5685d
(9) 74b21b4a99120463d9abe4369c5f6cf4c03331791eb602b12f9a1c861c1ef156

HD Wallet
==================
Mnemonic:      wreck soap until average exact supreme benefit slice few subway grit fantasy
Base HD Path:  m/44'/60'/0'/0/{account_index}

Listening on localhost:8545

```

With ganache-cli, you have 10 test accounts to play with and they are loaded with fake 100 ethers. 

---

# Simple Voting contract

Now for this step, we are going to use Solidity (programming language) to write our contract. If you have a experience with object-oriented programming, learning Solidity contracts should not be as intense as it seems. First, we are going to write a contract called Voting, which is a like a class from an Object Oriented Programming language. This contract includes a constructor that initialized an array of candidates. Next, we will write 2 methods. One that will return total votes a candidate has received and another method to increment vote count for a candidate. 

Things To Consider: A constructor is called only once when a contract is deployed to the blockchain. If web world overwrites an old code to a new code when it is deployed, old code in blockchain is immutable. When a contract is deployed again in the blockchain, old contract will still be in blockchain along with all the data stored in it.

### Creating a Contract
- Create a file: `Voting.sol`
- Within the file, we will specify which version of compiler the code will compile with. Add this to the top of this file.

```
pragma solidity ^0.4.18;
```

- Next, we will write our contract and add mapping field. This mapping field is similar to an associative array or hash. The main point in mapping is candidate name is stored as type byte32 and value is an unsigned integer to store the vote count 
```
contract Voting{

  mapping (bytes32 => uint8) public votesReceived;

}
```

- Since solidity does not let you pass in an array of strings in the constructor, we will use an array of bytes32 to store the list of candidates

```
contract Voting{

  mapping (bytes32 => uint8) public votesReceived;

  bytes32[] public candidateList;

}
```

- Now, we will create a constructor that will be called when you deploy this contract to the blockchain. When a contract is deployed, we will pass an array of candidates who will be contesting in the election. 

```
contract Voting{

  mapping (bytes32 => uint8) public votesReceived;

  bytes32[] public candidateList;

  function Voting(bytes32[] candidateNames) public {
    candidateList = candidateNames;
  }

}
```

- We are also going to add a function that will return the total amount of votes a candidate received and another function that will increment the vote count for the specified candidate. This is to cast a vote. 

```
contract Voting{

  mapping (bytes32 => uint8) public votesReceived;

  bytes32[] public candidateList;

  function Voting(bytes32[] candidateNames) public {
    candidateList = candidateNames;
  }

  function totalVotesFor(bytes32 candidate) view public returns (uint8) {
    require(validCandidate(candidate));
    return votesReceived[candidate];
  }

  function voteForCandidate(bytes32 candidate) public {
    require(validCandidate(candidate));
    votesReceived[candidate] += 1;
  }

}
```

- Lastly, we will add a function that will checks if the candidates are valid and are on the list of candidates 

```
contract Voting{

  mapping (bytes32 => uint8) public votesReceived;

  bytes32[] public candidateList;

  function Voting(bytes32[] candidateNames) public {
    candidateList = candidateNames;
  }

  function totalVotesFor(bytes32 candidate) view public returns (uint8) {
    require(validCandidate(candidate));
    return votesReceived[candidate];
  }

  function voteForCandidate(bytes32 candidate) public {
    require(validCandidate(candidate));
    votesReceived[candidate] += 1;
  }

  function validCandidate(bytes32 candidate) view public returns (bool) {
    for(uint i = 0; i < candidateList.length; i++) {
      if (candidateList[i] == candidate) {
        return true;
      }
    }
    return false;
  }

}
```

### Compile and Deploy the contract to ganache blockchain

- We will install npm module solc to compile our solidity code: `npm install solc`

  We are going to use web3js library to interact with the blockchain through rpc

- Open another terminal window. Within the same directory install: `npm install ethereumjs-testrpc web3@0.20.1`
- Run testrpc and go back to the original window: `node_modules/.bin/testrpc`
- Run `node` and initialize the solc and web3 objects: 

```
> Web3 = require('web3')

> web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
```
- To be sure that web3 object is inistialized and is able to communicate with the blockchain, we will query all the accounts to the blockchain:

```
> web3.eth.accounts

[ '0x16fa44948ebcc6574439e506d87a9e997df4fa04',
  '0x8cf9b2ecc777c1f0d67fdaaee9936488a95e2afe',
  '0x98f09d41a848147fde90d4b66002ee6a3186ac09',
  '0xaf4ac9b719bd61e5e7f70ffa30893edead72be71',
  '0xf6c84e691afed6c8b0ae612892e5116cb0ced293',
  '0x25af9bdb07d2d187937d76b69bb657f8dd104288',
  '0xff46937da7a34ea2a984bcd0e9fa1c694803a6ca',
  '0x378638230fcf5f60fbbe0e6cdfd53eec5cc05571',
  '0x50d6e01969128dcd565071a38d7db26e76b4c4c9',
  '0xa283934a4663df7cbe146aef4c3234484dc1fc16' ]

```

- We will compile the contract by loading the code from Voting.sol into a string variable and compile it.

```
> code = fs.readFileSync('Voting.sol').toString()
> solc = require('solc')
> compiledCode = solc.compile(code)
```

Upon successfully compiling the contract, print compiledCode and note 2 very important fields here. 

* compiledCode.contracts[':Voting'].bytecode: When Voting.sol is compiled, you will get this bytecode. This is the code that will be deployed to the blockchain

* compiledCode.contracts[':Voting'].interface: This interface of the contract (abi) shows that the contract user what kind of methods are available in the contract. In the future, you will use this when you would have to interact with the contract. 

- We will now deploy the contract by creating a contract object. This object will be used to deploy and initiate contracts in the blockchain: 

```
> abiDefinition = JSON.parse(compiledCode.contracts[':Voting'].interface)
> VotingContract = web3.eth.contract(abiDefinition)
> byteCode = compiledCode.contracts[':Voting'].bytecode
> deployedContract = VotingContract.new(['David','Matt','Jane'],{data: byteCode, from: web3.eth.accounts[0], gas: 4700000})
> deployedContract.address
> contractInstance = VotingContract.at(deployedContract.address)
```

It is important to note that VotingContract above deployes the contract to the blockchain. First hash is an array of candidates who are competing in the election. Let's examine the second hash:

* data: Compiled bytecode that is deployed to the blockchain

* from: Blockchain needs to keep track of who deployed the contract and, in this case, the first account we get from calling web3.eth.accounts will be the owner of this contract. Note that web3.eth.accounts return 10 test accounts ganache created when we used test blockchain. In live blockchain, you won't be able to just use any account. It requires you to own an actual account and unlock it before transacting. To prove your ownership in of an account, you will be asked for a passphrase. Luckily, ganache by default provides 10 unlocked accounts for convenience. 

* gas: To actually interact with blockchain, you will need money. This money goes to miners who do all the work to include your code in the blockchain. To include your code in the blockchain, you will have to specify how much money you are willing to put down and you would do this by setting the value of 'gas'. The ether balance in your 'from' accounts is used to buy gas and its price is set by the network. 

Since we have now deployed the contract and have an instance of the contract that allows us to interact with the contract, it is crucial to know how to identify your contract. For this example, you can get your contact address with `deployedContract.address`. In the future, you will need to interact with your contract and you will need this deployed address and abi definition.

---

# Interact with the contract in nodejs console

- Let's see how many votes Matt currently have: 
```
contractInstance.totalVotesFor.call('Matt')
{ [String: '0'] s: 1, e: 0, c: [ 0 ] }
```

- Now let's vote for Matt 4 times:
```
> contractInstance.voteForCandidate('Matt', {from: web3.eth.accounts[0]})
'0x9c4d185acb811214bdb93c67f8814ac9f1ecd752c2a175b4e69ea6bc7ac76291'

> contractInstance.voteForCandidate('Matt', {from: web3.eth.accounts[0]})
'0x0fb25a359ef2d1a7178f4da28dbf65885e239d1f9a81cae68e128641cf422ece'

> contractInstance.voteForCandidate('Matt', {from: web3.eth.accounts[0]})
'0xbad842ad1b934049522a02fdd3e3ca261645adb7c123a5caad9d09dc29fe4021'

> contractInstance.voteForCandidate('Matt', {from: web3.eth.accounts[0]})
'0x82b781c024e0460513f111143619fd0d30dfc3e9c4260f77db566d4f71bfb520'
```

- Check how many votes Matt earned: 
```
> contractInstance.totalVotesFor.call('Matt').toLocaleString()
'4'
```

With this exercise, we can note that every time we vote, vote count increment and gives us back a transaction id: `'0x9c4d185acb811214bdb93c67f8814ac9f1ecd752c2a175b4e69ea6bc7ac76291'`. This id is proof that vote or the transaction has occurred. We can refer it back to this at any time in the future. It is very important to remember that this transaction is now immutable. Not being able to modify this transaction is one of the biggest advantages of blockchains. 

---

# Create a simple Webpage to connect it to Blockchain and Vote

All the major work is now done. A task that is left for us to do is to create an HTML file with candidate names and call the voting commands(this has been tested in nodejs console) in a js file.

## HTML file:

- Create an HTML file on project directory: `touch index.html`
- We will be using materialize and google fonts for some style on our simple page. Add this to the head section of your HTML file: 
```
  <link href='https://fonts.googleapis.com/css?family=Open+Sans:400,700' rel='stylesheet' type='text/css'>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/materialize/0.100.2/css/materialize.min.css">
```

- We will also need to connect web3js CDN, jquery, and our js file to this simple page. Add this right after the closing body tag:

```
  <script src="https://cdn.rawgit.com/ethereum/web3.js/develop/dist/web3.js"></script>
  <script src="https://code.jquery.com/jquery-3.1.1.slim.min.js"></script>
  <script src="/index.js"></script>
```

- Now we are going to create a simple Table and add an input field for us to vote for candiates:
```
<div class="container">
  <h3>Hello World Voting Application Demo</h3>
  <table>
    <thead>
      <tr>
        <th>Candidate</th>
        <th>Votes</th>
      </tr>
    </thead>

    <tbody>
      <tr>
        <td>David</td>
        <td id="candidate-1"></td>
      </tr>
      <tr>
        <td>Matt</td>
        <td id="candidate-2"></td>
      </tr>
      <tr>
        <td>Jane</td>
        <td id="candidate-3"></td>
      </tr>
    </tbody>
  </table>
  <hr>
  <input id="candidate" type="text" >
  <a class="btn" href="#" onclick="voteForCandidate()">Vote</a>
```

## JS file: 

- Create a variable name web3 and initialize Web3 objects
```
  web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
```

- Create a variable called abi and we set it to parse data: 
```
  abi = JSON.parse('[{"constant":false,"inputs":[{"name":"candidate","type":"bytes32"}],"name":"totalVotesFor","outputs":[{"name":"","type":"uint8"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"candidate","type":"bytes32"}],"name":"validCandidate","outputs":[{"name":"","type":"bool"}],"payable":false,"type":"function"},{"constant":true,"inputs":[{"name":"","type":"bytes32"}],"name":"votesReceived","outputs":[{"name":"","type":"uint8"}],"payable":false,"type":"function"},{"constant":true,"inputs":[{"name":"x","type":"bytes32"}],"name":"bytes32ToString","outputs":[{"name":"","type":"string"}],"payable":false,"type":"function"},{"constant":true,"inputs":[{"name":"","type":"uint256"}],"name":"candidateList","outputs":[{"name":"","type":"bytes32"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"candidate","type":"bytes32"}],"name":"voteForCandidate","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"contractOwner","outputs":[{"name":"","type":"address"}],"payable":false,"type":"function"},{"inputs":[{"name":"candidateNames","type":"bytes32[]"}],"payable":false,"type":"constructor"}]')
```

- Next we are going to create a VotingContract variable to create a contract object: 
```
VotingContract = web3.eth.contract(abi);
```

- For this step, we would need to go to our nodejs console and execute contractInstance.address like before to get the address at which the contract was deployed. Change the address line below with your own address:

```
  contractInstance = VotingContract.at('0x921535bec44c28f06a2639b06761b621b4b6fe18');
```

- Next we wil set our candidates: 
```
candidates = {"David": "candidate_1", "Matt": "candidate_2", "Jane": "candidate_3"}
```

- Since we set all the variables needed, we will be creating a function that allows us to vote for candidates: 

```
  function voteForCandidate() {
    candidateName = $("#candidate").val();
    contractInstance.voteForCandidate(candidateName, {from: web3.eth.accounts[0]}, function() {
      let div_id = candidates[candidateName];
      $("#" + div_id).html(contractInstance.totalVotesFor.call(candidateName).toString());
    });
```

- Lastly, we are going to use jquery to display to total count of the vote for candidates:

```
$(document).ready(function() {
  candidateNames = Object.keys(candidates);
  for (var i = 0; i < candidateNames.length; i++) {
    let name = candidateNames[i];
    let val = contractInstance.totalVotesFor.call(name).toString()
    $("#" + candidates[name]).html(val);
  }
});

```Create a simple web page to interact with that contract and display the vote counts and vote for candidates through the page