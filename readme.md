# p-timeout

> Timeout a promise after a specified amount of time

## Install

```
$ npm install p-timeout
```

## Usage

```js
const delay = require('delay');
const pTimeout = require('p-timeout');

const delayedPromise = delay(200);

pTimeout(delayedPromise, 50).then(() => 'foo');
//=> [TimeoutError: Promise timed out after 50 milliseconds]
```

## API

### pTimeout(input, milliseconds, message?, options?)
### pTimeout(input, milliseconds, fallback?, options?)

Returns a decorated `input` that times out after `milliseconds` time. Decorated input has `.clear()` method to be able to clear timeout.

If you pass in a cancelable promise, specifically a promise with a `.cancel()` method, that method will be called when the `pTimeout` promise times out.

#### input

Type: `Promise`

Promise to decorate.

#### milliseconds

Type: `number`

Milliseconds before timing out.

Passing `Infinity` will cause it to never time out.

#### message

Type: `string | Error`\
Default: `'Promise timed out after 50 milliseconds'`

Specify a custom error message or error.

If you do a custom error, it's recommended to sub-class `pTimeout.TimeoutError`.

#### fallback

Type: `Function`

Do something other than rejecting with an error on timeout.

You could for example retry:

```js
const delay = require('delay');
const pTimeout = require('p-timeout');

const delayedPromise = () => delay(200);

pTimeout(delayedPromise(), 50, () => {
	return pTimeout(delayedPromise(), 300);
});
```

#### options

Type: `object`

##### customTimers

Type: `object` with function properties `setTimeout` and `clearTimeout`

Custom implementations for the `setTimeout` and `clearTimeout` functions.

Useful for testing purposes, in particular to work around [`sinon.useFakeTimers()`](https://sinonjs.org/releases/latest/fake-timers/).

Example:

```js
const pTimeout = require('p-timeout');
const sinon = require('sinon');

(async () => {
	const originalSetTimeout = setTimeout;
	const originalClearTimeout = clearTimeout;

	sinon.useFakeTimers();

	// Use `pTimeout` without being affected by `sinon.useFakeTimers()`:
	await pTimeout(doSomething(), 2000, undefined, {
		customTimers: {
			setTimeout: originalSetTimeout,
			clearTimeout: originalClearTimeout
		}
	});
})();
```

### pTimeout.TimeoutError

Exposed for instance checking and sub-classing.

## Related

- [delay](https://github.com/sindresorhus/delay) - Delay a promise a specified amount of time
- [p-min-delay](https://github.com/sindresorhus/p-min-delay) - Delay a promise a minimum amount of time
- [p-retry](https://github.com/sindresorhus/p-retry) - Retry a promise-returning function
- [More…](https://github.com/sindresorhus/promise-fun)
