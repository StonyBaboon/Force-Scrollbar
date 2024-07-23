# Force Scrollbar 

This userscript ensures that a scrollbar is visible on websites that disable it. It checks for the presence of a scrollbar and enables one only if none exists, without interfering with sites that already have one, such as those with infinite scrolling (e.g., Facebook, YouTube).

## Description

Some websites disable scrolling to prevent users from bypassing certain content or advertisements. This script aims to counteract such behavior by forcing the scrollbar to be visible only when necessary. The script checks if a scrollbar is already present on the page and only adds one if it isn't. Additionally, it attempts to remove or hide overlays and fixed position elements that might block scrolling.

## Features

- **Detects Existing Scrollbar**: Checks if the page already has a scrollbar and avoids adding a new one if it does.
- **Enables Scrolling**: Overrides styles that hide the scrollbar and removes event listeners that prevent scrolling.
- **Hides Overlays**: Hides fixed position overlays that might block scrolling without removing essential site elements.
- **Dynamic Updates**: Re-applies the script whenever new elements are inserted into the DOM or when the window is resized.

## Installation

1. **Install a Userscript Manager**:
   - [Tampermonkey](https://www.tampermonkey.net/) 
   - [Greasemonkey](https://www.greasespot.net/)

2. **Create a New Script**:
   - Open your userscript manager and create a new script.
   - Copy and paste the following code into the script editor.

```javascript
// ==UserScript==
// @name         Force Scrollbar 
// @version      0.1
// @description  Force the scrollbar to be visible if none exists on sites that disable it
// @author       StonyBaboon
// @match        *://*/*
// @grant        none
// ==/UserScript==

(function() {
    'use strict';

    // Function to check if a scrollbar is present
    function isScrollbarPresent() {
        return document.documentElement.scrollHeight > document.documentElement.clientHeight;
    }

    // Function to enable scrolling if no scrollbar is present
    function enableScrolling() {
        // Check if a scrollbar is already present
        if (isScrollbarPresent()) {
            return;
        }

        // Override any styles that hide the scrollbar
        document.body.style.overflow = 'auto';
        document.documentElement.style.overflow = 'auto';

        // Remove event listeners that might block scrolling
        window.onscroll = null;
        document.onscroll = null;
        document.body.onscroll = null;
        document.documentElement.onscroll = null;

        // Additional step to remove any overflow hidden styles
        document.body.style.height = 'auto';
        document.documentElement.style.height = 'auto';

        // Remove any overlays or divs that might be blocking scrolling
        let overlays = document.querySelectorAll('div');
        overlays.forEach(div => {
            if (getComputedStyle(div).position === 'fixed' && getComputedStyle(div).zIndex > 1000) {
                div.style.display = 'none';
            }
        });
    }

    // Run the function initially and whenever the DOM changes
    enableScrolling();
    document.addEventListener('DOMNodeInserted', enableScrolling);
    window.addEventListener('resize', enableScrolling);
})();
