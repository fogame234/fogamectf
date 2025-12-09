---
layout: post
title: CTF 2 - All Stat-Bearers
ctf: Flare.io
date: 2025-12-01
tags: 
 - Flare.io
 - Cookie Manipulation 
 - Git
 - CTF
 - Writeup
---

Write up for Flare's CTF week 2 of 6

## <span class="heading-color">Roll for Initiave:</span> ##

Show your stats: <https://jimmyswebspace.net/>

**Hint:**  
> Align your stats to claim the prize.

---

## <span class="heading-color">Initial Recon:</span> ##

Upon first visiting the site I was presented with a character sheet for Sir Alaric. The respec button is disabled. 

<img src="/assets/images/initialstats.jpg" alt="Inital Stats" >

Wanting to start off on the right foot, I checked the standard /robots.txt to see if there was anything. Alas I came up empty handed. Was hoping for a quick win but was easily defeated. 

Reviewing the source code for the website I was presented with ``<script>`` brackets. Upon careful review of the script, I found some constant variables that seemed to yeild something 

```
const ABCDEFG = 73;
const DEFAULT_COOKIE ="MmsaHRtrc3B8ZWsNDBFrc31/ZWsKBgdrc355ZWsABx1rc315ZWseABprc3x8ZWsPBQgOa3N5NA==";
```
The default cookie was encoded in base64 but provided no readable output while using cyberchef to decode it. Continuing down the script there were a few scripts that would read the cookie. From my understanding of the JavaScript, it would encrypt the stats and then store them within the base64 encoded cookie string. In order to decrypt them it would use the ABCDEFG key. 

```
(c) => c.charCodeAt(0) ^ ABCDEFG
```
Once it was done decrpyting the cookie, it would parse it into a JSON file that could be read and display it on the site. 

---

## <span class="heading-color">Lets Get Scripty:</span> ##

My first thought was to go to cookie manipulation. How could I get the cookie to read 100s across all the stats and the flag. Forging a script with the low carbon steel that is my JavaScript skills, I reached deep and came up with this:

```
const ABCDEFG = 73;
const obj = {STR:100,DEX:100,CON:100,INT:100,WIS:100,FLAG:100};
const json = JSON.stringify(obj);
const encoded = btoa([...json].map(c => String.fromCharCode(c.charCodeAt(0) ^ ABCDEFG)).join(""));
document.cookie = `stats=${encoded}; path=/; SameSite=Lax`;
console.log("New cookie:", encoded);
```
**Script Explination:**
The above script does a few things. 
```
- Sets the XOR key to be 73, same as the original script
- Sets all the stats to be 100
    -Data that will be encoded and stored
- Converts the object to a JSON string
    - {"STR":100,"DEX":100,"CON":100,"INT":100,"WIS":100,"FLAG":100}
- XOR-encode every character
- Base64-encode the XOR string
- Store the encoded results in a cooke and set the cookie
- Finally print the cookie values
```
Once the script was ready, I fired up developer tools withing the browser and ran the script within the console. Upon script completion I refreshed the page and it presented me with the flag

<img src="/assets/images/statsflag.jpg" alt="Stats Flag" >