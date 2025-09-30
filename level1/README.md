
# Level 1: Hello, world of XSS

## Challenge Overview
- Level 1 introduces the most fundamental type of Cross-Site Scripting vulnerability: **Reflected XSS**.
- The application is a simple search page where users can enter a search query. When you submit the query, the application displays a message showing what you searched for. If no results are found, it displays: "Sorry, no results were found for **[your search term]**".
- **Objective**: Inject JavaScript code that triggers an alert() dialog box.
- **URL Structure**: `https://xss-game.appspot.com/level1/frame?query=[YOUR_INPUT]`

## The Vulnerability
The application takes your search query from the URL parameter and **directly embeds it into the HTML page without proper sanitization or encoding**. This means any HTML or JavaScript code you include in the query will be interpreted and executed by the browser.

## Solution
To solve this level, you need to inject a `<script>` tag that will execute JavaScript code.
<br>
Here's how:
### Step 1: Understand the Attack Vector
The query parameter is directly reflected in the page. If you enter:
```
test
```
The page will display: "Sorry, no results were found for **test**"

### Step 2: Inject the Payload
Instead of a normal search term, inject a script tag:
```html
<script>alert()</script>
```

### Step 3: Complete URL
The full URL would be:
```html
https://xss-game.appspot.com/level1/frame?query=<script>alert()</script>
```
Or URL-encoded version:
```html
https://xss-game.appspot.com/level1/frame?query=%3Cscript%3Ealert%28%29%3C%2Fscript%3E
```
