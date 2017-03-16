A polyfill for React Native's `whatwg-fetch`'s mirror.

### The polyfill

This adds support for `timeout` as one of the `fetch` options.

```js
import fetch from 'react-native-fetch-polyfill';

fetch(url, {timeout: 30 * 1000})
  .then(response => {
    // a successful response
  })
  .catch(error => {
    // an error when the request fails, such as during a timeout
  })
```

React Native's `XMLHttpRequest` interface [exposes a timeout property sent to the `RCTNetworking` module](https://github.com/facebook/react-native/blob/v0.42.1/Libraries/Network/XMLHttpRequest.js#L500), as well as an [abort method](https://github.com/facebook/react-native/blob/v0.42.1/Libraries/Network/XMLHttpRequest.js#L505-L520). `fetch` does not expose access to this by default, this polyfill allows specifying a `timeout` within the options.

This value [attached to `NSMutableURLRequest`](https://github.com/facebook/react-native/blob/v0.42.1/Libraries/Network/RCTNetworking.mm#L232), where the native networking layer will enforce the timeout rule.

The result of the timeout being reached will result in a promise [rejected with a `TypeError('Network rqeuest failed')](https://github.com/github/fetch/blob/v1.1.1/fetch.js#L445).


### What is fetch?

Fetch is a networking abstraction above [`XMLHttpRequest`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest). It reflects the [WHATWG fetch specification](https://fetch.spec.whatwg.org/) and can be found in [whatwg/fetch](https://github.com/whatwg/fetch). It is the networking library [used in React Native](https://facebook.github.io/react-native/docs/network.html#using-fetch).

### Why a polyfill?

Fetch has two challenges:
- It cannot be externally aborted (https://github.com/whatwg/fetch/issues/27 and https://github.com/whatwg/fetch/issues/447)
- It does not support `timeout`(https://github.com/facebook/react-native/issues/2394, https://github.com/facebook/react-native/issues/2556, https://github.com/whatwg/fetch/issues/20, https://github.com/github/fetch/issues/175)

Why are these not supported? As a `fetch` maintainer points out in https://github.com/github/fetch/pull/68#issuecomment-70103306, the spec does not describe a standard for this behavior.

### How is the polyfill maintained?

The polyfill picks out specific pieces of [whatwg/fetch](https://github.com/whatwg/fetch) required to apply the patch.

The tagged version of the polyfill corresponds to the version of `fetch` that it patches.

When new versions of `fetch` are released, the polyfill will be updated and tagged.
