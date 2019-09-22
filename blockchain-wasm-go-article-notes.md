# Part I
## Introduction
### Blockchain
- Blockchain is a technology with countless applications, great potential that is not yet fully utilized. 
- It is an ordered list of blocks that can be seen as a chain of blocks, hence came the name blockchain, from which blocks are added by consensus of the nodes building that blockchain.
- It can be looked at as a distributed database run by a swarm of nodes in which it provides consensus algorithms to allow adding entries to that database in a secure and non-repudiable manner so that entries are ensured to be valid.
- One method to provide achieve is providing Proof of Work (PoW).
- ~Explain mining~
- more on blockchains later in part 2 of this tutorial.
### Golang
- Golang shows up as a worthy candidate programming language, suited to run a blockchain across nodes implemented in a Decentralized Application (DApp). 
- Its performance metrics are comparable to high performing languages like C, C++ and Rust but Go being an easy to learn and to use without sacrificing performance sets it up an edge over them(at least for this article).
### Webassembly
- In order to run this DApp written in Go you need to compile it as a console application but this would be the case without webassembly (luckily we have it). Webassembly is a compilation target for this code to enable us run the DApp in a web browser and feel natural while interacting with it.
## What is in this article
### what does this article do
- This article walks you through the code for a blockchain DApp over websocket.
### Program structure
- The program is structured according to the figure below.
 - web: UI components and event callbacks provided for user.
 - wallet: Abstracting blockchain operations to very basic buy and reward operations.
 - blockchain: Blockchain operations
 - chainfabric: Underlying network
### Usecase scenario
- There are two users Alice and Bob, each one does the following:
 - Suppose Alice opens the browser and navigate to the application.
 - First she identifies herself by picking "Alice" from a dropdown menu.
 - She is seeing a window in which she can undergo operations with currency, let us call this unit currency crypsys.
 - Then she clicks "Reward" which in turn her balance adds up 10 more crypsys.
 - Once she is having sufficient amount of crypsys, she buys apples from Bob. 
 - The blockchain is shown as a sequence of blocks on the right.
 - Each operation reward/buy done, the block gets update.
 - On the other hand Bob is following similar steps as Alice and 
 - He shares the same blockchain view that she is viewing.
## Code
### index.html
- As for that the process runs in a browser it requires an index.html file from which the application starts.
- The head of the index file includes two scripts at lines ... which are essential to run the go program as a wasm on the browser.
- The wasm_exec.js is included as is since there is no need to change that file while the script included in line ... fetches the main.wasm compiled from the main.go program to run it. 
- The jquery source included on line ... is not essential for webassembly but for the sake of easier life I included it. This also shows that we can definitely use js frameworks side by side with webassembly.
- From line ... to ... I am laying out the componenets of the UI within the <body> selector like we do with plain html.
- The js functions registered on the html elements on lines ... are implemented in web/actions.go.
- NB: This is the "bridge" that links operations written in go and its execution within the browser via webassembly.
- The last script on line ... describes the behaviour of the slider and the js callback is also implemented in web/actions.go.

### actions.go
- In this code all the js functions called in index.html are implemented.
- All the functions in go have the same signature to map to their respective js functions in index.html regardless of the arguments or return type of the later.
- In lines # I create a function to map the js functions into the go functions implemented in this file.
- It goes as follow: in line # the first arguemnt of Set(...) points at the js function named reward.
 - While the second arguemnt is constructing a js function from the go function reward declared in line #.
- The changeUser() first checks if the choice coming from the dropdown is either Alice or Bob.
 - Then it enables the Buy button in line # by setting the disable attribute to false.
 - Similarily it toggles the disable attribute for the dropdown options to true.
 - Since user is not allowed to changed once being set.
 - The User varibale is set to the value coming from the dropdown.
 - The User varibale is checked by GetWallet() which sets the owner of the wallet to that User.
 - Also setting the difficulty of the blockchain mining process to diff.
 - In lines #, the Buy button is being customized according to which user have been picked.
 - For instance if the User is Alice, then Buy button's action is buy apples for price of 20 crypsys.
- The buyCommodity() invokes the wallet to pay the price of the commodity to the merchant.
 - Which both have been set priorly in the changeUser() function call.
 - It also alerts the user if the amount s/he has of crypsys are not sufficient.
- networth() invokes the wallet to check the amount of crypsys the wallet's owner is having.
 - It returns the value of networth as a js Value which is then viewed in index.html.
- reward() grants 10 crypsys to the user out of thin air, that is not how things work in real world.
 - But it is a way to inject crypsys to this scenario.
- sendSliderValToWasm() passes the value of the slider in index.html unto diff variable in package web in go.
 - Which in turns sets difficulty in the wallet to be then considered by the underlying blockchain. 
### renderer.go
- RenderPage() makes the page dynamic by reloading page elements according to changes.
- First I get the user's wallet in order to get the blockchain and the networth balance of the user later.
- In line # chainView is a div element including other dom elements needed to draw the wallet's blockchain.
- In line # chainView is appended into the blockchain-placeholder in index.html.
- The networth label of index.html is also updated in lines #.
### wallet.go
- Wallet struct is a wrapper around the blockchain operations as it mainly abstract the blockchain transactions.
- It belongs to the blockchain package.
- In line # NotifyListeners is a callback which is called right after any mutation to the blockchain.
 - which in turn requires a change in the user's UI.
- The struct comprises the username and a pointer to the Blockchain in which it wraps.- A Wallet instance is created by calling (constructor-like) function NewWallet(...).
 - since golang does not provide a dedicated constructor syntax for its structs. 
 - we usually resort to that way to implement it.
- Networth() traverses the blockchain to find the username of the wallet in the transactions.
 - and calculate his networth by subtracting the transactions initiated from her.
 - from the transactions forwarded to her.
- Functions PayTo and Reward are wrappers around the blockchain's RequestTransaction.
 - which initiates the process of creating a new block with the corresponding transaction.
 - Reward intiates the transaction from username "Coinbase". 
 - For the sake of this tutorial this user reflects the extra crypys added into the "cryrpsy-economy"
 - hence it can provide endless amount of crypsys if it needs to.
 - which is not how it should be implemented in a real blockchain scenario.
 - PayTo checks that user possesses adequate amount of crypsys before invoking the transaction. 
### main.go
- In this example main.go is the compile target.
- Being the entry-point to the application RegisterCallbacks is invoked from package web.
 - from which user actions are listened to in order to respond by the assigned callbacks.
- A WaitGroup variable is declared, before invoking RegisterCallbacks Add(1) is invoked on the WaitGroup.
 - hence when Wait is called on that WaitGroup the application does not terminate.
 - so it can wait and react to user's inputs accordingly.
## Let's run it
- compile it to wasm
- node tracker.js
- duplicate folder
- http-server on both then open browser and write address:port
- pick Alice, pick Bob, now both wallets are initiated.
- reward alice
 - new block is being created
 - mining is taking time
 - this mining problem is solved by Alice's wallet
 - a block is created showing the transaction, her networth increases by 10.
 - Bob also share the same blockchain despite he's not involved in transactions
 - another reward then let her buy apples from Bob
 - new block created.
 - Bob's apples cost Alice 20 crypsys that leaves her with 0 networth while Bob is having 20 networth.



# Part II
## Introduction
- In this article I aim at making an attempt to demystify blockchain.
 - by presenting an implementation using Go programming language.
- In part 1 of this article, the application has been presented as a scenario between two actors Alice and Bob.

## What is in this article
### Revisit program structure
- Repost drawing again
### What you learn
- understand the inner workings of blockchain.
- How to create a blockchain in Go. 
- Consider the distributed aspect of the app carrying out the blockchain operations.
 
## Explain Blockchain
### Distributed Database
### Proof of Work
- A hard problem enough so that one node can not consistently solve it.
 - Hence a proof for group consensus.
 - Create an example with probablity to show that idea (diagram).
- ~explain difficulty
#### Mining
- Nodes loop through nonce values until hash is valid (diagram).
- In this example I implemented the nonce values I loop through are not incremental.
 - This to allow different nodes in the network to go through different calculations.
- An example of a block hash,
 - Little endian vs big endian
- This is a 4 byte hash representing in memory in the figure below.
 - in the figure the memory addressing increases from top to bottom of table.
 - this is how it gets represented finally as an array.
 - > if we want to enforce the leading zeros condition upon AB0300
  - it is first converted to 0003AB
 - There can be no 4 byte hash in a real life scenario,
 - but this conveys the idea of its representation in memory as it generalizes to the 64 byte hash.

## Code
### Transaction
- First I define what a transaction is, 
 - in a real blockchain application transaction most proabably is required to contain more attributes.
 - but the bare minimum which I choose to present is to have the sender (From) of the transaction.
 - the reciever (To) and the amount of crypsys (Amount) being transferred.
- I also allow the transaction to be represented in json format since it being propagated through network.
### Blocks
#### doHash
- I create a struct for the part of the block to be hashed.
 - which is basically all the attributes of the block except for the hash.
- In lines # , these fields are filled then converted to a string.
- Then in line # the string is hashed and copied into the hash attribute of the block instance.
#### hashValid
- Checking the validity of the hash is simply checking the number of leading zeros.
- Considering the endiannes things shall require a bit more work than just counting zeros.
- I start by taking  the last 8 bytes of the hash array that is under check.
 - taking only those 8 bytes is not functionally wrong as long as the difficulty is no more than 64.
 - which is 8*8 bits.
- In line # I shift the last byte of the array to take the place of the most significant byte.
 - of a new uint64 number (hashAsInt).
 - while the first byte of array forms the least significant byte of the new number.
 - the rest of array bytes forms the remaining bytes of hashAsInt accordingly in that order.
 - after finishing that for loop in lines #, I am retreiving back the most significant 64-bits of the hash.
 - before it getting represented in little-endian representation in the array.
- Hence now we can check it according to difficulty in lines #.
#### mine
### Blockchain
### Networking
