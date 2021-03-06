# Node.js Integration #

The Reactive Extensions for JavaScript provides integration points to the core Node.js libraries.

<!-- div -->

## `RxNode Methods`

### Callback Handlers

- [`fromCallback`](#rxnodefromcallbackfunc-scheduler-context)
- [`fromNodeCallback`](#rxnodefromnodecallbackfunc-scheduler-context)

### Event Handlers

- [`fromEvent`](#rxnodefromeventeventemitter-eventname)
- [`toEventEmitter`](#rxnodetoeventemitterobservable-eventname-handler)

### Stream Handlers

- [`fromStream`](#rxnodefromstreamstream-finisheventname)
- [`fromReadableStream`](#rxnodefromreadablestreamstream)
- [`fromWritableStream`](#rxnodefromwritablestreamstream)
- [`fromTransformStream`](#rxnodefromtransformstreamstream)
- [`writeToStream`](#rxnodewritetostreamobservable-stream-encoding)

## _RxNode Methods_ ##

### Callback Handlers ###

### <a id="rxnodefromcallbackfunc-scheduler-context"></a>`RxNode.fromCallback(func, [scheduler], [context])`
<a href="#rxnodefromcallbackfunc-scheduler-context">#</a> [&#x24C8;](https://github.com/Reactive-Extensions/RxJS/blob/master/index.js#L20-L22 "View in source")

**Deprecated in favor of `Rx.Observable.fromCallback` in rx.async.js.**

Converts a callback function to an observable sequence.

#### Arguments
1. `func` *(Function)*: Callback function
2. `[scheduler = Rx.Scheduler.timeout]` *(Scheduler)*: Scheduler used to execute the callback.
3. `[context]` *(Any)*: The context to execute the callback.

#### Returns
*(Function)*: Function, when called with arguments, creates an Observable sequence from the callback.

#### Example
```js
var fs = require('fs');
var Rx = require('rx');
var RxNode = require('rx-node');

// Wrap exists
var exists = RxNode.fromCallback(fs.exists);

// Call exists
var source = exists('/etc/passwd');

var observer = Rx.Observer.create(
    function (x) {
        console.log('Next: ' + x);
    },
    function (err) {
        console.log('Error: ' + err);
    },
    function () {
        console.log('Completed');
    }
);

var subscription = source.subscribe(observer);

// => Next: true
// => Completed
```

### Location

- index.js

* * *

### <a id="rxnodefromnodecallbackfunc-scheduler-context"></a>`RxNode.fromNodeCallback(func, [scheduler], [context])`
<a href="#rxnodefromcallbackfunc-scheduler-context">#</a> [&#x24C8;](https://github.com/Reactive-Extensions/RxJS/blob/master/index.js#L34-L36 "View in source")

**Deprecated in favor of `Rx.Observable.fromNodeCallback` in rx.async.js.**

Converts a Node.js callback style function to an observable sequence.  This must be in function (err, ...) format.

#### Arguments
1. `func` *(Function)*: Callback function which must be in function (err, ...) format.
2. `[scheduler = Rx.Scheduler.timeout]` *(Scheduler)*: Scheduler used to execute the callback.
3. `[context]` *(Any)*: The context to execute the callback.

#### Returns
*(Function)*: An function which when applied, returns an observable sequence with the callback arguments as an array.

#### Example
```js
var fs = require('fs');
var Rx = require('rx');
var RxNode = require('rx-node');

var source = RxNode.fromNodeCallback(fs.stat)('file.txt');

var observer = Rx.Observer.create(
    function (x) {
        var stat = x[0];
        console.log('Next: ' + stat.isFile());
    },
    function (err) {
        console.log('Error: ' + err);
    },
    function () {
        console.log('Completed');
    }
);

var subscription = source.subscribe(observer);

// => Next: true
// => Completed
```

### Location

- index.js

* * *

### Event Handlers ###

### <a id="rxnodefromeventeventemitter-eventname"></a>`RxNode.fromEvent(eventEmitter, eventName)`
<a href="#rxnodefromeventeventemitter-eventname">#</a> [&#x24C8;](https://github.com/Reactive-Extensions/RxJS/blob/master/index.js#L48-L50 "View in source")

**Deprecated in favor of `Rx.Observable.fromEvent` in rx.async.js.**

Handles an event from the given EventEmitter as an observable sequence.

#### Arguments
1. `eventEmitter` *(EventEmitter)*: The EventEmitter to subscribe to the given event.
2. `eventName` *(String)*: The event name to subscribe.

#### Returns
*(Observable)*: An observable sequence generated from the named event from the given EventEmitter.

#### Example
```js
var EventEmitter = require('events').EventEmitter;
var Rx = require('rx');
var RxNode = require('rx-node');

var emitter = new EventEmitter();

var source = RxNode.fromEvent(emitter, 'data');

var observer = Rx.Observer.create(
    function (x) {
        console.log('Next: ' + x[0]);
    },
    function (err) {
        console.log('Error: ' + err);
    },
    function () {
        console.log('Completed');
    }
);

var subscription = source.subscribe(observer);

emitter.emit('data', 'foo');

// => Next: foo
```

### Location

- index.js

* * *

### <a id="rxnodetoeventemitterobservable-eventname"></a>`RxNode.toEventEmitter(observable, eventName)`
<a href="#rxnodetoeventemitterobservable-eventname">#</a> [&#x24C8;](https://github.com/Reactive-Extensions/RxJS/blob/master/index.js#L60-L88 "View in source")

Converts the given observable sequence to an event emitter with the given event name.
The errors are handled on the 'error' event and completion on the 'end' event.

#### Arguments
1. `observable` *(Obsesrvable)*: The observable sequence to convert to an EventEmitter.
2. `eventName` *(String)*: The event name to subscribe.

#### Returns
*(EventEmitter)*: An EventEmitter which emits the given eventName for each onNext call in addition to 'error' and 'end' events.

#### Example
```js
var Rx = require('rx');
var RxNode = require('rx-node');

var source = Rx.Observable.return(42);

var emitter = RxNode.toEventEmitter(source, 'data');

emitter.on('data', function (data) {
    console.log('Data: ' + data);
});

emitter.on('end', function () {
    console.log('End');
});

// Ensure to call publish to fire events from the observable
emitter.publish();

// => Data: 42
// => End
```

### Location

- index.js

* * *

### Stream Handlers ###

### <a id="rxnodefromstreamstream-finisheventname"></a>`RxNode.fromStream(stream, finishEventName)`
<a href="#rxnodefromstreamstream-finisheventname">#</a> [&#x24C8;](https://github.com/Reactive-Extensions/RxJS/blob/master/index.js#L96-L124 "View in source")

Converts a flowing stream to an Observable sequence.

#### Arguments
1. `stream` *(Stream)*: A stream to convert to a observable sequence.
2. `[finishEventName]` *(String)*: Event that notifies about closed stream. ("end" by default)

#### Returns
*(Observable)*: An observable sequence which fires on each 'data' event as well as handling 'error' and finish events like `end` or `finish`.

#### Example
```js
var RxNode = require('rx-node');

var subscription = RxNode.fromStream(process.stdin, 'end')
    .subscribe(function (x) { console.log(x); });

// => r<Buffer 72>
// => x<Buffer 78>
```

### Location

- index.js

* * *

### <a id="rxnodefromreadablestreamstream"></a>`RxNode.fromReadableStream(stream)`
<a href="#rxnodefromreadablestreamstream">#</a> [&#x24C8;](https://github.com/Reactive-Extensions/RxJS/blob/master/index.js#L131-L133 "View in source")

Converts a flowing readable stream to an Observable sequence.

#### Arguments
1. `stream` *(Stream)*: A stream to convert to a observable sequence.

#### Returns
*(Observable)*: An observable sequence which fires on each 'data' event as well as handling 'error' and 'end' events.

#### Example
```js
var RxNode = require('rx-node');

var subscription = RxNode.fromReadableStream(process.stdin)
    .subscribe(function (x) { console.log(x); });

// => r<Buffer 72>
// => x<Buffer 78>
```

### Location

- index.js

* * *

### <a id="rxnodefromwritablestreamstream"></a>`RxNode.fromWritableStream(stream)`
<a href="#rxnodefromwritablestreamstream">#</a> [&#x24C8;](https://github.com/Reactive-Extensions/RxJS/blob/master/index.js#L140-L142 "View in source")

Converts a flowing writeable stream to an Observable sequence.

#### Arguments
1. `stream` *(Stream)*: A stream to convert to a observable sequence.

#### Returns
*(Observable)*: An observable sequence which fires on each 'data' event as well as handling 'error' and 'finish' events.

#### Example
```js
var RxNode = require('rx-node');

var subscription = RxNode.fromWritableStream(process.stdout)
    .subscribe(function (x) { console.log(x); });

// => r<Buffer 72>
// => x<Buffer 78>
```

### Location

- index.js

* * *

### <a id="rxnodefromtransformstreamstream"></a>`RxNode.fromTransformStream(stream)`
<a href="#rxnodefromtransformstreamstream">#</a> [&#x24C8;](https://github.com/Reactive-Extensions/RxJS/blob/master/index.js#L149-L151 "View in source")

Converts a flowing transform stream to an Observable sequence.

#### Arguments
1. `stream` *(Stream)*: A stream to convert to a observable sequence.

#### Returns
*(Observable)*: An observable sequence which fires on each 'data' event as well as handling 'error' and 'finish' events.

#### Example
```js
var RxNode = require('rx-node');

var subscription = RxNode.fromTransformStream(getTransformStreamSomehow());
```

### Location

- index.js

* * *

### <a id="rxnodewritetostreamobservable-stream-encoding"></a>`RxNode.writeToStream(observable, stream, [encoding])`
<a href="#rxnodewritetostreamobservable-stream-encoding">#</a> [&#x24C8;](https://github.com/Reactive-Extensions/RxJS/blob/master/index.js#L160-L171 "View in source")

Writes an observable sequence to a stream.

#### Arguments
1. `observable` *(Observable)*: Observable sequence to write to a stream.
2. `stream` *(Stream)*: The stream to write to.
3. `[encoding]` *(String)*: The encoding of the item to write.

#### Returns
*(Disposable)*: The subscription handle.

#### Example
```js
var Rx = require('rx');
var RxNode = require('rx-node');

var source = Rx.Observable.range(0, 5);

var subscription = RxNode.writeToStream(source, process.stdout, 'utf8');

// => 01234
```

### Location

- index.js

* * *
