# Introduction
## Golang
- Golang shows up as a worthy candidate programming language, suited to run a blockchain across nodes implemented in a Decentralized Application (DApp). 
- Its performance metrics are comparable to high performing languages like C, C++ and Rust but Go being an easy to learn and to use without sacrificing performance sets it up an edge over them(at least for this article).
## Webassembly
- In order to run this DApp written in Go you need to compile it as a console application but this would be the case without webassembly (luckily we have it). Webassembly is a compilation target for this code to enable us run the DApp in a web browser and feel natural while interacting with it.
## What is in this article
- This article walks you through the code for a blockchain DApp over websocket.
- The program is structured according to the figure below.
 - web: UI components and event callbacks provided for user.
 - wallet: Abstracting blockchain operations to very basic buy and reward operations.
 - blockchain: Blockchain operations
 - chainfabric: Underlying network 
