
# Level 2: Persistence is key

## Challenge Overview
- Level 2 introduces **Stored XSS** (also called **Persistent XSS**).
- The application is a simple chat/message board where users can post messages.
- Unlike Level 1 (where the **XSS** was in the **URL**), this vulnerability **stores malicious input in the application**.
- **Objective**: Inject JavaScript that triggers an **alert()** and persists across page reloads.

## The Vulnerability
The application allows users to post messages, but it doesn't properly sanitize the input before:
1. Storing it
2. Displaying it to other users
This means any **HTML/JavaScript** you submit will be **permanently stored and executed whenever anyone views the page**.

## Understanding the Application
When you look at the page source, you'll likely find something like:
```html
<td class="message-container" valign="top">
	<div class="shim"></div>
	<b>You</b>
	<span class="date">Tue Sep 30 2025 04:19:03 GMT+0100 (GMT+01:00)</span>
	<blockquote>
		[USER_INPUT_HERE]
	</blockquote>
</td>
```
The application takes your input and directly inserts it into the HTML.

## Solution

### Attempt 1: Basic Script Tag (Like Level 1)
```html
<script>alert()</script>
```
**This might NOT work!** Why? The application might filter `<script>` tags specifically.

### Attempt 2: HTML Event Handlers
If script tags are blocked, try:
```html
<img src=x onerror=alert()>
```
How it works:
- Creates an `<img>` tag with invalid source.
- When image fails to load, `onerror` event fires.
- Executes `alert()`
