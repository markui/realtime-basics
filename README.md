# realtime-basics
A study of how to build scalable real time chat apps

## The history of web and the need for realtime
1. Static Content(HTTP)
2. Dynamic Content(AJAX)
3. Realtime(Polling => Long Polling/Streaming => Websockets)

## Scailng Websockets?
[hackernoon-scaling-websockets](https://hackernoon.com/scaling-websockets-9a31497af051)

**"Scaling horizontally for load sharing and fault tolerance."**


### 1. Why is scaling Websockets different from HTTP?

* Since, HTTP is **stateless**, it can scale well. A common way to scale is **horizontal scaling**, scaling with a number of servers. For example, when there is high traffic, a load balancer would disperse the requests to a number of healthy servers behind the load balancer, and you don't have to worry. Since, HTTP is stateless, and the request itself contains all the information, a single request can be sent to whatever server on every request.
* However, in Websockets, interactions are **stateful** since it is a **duplex connection** between the client and the server, for each connection, you will end up storing at least some data(ex) **What clients, What data the clients is interested in(like socket.io channnels concept)**) in memory on the Websocket server.


> The fact that WebSocket connections are **persistent** is what makes it so powerful for **real-time applications**, but it’s also what makes it **more difficult to scale**.


### 2. You need a Pub/Sub Broker

#### 1) For coordination of your WS Cluster Servers
ex) Redis, RabbitMQ, Kafka, and RethinkDB

>Think about our example app. When a client sends through coordinates for a drawing, we just find the correct channel for the drawing and publish the updates made to the drawing to that channel. All the clients are connected to the one server, so they all get notified of the change. It’s kind of like **in-memory pub/sub**

>But in reality, we would want to scale across multiple servers and we want to do this for 2 reasons: **1) sharing processing power, and 2) redundancy.**


ex) 1 client per server, total of 3 WS Servers. One client makes a change => save it to DB => Pub to Broker => Notify Changes to Broker Sub WS Servers => WS Server requests for update in DB => emit that change to clients.

#### 2) To handle failover

When a client is connected to a WebSocket server, and that server falls over, the client can open a connection through the load balancer to another WebSocket server. The new WebSocket server will just ensure that there is a subscription to the pub/sub broker for the data that the WebSocket client is interested in and start piping through changes on the WebSocket when they occur.

### 4. Working with deltas

Use **data synchronization offsets**(ex) timestamps) so that the server doesn't send all the data again.


## What Pub/Sub Broker should we use for realtime-chat?


## What DB should we use for realtime-chat?
