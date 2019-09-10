# Introduction
## Golang
- Golang shows up as a worthy candidate programming language, suited to run a blockchain across nodes implemented in a Decentralized Application (DApp). 
- Its performance metrics are comparable to high performing languages like C, C++ and Rust but Go being an easy to learn and to use without sacrificing performance sets it up an edge over them(at least for this article).
## Webassembly
- In order to run this DApp written in Go you need to compile it as a console application but this would be the case without webassembly (luckily we have it). Webassembly is a compilation target for this code to enable us run the DApp in a web browser and feel natural while interacting with it.
# What is in this article
- This article walks you through the code for a blockchain DApp over websocket.
- The program is structured according to the figure below.
 - web: UI components and event callbacks provided for user.
 - wallet: Abstracting blockchain operations to very basic buy and reward operations.
 - blockchain: Blockchain operations
 - chainfabric: Underlying network
# Code
## index.html
- As for that the process runs in a browser it requires an index.html file from which the application starts.
- The head of the index file includes two scripts at lines ... which are essential to run the go program as a wasm on the browser.
- The wasm_exec.js is included as is since there is no need to change that file while the script included in line ... fetches the main.wasm compiled from the main.go program to run it. 
- The jquery source included on line ... is not essential for webassembly but for the sake of easier life I included it. This also shows that we can definitely use js frameworks side by side with webassembly.
- From line ... to ... I am laying out the componenets of the UI within the <body> selector like we do with plain html.
- The js functions registered on the html elements on lines ... are implemented in web/actions.go.
- NB: This is the "bridge" that links operations written in go and its execution within the browser via webassembly.
- The last script on line ... describes the behaviour of the slider and the js callback is also implemented in web/actions.go.
