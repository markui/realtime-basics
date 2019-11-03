# realtime-basics
A study of how to build scalable real time chat apps

## The history of web and the need for realtime
1. Static Content(HTTP)
2. Dynamic Content(AJAX)
3. To Achieve Realtime
>3-1. Polling: Send AJAX request to server every specified interval, and return an empty response even if there is no update from the server<br>
3-2. Long Polling(Comet): Send request to server and keep the connection open until a new update on the server, and immediately requests back to the server. (ex) Hanging GET Requests / Pending POST Requests)<br>
3-3. Streaming<br>
3-4. Server Sent Events: Uses Eventsource API to send messages from the server to client.(Not truly bi-directional). Generally requires event-loop. No binary message capability.<br>
>3-5. Websockets

## The 7 Layers of the OSI Model

## Sockets? File Descriptors?

## What are Websockets?

[Introduction to Websockets](https://www.linode.com/docs/development/introduction-to-websockets/)
[A Beginner's Guid to Websockets](https://www.youtube.com/watch?v=PjiXkJ6P9pQ)

> Websocket Protocol is an Upgrade of the HTTP Protocol. You only send headers once in the HTTP handshake, and then just send back and forth data through the same TCP connection.
> To Deploy, you might have to use Eventlet or Gevent to monkey patch async, Async Web Frameworks are idela(like torando), Use message queue to run multiple instances behind (load balancers with sticky sessions on workers)

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


https://www.youtube.com/watch?v=KrPE5X4dqww
https://medium.com/containers-on-aws/scaling-a-realtime-chat-app-on-aws-using-socket-io-redis-and-aws-fargate-4ed63fb1b681


## What Pub/Sub Broker should we use for realtime-chat?


## What DB should we use for realtime-chat?

## System Design

https://www.youtube.com/watch?v=zKPNUMkwOJE
https://www.youtube.com/watch?v=vvhC64hQZMk
https://www.educative.io/courses/grokking-the-system-design-interview?affiliate_id=4793322061168640
[Line Live Chat Architecture](https://engineering.linecorp.com/en/blog/the-architecture-behind-chatting-on-line-live/)

## Websocket Benchmark Tools
[Thor](https://github.com/observing/thor#readme)


## Django Channels
[Deploying-Scaling-Django-Channels](https://avilpage.com/2018/05/deploying-scaling-django-channels.html)
[Scaling-Django-Channels-with-Docker](https://www.excella.com/insights/scaling-django-channels-with-docker)
[Setting-up-django-channels-on-eb](https://hackernoon.com/setting-up-django-channels-on-aws-elastic-beanstalk-716fd5a49c4a)
