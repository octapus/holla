![status](https://secure.travis-ci.org/wearefractal/holla.png?branch=master)

## Information

<table>
<tr>
<td>Package</td>
<td>holla</td>
</tr>
<tr>
<td>Description</td>
<td>WebRTC Sugar</td>
</tr>
<tr>
<td>Node Version</td>
<td>>= 0.6</td>
</tr>
</table>

## Example

### Server

```javascript
var holla = require('holla');
var server = http.createServer().listen(8080);
var rtc = holla.createServer(server);

console.log('Server running on port 8080');
```
Note: Express 3 is no longer a httpServer so you need to do something like:  
```javascript
var server = require('http').createServer(app).listen(8080);
```
before passing it to holla.createServer

### Client

Sending a call:

```javascript
var rtc = holla.createClient();
rtc.register("tom", function(worked) {
  holla.createFullStream(function(err, stream) {

    var call = rtc.call("bob");
    call.addStream(stream);
    holla.pipe(stream, $("#me"));

    call.on("answered", function() {
      console.log("Remote user answered the call");
    });

    console.log("Calling ", call.user);
  });
});
```

Receiving a call:

```javascript
var rtc = holla.createClient();
rtc.register("bob", function(worked) {
  rtc.on("call", function(call) {
    console.log("Inbound call from ", call.user);

    holla.createFullStream(function(err, stream) {

      call.addStream(stream);
      call.answer();
      holla.pipe(stream, $("#me"));

      call.ready(function(stream) {
        holla.pipe(stream, $("#them"));
      });

    });

  });
});
```

## Client

### holla

#### .supported

true or false if WebRTC is supported in the browser

#### .connect(host)

Takes a host (http://site.com:8080) - defaults to window.location.href. Returns an RTC instance

#### .pipe(stream, el)

Pipes a WebRTC video stream to a video element. el can be a string (id), jquery element, or dom node.

#### .createStream(opt, cb)

Creates a WebRTC stream - opt looks like ```{video:true,audio:true}``` depending on the stream you want. cb signature is ```(err, stream)```

#### createFullStream(cb)

Sugar for ```.createStream({video:true,audio:true}, cb)```

#### createVideoStream(cb)

Sugar for ```.createStream({video:true,audio:false}, cb)```

#### createAudioStream(cb)

Sugar for ```.createStream({video:false,audio:true}, cb)```


### RTC

#### .register(name, cb)

Registers your connection with the server under a name. cb receives true or false if it worked. cb is optional.

#### .call(name)

Creates a call to user with name. Returns a Call instance

#### .ready(fn)

fn gets called when connection is registered or if it already has been.

#### Events

RTC will emit connected, authorized, disconnected, error, presence, and call events.


### Call

#### .isCaller

true or false if you started this call.

#### .user

Name of the user on the other end.

#### .startTime

Date of the call start.

#### .endTime

Date of the call end.

#### .duration()

Duration of the call.

#### .addStream(stream)

Adds your WebRTC stream to the call. Must be done before answering or sending a call.

#### .chat(msg)

Sends a chat message

#### .answer()

Accepts the call (inbound only)

#### .decline()

Declines the call (inbound only)

#### .end()

Ends the call (hangup and close connection)

#### .ready(fn)

Will call fn when the call has been connected and is ready to go or if it is already.

#### Events

Call will emit calling, connecting, connected, hangup, and chat events

## Examples

You can view more examples in the [example folder.](https://github.com/wearefractal/holla/tree/master/examples)

## Demo

There is a crappy demo up at [holla.jit.su](http://holla.jit.su)


## LICENSE

(MIT License)

Copyright (c) 2012 Fractal <contact@wearefractal.com>

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
