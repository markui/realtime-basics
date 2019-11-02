# realtime-basics
A study of how to build scalable real time chat apps

## The history of web and the need for realtime
1. Static Content(HTTP)
2. Dynamic Content(AJAX)
3. Realtime(Polling => Long Polling/Streaming => Websockets)

## Scailng Websockets?
[hackernoon-scaling-websockets](https://hackernoon.com/scaling-websockets-9a31497af051)

### Why is scaling Websockets different from HTTP?

* Since, HTTP is **stateless**, it can scale well. A common way to scale is **horizontal scaling**, scaling with a number of servers. For example, when there is high traffic, a load balancer would disperse the requests to a number of healthy servers behind the load balancer, and you don't have to worry. Since, HTTP is stateless, and the request itself contains all the information, a single request can be sent to whatever server on every request.
* However, in Websockets, interactions are **stateful** since it is a **duplex connection** between the client and the server, for each connection, you will end up storing at least some data(ex) **What clients, What data the clients is interested in(like socket.io channnels concept)**) in memory on the Websocket server.


> The fact that WebSocket connections are **persistent** is what makes it so powerful for **real-time applications**, but it’s also what makes it **more difficult to scale**.


### You need a Pub/Sub Broker

ex) Redis, RabbitMQ, Kafka, and RethinkDB


>Think about our example app. When a client sends through coordinates for a drawing, we just find the correct channel for the drawing and publish the updates made to the drawing to that channel. All the clients are connected to the one server, so they all get notified of the change. It’s kind of like **in-memory pub/sub**

>But in reality, we would want to scale across multiple servers and we want to do this for 2 reasons: **1) sharing processing power, and 2) redundancy.**
>
