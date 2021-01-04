---
template: post
title: 'Puppeteer, Cron Jobs, and You'
slug: run-puppeteer-cronjob
draft: false
date: 2021-01-02T02:18:54.839Z
description: >-
  A quick guide on how to run a puppeteer script in a cron job without doing a
  bunch of weird stuff with your node path.
category: Guides
tags:
  - Javascript
  - Node
---
This is a quick guide on running puppeteer scripts (or any node script really) in a cronjob easily. This may not be the best way, but it will get you where you need to go. 

I recently made a puppeteer script to scrape Grafana dashboards, derive calculations, and hit an endpoint with the resulting data. I wanted this script to run every weekday at the same time.

I immediately reached for a cron job. 

The cronjob immediately failed...

It turns out that opening `crontab -e` and creating a cron task to execute a node script doesn't work as expected. 

When you open your terminal and run `node <your script here>` you have access to a full shell environment. When cron runs a script, it does so without a full shell environment to inform it about where it is, and what paths are relative to each other.

This results in sneaky errors and failures. 

Stackoverflow will tell you to prepend your cronjob with the full path to node on your system (found by running `which node` in your terminal). This would look like:
`/Users/username/.nvm/versions/node/v12.18.0/bin/node <path to your node script here>`.

This actually works! For now. If you look closely at the code above, it includes `nvm` in the path. At work, we use at least three different node versions for different projects. I'm constantly switching my node versions with nvm.

If I were to set up my cronjob like this it would work for as long as I stayed on that node version. The second I were to run `nvm use <some node version>` the absolute path to node in the crontab would be wrong and the script would fail.

That's not sustainable. 

I then reached for [node-cron](https://www.npmjs.com/package/node-cron). Node-cron is a package that allows you to write a node job directly in your node script as opposed to crontab. 

This solves one problem - you have access to a full shell environment, solving the node pathing issue. 

It creates another problem though. For this to work as a regular crontab job would, the node script needs to be running...forever. Think of node-cron like a sophisticated setInterval - it needs to be in a process that is constantly running for it to work.

That's also not sustainable. What if you close your terminal? The job dies with it.

To solve this problem, I reached for [pm2](https://www.npmjs.com/package/pm2).   

pm2 is a sophisticated process manager for Node.js applications that allows you to keep processes alive forever. It even provides a nice GUI for the processes you have running (check out the link, it's pretty cool).

From here, things got very simple. I installed pm2, threw my script inside a node-cron function, and ran it with pm2.

The node script looked like this 
```js
cron.schedule('45 11 * * 1-5', () => {
  runScript();
});
```
and to kick off the process I ran
`pm2 start index.js` in the project directory. 

It worked! The job ran as expected. If my system ever restarts, the process will restart with it. If I ever want to end the process, it's a one liner. The `runScript()` function now consistently runs at 11:45 every weekday under any circumstance (as long as my laptop is on).

There you go - instead of fighting with crontab or launchd, etc, just use node-cron and pm2. You'll have your node script running in no time. 
