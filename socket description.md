## TCP / IP sockets

### We are working with flask-socketio and handeling the following events:


#### Command
```
@socketio.on("command")
```

The format for a command is "/humain HUMAIN_NAME"

example from javascript client: 
```
socket.emit('command', {text: "/humain s0phi5"});
```
will return varification message in the format:
```
system: humain set to s0phi5
```

another command is "/conversion CONVERSATION_ID"
example from javascript client: 
```
socket.emit('command', {text: "/conversion vyjsvdj668vd"});
```
will return varification message in the format:
```
system: conversion set to vyjsvdj668vd
```



#### message
```
@socketio.on("message")
```
 * Will return 1 or more response's
 * If client didnt set the humain - will talk to s0phi5
 * if client didnt set conversation - will start new conversation

javascript client code example:
```
 socket.emit('message', {text: "example message text");
```


#### connect
```
@socketio.on("connect"
```
will not send a message. the framework should notify client.

javascript client code example:
```
var socket = io.connect(localTCPUrl);
```



#### disconnect
```
@socketio.on("disconnect")
```

will not send a message. the framework should notify client.


#### error
```
@socketio.on_error() 
```
we will not send a message. the framework should notify client.



## UDP
#### not Implemented yet


