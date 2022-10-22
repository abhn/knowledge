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
- One off messages using the `browser.runtime.sendMessage` and `browser.runtime.onMessage` APIs
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