
# Level 5: Breaking protocol

## Challenge Overview
- Level 5 explores **URL protocol manipulation** and **href attribute injection**.
- The application is a signup page that includes a "Next" or "Skip" link.
- The link URL is constructed using a query parameter.
- **Objective**: Inject JavaScript through URL manipulation using the `javascript:` protocol
- **URL Structure**: `https://xss-game.appspot.com/level5/frame?next=[URL]`

## The Vulnerability
The application likely has code like:
```html
<a href="{{ next }}">Next >></a>
```
The application takes a URL from the query parameter and uses it **directly in an `href` attribute without validation**.

## Understanding the javascript: Protocol
When you click a link with `javascript:` protocol, the browser executes the code instead of navigating:
```js
<!-- Normal link -->
<a href="https://google.com">Google</a>

<!-- JavaScript execution -->
<a href="javascript:alert()">Click me</a>
```
When clicked, the second link executes `alert()` instead of navigating anywhere.

## Solution
The Payload:
```
javascript:alert()
```
Full URL:
```
https://xss-game.appspot.com/level5/frame/signup?next=javascript:alert()
```
