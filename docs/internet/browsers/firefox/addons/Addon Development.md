# Add-ons

Background scripts - Can access all WebExtension APIs but not the DOM / page content

Content scripts - Can access all DOM / page content but not all WebExtension APIs

```js
function logTabs(tabs) {
  console.log(tabs)
}

browser.tabs.query({currentWindow: true}, logTabs)
```

`browser` is the namespace for WebExtension APIs (`chrome` in chrome land).

## Content scripts

### Xray vision in content scripts

Content script only see the pristine state of objects, and nothing that was added by any lesser privileged script. For example, if the page loads the following javascript

```js
var foo = 5;
```

Then in content script, trying to access the global foo object will result in undefined being returned

```js
console.log(foo); // undefined
```

### Content script accessible WebExtension APIs
[https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Content_scripts#webextension_apis](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Content_scripts#webextension_apis)


### Communicating between **background script** and **content script**
- One off messages using the `browser.runtime.sendMessage` (CS to BS) and `browser.runtime.onMessage` (CS and BS) APIs. To send a message from BS to CS, use `browser.tabs.sendMessage(tabId, message)` API
- Connection based using `browser.runtime.connect` => `postMessage` and `browser.runtime.onConnect` => `onMessage` APIs

### Communicating between content script and web page
Due to Xray, content scripts cannot view the new objects created by the page. But the page can send data using the `window.postMessage` and `window.addEventListener` APIs.

> [!WARNING]
> Data coming from the webpage can be maliciously crafted (eg. user has visited a malicious website that's targeting users who've installed the addon). Never pass the data directly to `eval()` or such functions

> [!INFO]
`eval()` is unavailable in manifest v3

## Background scripts (BGS)
Allows devs to react to events happening browser wide, like navigation to a new tab, bookmark, closing a tab etc.

They can be
- **Persistent**: Starts when extension starts and only stops when extension disabled / removed
- **Non-persistent**: Loaded as a response to an event and unloaded after

Remember
- BGS run in a special page's context and have access to a DOM and all APIs.
- They may use any WebExtension API. 
- They can access web content through the content script that they can inject into the page
- They can make cross origin requests
- They're bound by the CSP
- BGS unload after a few seconds of inactivity

APIs of interest
- storage - store state of the extension when idling
- alarms - timeouts don't work in non-persistent BGS, so use alarms to get a callback after a specific interval of time

### Downloads

`browser.downloads.download({ url: '<url>'})` API can be used to download a file to user's default download location.

### Native messaging

manifest.json
```json
  "browser_specific_settings": {
    "gecko": {
      "id": "ping_pong@example.org",
      "strict_min_version": "50.0"
    }
  },
```

app.json
```json
{
  "name": "ping_pong",
  "description": "Example host for native messaging",
  "path": "/path/to/native-messaging/app/ping_pong.py",
  "type": "stdio",
  "allowed_extensions": [ "ping_pong@example.org" ]
}
```

Note that the `browser_specific_settings.gecko.id` matches the `allowed_extensions`

### Addons user interface
- [MDN docs for addon user interfaces](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/user_interface)

Types of user interfaces supported in Firefox
1. Toolbar button (AKA browser action)
2. Toolbar button with popup (lastpass)
3. Address bar button (trello add to board button) (page action)
4. Address bar button with popup (containers)
5. Context menu (right click on page) (translate)
6. Sidebar (tree style tabs)
7. Options page (preferences of extensions)
8. Extensions pages (lastpass vault)
9. Address bar suggestions
10. Dev tools panel (React dev tools)

### Links
- [Web extensions polyfill github repo](https://github.com/mozilla/webextension-polyfill)
- [chrome incompatibilities with webextension standard](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Chrome_incompatibilities)
- [web extensions examples repository](https://github.com/mdn/webextensions-examples)

### `web-ext` cli tool
Install for handly functions when developing addons `npm install -g web-ext`

`web-ext lint` to do a dry run lint
`web-ext run` inside the extension directory - install the addon in a new firefox instance for testing
`web-ext run --firefox=/path/to/firefox-bin` to specify which firefox to use
