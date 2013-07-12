![PSON](https://raw.github.com/dcodeIO/PSON/master/PSON.png)
====
You **love** JSON for its simplicity but **hate** it for its network overhead?  
You **love** ProtoBuf for its small packet size but **hate** it for its complexity?

*Can't there solely be love?*

Yes it can!
-----------
**PSON** is a binary serialization format built on the simplicity of JSON and the encoding capabilities of
[ProtoBuf.js](https://github.com/dcodeIO/ProtoBuf.js).

Usage
-----

#### node.js/CommonJS

`npm install pson`

```js
var PSON = require("pson");
var initialDictionary = {...};
var pson = new PSON(initialDictionary);
...
```

#### RequireJS/AMD

```js
require.config({
    ...
    "paths": {
        "Long": "/path/to/Long.js",
        "ByteBuffer": "/path/to/ByteBuffer.js",
        "ProtoBuf": "/path/to/ProtoBuf.js",
        "PSON": "/path/to/PSON.js"
    },
    ...
});
require(["PSON"], function(PSON) {
    ...
});
```

#### Browser

```html
<script src="//raw.github.com/dcodeIO/Long.js/master/Long.min.js"></script>
<script src="//raw.github.com/dcodeIO/ByteBuffer.js/master/ByteBuffer.min.js"></script>
<script src="//raw.github.com/dcodeIO/ProtoBuf.js/master/ProtoBuf.min.js"></script>
<script src="//raw.github.com/dcodeIO/PSON/master/PSON.min.js"></script>
```

```js
var PSON = dcodeIO.PSON;
...
```

Example
-------
```js
// Sender
var initialDictionary = {...};
var pson = new PSON(initialDictionary);
var data = { "hello": "world!" };
var buffer = pson.encode(data);
someSocket.send(buffer);
```

```js
// Receiver
var pson = new PSON();
someSocket.on("data", function(data) {
    data = pson.decode(data);
    ...
});
```

Behind the love
---------------
The idea behind PSON is to keep a common dictionary containing keys to integer mappings on both ends. This is similar to
how ZIP archives work. The dictionary allows us to shorten any keywords to be represented by a single byte (in an optimal
case). As a result the exact string representing a keyword needs to be submitted only once, making each subsequent
message considerably smaller - in theory. Additionally, ProtoBuf's varint encoding is great for submitting
numeric values - in practice - and the `initialDictionary` parameter even allows to start off with arbitrary string
values (for keys _and_ values).

Comparison
----------
As of today there is a minimalistic test case for the following data:

```json
{
    "hello": "world!",
    "time": 1234567890,
    "float": 0.011,
    "boolean": true,
    "otherbool": false,
    "null": null,
    "obj": {
        "what": "that"
    },
    "arr": [1,2,3]
}
```

which is

* **131 bytes** large after `JSON.stringify`
* **151 bytes** after the first `PSON#encode` including the dictionary and
* **78 bytes** after each subsequent `PSON#encode`

which is, in this case, from the second message onwards about **40% smaller than JSON**.

Documentation
-------------
* [Fully documented source code](https://github.com/dcodeIO/PSON/tree/master/src)
* The [PSON.proto](https://github.com/dcodeIO/PSON/blob/master/src/PSON.proto) is also freely available and it should
  be quite easy to implement the protocol in a variety of programming languages using your favourite
  [protobuf library](http://code.google.com/p/protobuf/wiki/ThirdPartyAddOns).
* [Background reading](https://github.com/dcodeIO/ProtoBuf.js/wiki/ProtoBuf.js-vs-JSON)

**Note:** I just started working on this and I am not yet sure if this is a great idea or just another waste of time.
However, if you are interested in the topic, feel free to try it out, make contact or even to run some benchmarks.

**License:** [Apache License, Version 2.0](http://opensource.org/licenses/Apache-2.0)
