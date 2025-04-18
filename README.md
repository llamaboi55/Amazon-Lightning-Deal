```markdown
# Amazon Lightning‑Deal Notifier

> 📉 Poll Amazon product pages for price drops and lightning deals, with desktop notifications when your target is reached or a deal goes live.

---

## Features

- **Price‑drop alerts**  
  Set your desired price and get a notification as soon as the current price is at or below it.  
- **Lightning deal detection**  
  If the page shows a lightning‑deal timer, you’ll get an instant alert.  
- **Configurable polling**  
  Checks every 15 minutes (you can tweak the interval in the script).  
- **Persistent settings**  
  Your target price is remembered in `localStorage`.

---

## Installation

### Tampermonkey / Greasemonkey

1. Install [Tampermonkey](https://www.tampermonkey.net/) or [Greasemonkey](https://www.greasespot.net/).  
2. Click the extension’s icon → **Create a new script**.  
3. Replace all boilerplate with the code below, then **Save**:

   ```js
   // ==UserScript==
   // @name         Amazon Lightning‑Deal Notifier
   // @namespace    llamaboi55.amznotifier
   // @version      1.0
   // @description  Notify on price drops and lightning deals on Amazon
   // @match        https://*.amazon.*/*
   // @grant        GM_notification
   // ==/UserScript==

   (function() {
     'use strict';

     // How often to check, in minutes
     const POLL_INTERVAL_MIN = 15;

     // Prompt for target price once; store in localStorage
     let target = localStorage.getItem('amznTargetPrice');
     if (!target) {
       target = prompt('Enter your target price (e.g. 19.99):');
       if (target) {
         localStorage.setItem('amznTargetPrice', target);
       } else {
         return; // no target set
       }
     }
     const targetPrice = parseFloat(target);

     function checkDeal() {
       // 1) Price‑drop check
       const priceEl = document.querySelector('#priceblock_ourprice, #priceblock_dealprice, .priceToPay .a-offscreen');
       if (priceEl) {
         const priceText = priceEl.textContent.replace(/[^0-9.]/g, '');
         const price = parseFloat(priceText);
         if (price <= targetPrice) {
           GM_notification({
             title: 'Amazon Price Alert',
             text: `Price is now ${price} (target was ${targetPrice})`,
             timeout: 8000
           });
         }
       }

       // 2) Lightning deal detection
       const timer = document.querySelector('.dealTimer, #dealExpiryCountdown, .feature-badge-label');
       if (timer) {
         GM_notification({
           title: 'Amazon Lightning Deal',
           text: 'This item is currently in a Lightning Deal!',
           timeout: 8000
         });
       }
     }

     // Initial check
     checkDeal();

     // Re‑check on interval
     setInterval(checkDeal, POLL_INTERVAL_MIN * 60 * 1000);
   })();
   ```

4. Visit any Amazon product page (e.g. `https://www.amazon.com/dp/...`). It will ask for your target price once, then poll automatically.

---

## Donate

If this script helps you snag deals, please consider supporting development:

[Donate via PayPal](https://paypal.me/llamaboi55?country.x=US&locale.x=en_US)  
```
