---
title: GM_* APIs
date: 2018-04-25 15:15:15
path: "/api/gm/"
---

`GM_*` are a group of special APIs provided by Violentmonkey.

<div class="toc">

* [GM_addStyle](#gm_addstyle)
* [GM_addValueChangeListener](#gm_addvaluechangelistener)
* [GM_deleteValue](#gm_deletevalue)
* [GM_download](#gm_download)
* [GM_getResourceText](#gm_getresourcetext)
* [GM_getResourceURL](#gm_getresourceurl)
* [GM_getValue](#gm_getvalue)
* [GM_info](#gm_info)
* [GM_listValues](#gm_listvalues)
* [GM_notification](#gm_notification)
* [GM_openInTab](#gm_openintab)
* [GM_registerMenuCommand](#gm_registermenucommand)
* [GM_removeValueChangeListener](#gm_removevaluechangelistener)
* [GM_setClipboard](#gm_setclipboard)
* [GM_setValue](#gm_setvalue)
* [GM_unregisterMenuCommand](#gm_unregistermenucommand)
* [GM_xmlhttpRequest](#gm_xmlhttprequest) - note `h` is lowercase (the historical spelling)

`GM` *(since VM2.12.0)* is a single variable with [Greasemonkey4-compatible](https://wiki.greasespot.net/Greasemonkey_Manual:API) aliases:

* [GM.deleteValue](#gm_deletevalue)
* [GM.getResourceURL](#gm_getresourceurl)
* [GM.getValue](#gm_getvalue)
* [GM.info](#gm_info)
* [GM.listValues](#gm_listvalues)
* [GM.notification](#gm_notification)
* [GM.openInTab](#gm_openintab)
* [GM.setClipboard](#gm_setclipboard)
* [GM.setValue](#gm_setvalue)
* [GM.xmlHttpRequest](#gm_xmlhttprequest) - note `H` is uppercase

</div>

### GM_info

An object that exposes information about the current userscript. It has following properties:

- `uuid` *string*

    A unique ID of the script.

- `scriptMetaStr` *string*

    The meta block of the script.

- `scriptWillUpdate` *boolean*

    Whether the script will be updated automatically.

- `scriptHandler` *string*

    The name of userscript manager, which should be the string `Violentmonkey`.

- `version` *string*

    Version of Violentmonkey.

- `script` *object*

    Contains structured fields from the [Metadata Block](../metadata-block/):

    - `description` *string*
    - `excludes` *array of string*
    - `includes` *array of string*
    - `matches` *array of string*
    - `name` *string*
    - `namespace` *string*
    - `resources` *array of {name, url}*
    - `runAt` *string*
    - `version` *string*

- `injectInto` *string* *(since VM2.10.0)*

    The injection mode of current script. See [`@inject-mode`](/api/metadata-block/#inject-into) for more information.

### GM_getValue

Retrieves a value for current script from storage.

```js
let value = GM_getValue(key, defaultValue)
```

- `key` *string*

    The name for `value` to load.

- `defaultValue` *any*

    The default value to return if no value exists in the storage.

### GM_setValue

Sets a key / value pair for current script to storage.

```js
GM_setValue(key, value)
```

- `key` *string*

    The unique name for `value` within this script.

- `value` *any*

    The value to be stored, which must be *JSON serializable* (string, number, boolean, null, or an array/object consisting of these types) so for example you can't store DOM elements or objects with cyclic dependencies.

### GM_deleteValue

Deletes an existing key / value pair for current script from storage.

```js
GM_deleteValue(key)
```

- `key` *string*

    The unique name for `value` within this script.

### GM_listValues

Returns an array of keys of all available values within this script.

```js
let arrayOfKeys = GM_listValues()
```

### GM_addValueChangeListener

Adds a change listener to the storage and returns the listener ID.

```js
let listenerId = GM_addValueChangeListener(name, callback)
```

* `name` *string*

    The name of the observed variable

* `callback` *function(name, oldValue, newValue, remote) {}*

    * `name` *string*

        The name of the observed variable

    * `oldValue` *any*

        The old value of the observed variable (`undefined` if it was created)

    * `newValue` *any*

        The new value of the observed variable (`undefined` if it was deleted)

    * `remote` *boolean*

        `true` if modified by the userscript instance of another tab or `false` for this script instance. Can be used by scripts of different browser tabs to communicate with each other.

### GM_removeValueChangeListener

Removes a change listener by its ID.

```js
GM_removeValueChangeListener(listenerId)
```

- `listenerId` *string*

### GM_getResourceText

Retrieves a text resource from the metadata block.

```js
let text = GM_getResourceText(name)
```

- `name` *string*

    Name of a resource defined in the [metadata block](../metadata-block/#resource).

### GM_getResourceURL

Retrieves a `Blob` URL of a resource from the metadata block.

```js
let blobUrl = GM_getResourceURL(name)
```

- `name` *string*

    Name of a resource defined in the [metadata block](../metadata-block/#resource).

### GM_addStyle

Appends and returns a `<style>` element with the specified CSS.

```js
let styleElement = GM_addStyle(css);
```

- `css` *string*

    The CSS code to inject.

Older versions of Violentmonkey (prior to 2.12.0) returned an imitation of Promise,
which is still maintained for compatibility, so don't be surprised if you see code like
`GM_addStyle(css).then(el => { /* whatever */ });`

### GM_openInTab

Opens URL in a new tab.

1. using an object

    ```js
    let tabControl = GM_openInTab(url, options)
    ```

    - `url` *string*

        The URL to open in a new tab. URL relative to current page is also allowed.  
        Note: Firefox does not support data URLs.

    - `options` *object* (optional)

        - `active` *boolean*

            Make the new tab active (i.e. open in foreground).

2. Using a boolean, compatible with Greasemonkey:

    ```js
    let tabControl = GM_openInTab(url, openInBackground)
    ```

    - `openInBackground` *boolean*

        Open the tab in background.  
        Note, this is a reverse of the first usage method so for example `true` is the same as `{ active: false }`.

Returns an object with following properties:
- `onclose`

    Сan be assigned to a function. If provided, it will be called when the opened tab is closed.

- `closed` *boolean*

    Whether the opened tab is closed.

- `close` *function*

    A function to explicitly close the opened tab.

```js
let tabControl = GM_openInTab(url);
tabControl.onclose = () => console.log('tab is closed');
tabControl.close();
```

### GM_registerMenuCommand

Registers a command in Violentmonkey popup menu.

```js
GM_registerMenuCommand(caption, onClick)
```

- `caption` *string*

    The name to show in the popup menu.

- `onClick` *function*

    The function to execute when clicked in the menu.

If you want to add a shortcut, please see [vm.shortcut](https://github.com/violentmonkey/vm-shortcut).

### GM_unregisterMenuCommand

Unregisters a command which has been registered to Violentmonkey popup menu.

```js
GM_unregisterMenuCommand(caption)
```

- `caption` *string*

    The name of command to unregister.

### GM_notification

Shows an HTML5 desktop notification.

1. using an object:

    ```js
    GM_notification(options)
    ```

    - `options` *object*

        - `text` *string* (required)

            Main text of the notification.

        - `title` *string*

            Title of the notification.

        - `image` *string*

            URL of an image to show in the notification.

        - `onclick` *function*

            Callback when the notification is clicked by user.

        - `ondone` *function*

            Callback when the notification is closed, either by user or by system.

2. Using separate parameters, compatible with Greasemonkey:

    ```js
    GM_notification(text, title, image, onclick)
    ```

    - `text` *string* (required)

        Main text of the notification.

    - `title` *string*

        Title of the notification.

    - `image` *string*

        URL of an image to show in the notification.

    - `onclick` *function*

        Callback when the notification is clicked by user.

### GM_setClipboard

Sets data to system clipboard.

```js
GM_setClipboard(data, type)
```

- `data` *string*

    The data to be copied to system clipboard.

- `type` *string*

    The MIME type of data to copy. Default as `text/plain`.

### GM_xmlhttpRequest

Makes a request like XMLHttpRequest, with some special capabilities, not restricted by same-origin policy.

```js
let control = GM_xmlhttpRequest(details)
```

- `details` *object*:

    - `url` *string* (required)

        URL relative to current page is also allowed.

    - `method` *string*

        Usually `GET`.

    - `user` *string*

        User for authentication.

    - `password` *string*

        Password for authentication.

    - `overrideMimeType` *string*

        A MIME type to specify with the request.

    - `headers` *object*

        For example `{ 'name1': 'value1', 'name2': 'value2' }`.

        Some special headers are also allowed:

        - `'Cookie'`
        - `'Host'`
        - `'Origin'`
        - `'Referer'`
        - `'User-Agent'`

    - `responseType` *string*

        One of the following:

        - `'text'` *(default value)*
        - `'json'`
        - `'blob'`
        - `'arraybuffer'`
        - `'document'` *(since VM2.12.0)*

    - `timeout` *number* *(since VM2.9.5)*

        Time to wait for the request, none by default.

    - `data` *string | FormData | Blob*

        Data to send with the request, usually for `POST` and `PUT` requests.

    - `binary` *boolean* *(since VM2.12.2)*

        Send the `data` string as a `blob`. This is for compatibility with Tampermonkey/Greasemonkey, where only `string` type is allowed in `data`.

    - `context` *any*

        Can be an object and will be assigned to `context` of the response object.

    - `anonymous` *boolean* *(since VM2.10.1)*

        When set to `true`, no cookie will be sent with the request. The default value is `false`.

    Event handlers:

    - `onabort` *function*
    - `onerror` *function*
    - `onload` *function*
    - `onloadend` *function*
    - `onprogress` *function*
    - `onreadystatechange` *function*
    - `ontimeout` *function*

> Note:
>
> - `synchronous` is not supported.

Returns a control object with the following properties:

- `abort` *function*

    A function to abort the request.

The response object will be passed to each event handler, with following properties:

- `status`

    Same as the standard `XMLHttpRequest`.

- `statusText`

    Same as the standard `XMLHttpRequest`.

- `readyState`

    Same as the standard `XMLHttpRequest`.

- `responseHeaders`

    An object of all response headers.

- `responseText`

    Same as the standard `XMLHttpRequest`, only provided when available.

- `finalUrl`

    The final URL after redirection.

- `context`

    The same object passed to the original request.

### GM_download

*Since VM2.9.5*

Downloads a URL to a local file.

1. using an object:

    ```js
    GM_download(options)
    ```

    - `options` *object*:

        - `url` *string* (required)

            The URL to download.

        - `name` *string*

            The filename to save as.

        - `onload` *function*

            The function to call when download starts successfully.

        These are the same as in [GM_xmlhttpRequest](#gm_xmlhttprequest):

        - `headers` *object*
        - `timeout` *number*
        - `onerror` *function*
        - `onprogress` *function*
        - `ontimeout` *function*

2. using separate parameters:

    ```js
    GM_download(url, name)
    ```

    - `url` *string* (required)

        The URL to download.

    - `name` *string*

        The filename to save as.
