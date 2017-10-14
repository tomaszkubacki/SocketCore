# SocketCore

SocketCore is a library for easy developing scalable realtime functionality in your .NET Core applications.
Conceptually very similar to SignalR.

It uses **only web sockets** as communication technology without support for any other technologies like long polling, forever frame, etc.
This gives a solid and future foundations for developing thruly persistend full-duplex communication in modern application.

Another core conncept is **scalability by default**.
Which means that every application by default will be able to use multiple instances to feed connected clients.
Internally it is realized by leveraging messaging infrastructure like Redis, but there is an option to switch to single process in-memory communication.

## Both persisted full-duplex communication and messaging architecture gives application developers amazying capabilities:
* send data to 'ConnectionID' (GUID or named connection like 'john_firefox_1')
* mananging connection's groups to support realtime collaboration features
* reacting on events like client connected/disconnedted

 Any application component/instance will be able to communicate with any connected client just by knowing its connection id without the need of being physically connected. This is huge opportunity to use microservices architecture and make applications very scalable.

 ## Features
 * persisted connections
 * naming connections
 * connection's groups
 * Redis and in-memory connection providers
 * TypeScript client

 ## Future
 * NuGet and Bower packages
 * more connection providers like RabbitMQ or Azure Service Bus
 * port client and server to other languages (.NET, node.js, python, etc)


# Getting started

## 1. Install nuget package for the middleware:
dotnet add package SocketCore.Server.AspNetCore

Create connection class:
```csharp
public class SimpleConnection : Connection
{
    protected override Task OnReceived(string connectionId, object data)
    {
        return SendToConnectionsAsync($"Reply to: {data}", connectionId);
    }
}
```

Configure middleware in your Startyp class:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory, IApplicationLifetime applicationLifetime)
{
    //...
    app.UseSocketCore("/realtime", new Conn1());
}
```

## 2. Install bower package for the js client:
bower install socketcore-client-javascript

Add script reference to your html page:
```html
<script src="/lib/socketcore-client-javascript/SocketCore.js"></script>
```

Create connection object:
```js
var conn  = new socketCore.connection("/realtime");

conn.stateChanged(function(state){
    log("state changed: " + state.current);
});

conn.opening(function(){
    log("opening: " + this.connectionId);
});

conn.opened(function(){
    log("opened: " + conn.connectionId);
});

conn.reopening(function(){
    log("reopening: " + conn.connectionId);
});

conn.closed(function(){
    log("closed: " + conn.connectionId);
});

conn.recieved(function(data){
    log("recieved (" + conn.connectionId + "): " + data);
});

conn.sent(function(data){
    log("sent (" + conn.connectionId + "): " + data);
});

conn.error(function(evt){
    log(JSON.stringify(evt));
});

conn.opened(function(){
    conn.send("Hello!!!");
});
```