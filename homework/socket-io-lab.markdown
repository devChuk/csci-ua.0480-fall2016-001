---
layout: homework
title: CSCI-UA.0480 - Socket IO Lab
---

<div class="panel panel-default">
	<div class="panel-heading">Homework #8</div>
	<div class="panel-body" markdown="block">

# Socket IO Lab - Emoji Racer (10 points)

## Overview

### Goals / Topics Covered

You'll be using the following concepts:

* socket.io
* some simple dom manipulation
* absolute or fixed positioning

### Description

Make a real time web app that:

1. displays two emoji
2. displays two buttons
3. clicking on one button moves one emoji
4. everyone connected to the game can click either button
5. everyone connected to the game can see the emoji move in real time

![Emoji Racer](../resources/img/hw09-screen.gif)

### Submission Process

1. You can work as individuals or in groups
2. Submit your code through [this google form](https://docs.google.com/a/nyu.edu/forms/d/e/1FAIpQLSdmF51oafE7ehLw56EPxg5nV5YxizkJef-W6Loh59qncR2xWg/viewform)
    * if working in a group, you only need to submit one form
    * add the net ids of the students in the group in the form




## Instructions

### Setup

1. create a directory to store your project
2. create your `package.json` and install these packages:
	<pre><code data-trim contenteditable>
npm init
npm install --save express socket.io
</code></pre>
3. use this boilerplate code for the server (perhaps in server.js or app.js):
    <pre><code data-trim contenteditable>
var express = require('express');
var app = express();
var server = require('http').Server(app);
var io = require('socket.io')(server);
app.use(express.static('public'));
// server code goes here!
server.listen(3000);
</code></pre>
4. use this boilerplate code for the markup (in `public/index.html`):
	<pre><code data-trim contenteditable>
&lt;!DOCTYPE html&gt;
&lt;head&gt;
&lt;title&gt;&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;script src="/socket.io/socket.io.js"&gt;&lt;/script&gt;
&lt;script src="racer.js"&gt;&lt;/script&gt;
&lt;button class="player1Btn"&gt;Move Tears of Joy &amp;rarr;&lt;/button&gt;
&lt;div class="play-area"&gt;
  &lt;div class="racer player1"&gt;&amp;#128514;&lt;/div&gt;
  &lt;div class="racer player2"&gt;&amp;#128561;&lt;/div&gt;
&lt;/div&gt;
&lt;button class="player2Btn"&gt;Move Face Screaming &amp;rarr;&lt;/button&gt;
&lt;/body&gt;
&lt;/html&gt;
</code></pre>
5. use this boilerplate code for the client (in `public/racer.js`):
    <pre><code data-trim contenteditable>
    let socket = io();
</code></pre>

## Extra Credit (4 points)

### Determine How to Deploy on Hyper Dev!

1. [go to hyperdev.com](https://hyperdev.com/)
2. fill out package.json with socket.io and express requirements
3. add necessary files!
4. instantly deployed app!

## Major Hint

### Don't feel like dealing with css? You can use this:

<pre><code data-trim contenteditable>
&lt;style type="text/css" media="screen"&gt;

.racer {
  position: absolute;
  left: 0px;
  font-size: 100px;
}

.player1 {
  top: 50px;
}    

.player2 {
  top: 300px
}    

.play-area {
  position: relative;
  width: 800px;
  height: 500px;
  border-right: 3px dashed black;
}

button {
  font-size: 3em;
}
&lt;/style&gt;

</code></pre>






</div>
</div>
