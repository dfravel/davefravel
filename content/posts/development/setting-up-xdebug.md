---
title: "Setting Up Xdebug"
description: "Setting up PHP Debug on macOS High Sierra for Laravel Valet and VS Code (or 'what to do when dd(), Log::info() and console.log() aren\'t enough')"
date: 2017-10-24T16:56:16-04:00
draft: false
tags: ["development"]
---

## Disclaimers and Prelude
My dev setup prior to implementing PHP Debug is the following:

- Laravel Valet
- PHP 7.0 (through homebrew)
- macOS High Sierra
- Visual Studio Code

I did **not** have PHP Xdebug installed, and while I had looked into it at one point, I did **not** have [Felix Becker's PHP Debug extension](https://github.com/felixfbecker/vscode-php-debug) installed. 

--- 

## Intro
The push that I needed to finally tackle proper debugging in Visual Studio Code came from a [blog post](https://tighten.co/blog/configure-vscode-to-debug-phpunit-tests-with-xdebug) and [podcast](https://overcast.fm/+I_P73-rgk) from the devs at [Tighten](https://tighten.co/). 

Prior to this, my Laravel debugging has been a series of dd() and Log::info() calls mixed in with an occasional var_dump() in a blade file. I imagine that I'm not alone with this style of debugging. This style was fine .. I guess. I was able to understand where most of the issues arose, but I missed the power of debugging in Visual Studio Professional. There's something magical about seeing the flow of data through the different function calls. Proper debugging allows you to truly understand what's happening to your data as opposed to the poop sandwich dropped in the browser through dd(). 

---

## Brew 
I made the decision to upgrade to PHP 7.1 while implementing debugging. I had been running PHP 7.0 without issue, but our Laravel Forge/Digital Ocean servers have all been upgraded to 7.1.

Make sure that brew is up to date
``` 
brew update
brew upgrade
``` 

Unlink PHP 7.0
```
brew unlink php70
```

Install PHP 7.1
```
brew install php71
```

After the successful install of PHP 7.1, you should see something like:
{{< figure src="/images/php-version.png"  >}}

___

## Install PHP Debug
```
brew install php71-xdebug
OR
brew install --build-from-source php71-xdebug
```
This was the first gotcha for me in the process. I initially ran **brew install php71-xdebug**, but hit some errors when trying to launch the debugger. After some additional research I found a stack overflow answer that helpfully suggested to include **--build-from-source** so I said ¯\\\_(ツ)\_/¯ sounds good to me.   

---

## Edit ext-xdebug.ini
```
sudo nano /usr/local/etc/php/7.1/conf.d/ext-xdebug.ini
```

Update the .ini file to have these values. It's possible that the zend_extension line will already be in there is you've selected the build-from-source option, but at a minimum, make sure these values are in the file.
```
[xdebug]
zend_extension="/usr/local/opt/php71-xdebug/xdebug.so"
xdebug.remote_enable = 1
xdebug.remote_autostart = 1
```

After installing php71-xdebug, your php -v should now look like this
{{< figure src="/images/with-xdebug.png"  >}}

---

## Install the PHP Debug Extension
In Visual Studio Code search for and install the PHP Debug Extension:
{{< figure src="/images/debug-extension.png"  >}} 

---

## Restart Laravel Valet. Reinstall Laravel Valet?
Because we've made so many changes to our PHP environment, a restart of Valet is required. I did this and got a nasty 502 Bad Gateway nginx error. After multiple attempts to tweak, restart, link, unlink .. etc I decided to reinstall Valet. That did the trick and my development domains came back up. 

---

## Start Debugging
If everything has worked, you should be able to go into one of your Controllers, set a breakpoint, go to the Debug menu, click "Listen for XDebug" and watch the magic happen
{{< figure src="/images/debugger.png"  >}} 


---

## Possible Gotcha(s)
In addition to the reinstall of Valet (which was probably due to my PHP upgrade) and the **--build-with-source** flag for the php71-xdebug install I ran into one other possible gotcha. One of the options with the PHP Debug extension is to set the localSourceRoot. During some of the initial "why the f* isn't this working?" troubleshooting I added this to the Listen for XDebug" configuration:

{{< figure src="/images/configuration.png"  >}} 

It seems to work fine with or without it, but if you find yourself unable to connect to the debugger that might be something to try.

---

