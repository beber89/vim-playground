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
- Gain an insight over the inner workings of blockchain.
- How to create a blockchain in Go. 
- Consider the distributed aspect of the app carrying out the blockchain operations.
 
## Explain Blockchain
### Distributed Database
### Proof of Work
- A hard problem enough so that one node can not consistently solve it.
 - Hence a proof for group consensus.
 - Create an example with probablity to show that idea (diagram).
 - Suppose S: event that one node finds the nonce that solves the hash in a time not exceeding ten minutes.
  - assume for one node we have p(S): probability of event S = 1/1000
  - which is a very insignificant probability let us now define 
  - Sn: probability of at least one node solves the hash within the ten minutes in a pool of n number of nodes.
  - accordingly p(Sn) = 1-(1-p(S))^n.
  - p(S1000) = 0.6323, p(S10000) = 0.99995 
  - With a big enough number of contributing nodes, the value jumps significantly from 1/1000 to 0.6323 to nearly one!
 - The probability model that describes this scenario should be more complex than that.
 - But the point is to make sense of the fact that the likelihood of getting the block created.
 - becomes significant if many nodes exist in the network.
 - hence it prevents a malicious node from introducing fake transactions into the chain. 
- It is needed to have a level of control on the time in which the nodes take to create a new block.
 - A harder PoW in turn requires more time, hence we need to control the difficulty of PoW.
 - In this article difficulty diff is the number of leading zeros in which the hash is required to have.
  - for the block to be considered valid.  
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
 - which is 8\*8 bits .
- In line # I shift the last byte of the array to take the place of the most significant byte.
 - of a new uint64 number (hashAsInt).
 - while the first byte of array forms the least significant byte of the new number.
 - the rest of array bytes forms the remaining bytes of hashAsInt accordingly in that order.
 - after finishing that for loop in lines #, I am retreiving back the most significant 64-bits of the hash.
 - before it getting represented in little-endian representation in the array.
- Hence now we can check it according to difficulty in lines #.
#### mine
- One common thing with Bitcoin in this article is that PoW is provided by a mining process.
- This method mine() implements figure #. 
- In every iteration of the loop a new hash is extracted for the block by changing the nonce.
 - by invoking tryNonce() on the block.
 - as long as the hash is not valid, every 500 iterations the node checks if other node produced a valid hash.
 - invoking GetObserver() instigates the chainfabric
 - from which the node can find if there is a response pending from the network or not
 - if there is a response then it might be the nonce needed for producing the valid hash.
 - if the hash in that case found to be valid the node fires up an alert to the user that a nonce is mined by other node.
 - if not , mining continues until a valid hash is produced.
- > There is a subtelty in line # where this thread goes to sleep for 70 microseconds.
 - it is worth noting that WebAssembly does not support threading properly yet.
 - So by letting the thread to go to sleep, the networking chainfabric gets CPU time
  - to check if there is a response.  
### Blockchain
#### Struct & NewBlockchain() & listener
- In this file blockchain.go, the blockchain structure and methods are defined.
- For this application Blockchain struct contains only one attribute chain.
 - which is an array of Block.
- The first function NewBlockchain constructs an instance of Blockchain. 
 - It first invokes GetObserver().
 - This initializes the underlying networking operations.
 - By creating a new blockchain the first block is created rightaway.
 - In blockchain terms it is called genesis block and it does not contain any relevant transaction info.
  - but its creation is essential for other blocks to follow. 
- In line # I set a callback, this callback is later called by the underlying network.
 - when a message is received by the node notifying it that there is a new transaction requested.
 - The receiving node then might contribute by mining the transaction or ignore it and just add the new block.
 - in this article it is assumed that nodes mine always mine transaction when they are asked for it.
- In line # the listener converts the serialized message msg received into a predefined struct.
 - after that it invokes a new thread that starts mining to create a new block in line #
 - the transaction information of the block to be created is just extracted priorly from msg.
 - at the end the chain is mutated by adding that new block to it.
#### RequestTransaction
- This method insitigates new transactions, at first it sends a message to the network in line #.
 - to notify other nodes about a newly requested block that needs to be created.
- Then it tries to mine for the new block by itself. 
### Networking
#### struct & SetNewTransactionCallback 
- Node struct reflects the networking aspect of the blockchain as it comprises features of the network. 
- First are the destination ip address toIP and port toPort of the running server tracker.js which is written in nodejs.
- response attribute holds messages received from the tracker.
- ws points at the websocket connection.
- newTransactionCallback is called once the node received a request for a new transaction to be created.
#### Connect
- At this point I write the Connect procedure first to establish connection with tracker.
 - then to register event listeners.
- First I get the websocket object from the javascript global scope running on the web browser.
 - from this object New() is invoked passing the url to connect to as an argument.
 - in this case url is "ws://127.0.0.1:8081/ws"
- In line #, I register the necessary event listener that is called once a message is received.
 - The go func callback is wrapped inside a js.FuncOf() which converts go func into a function to be used by javascript.
 - This is also mentioned in part 1 of this article.
 - The message is received and parsed then it branches to two cases either it be a transaction request or be it a response.
  - In the first case the newTransactionCallback is invoked on the transaction, 
   - this in turn invokes the listener in blockchain.go.
  - response holds the value of a nonce during the mining process which solves the nonce to achieve the required hash.

