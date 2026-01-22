---
title: "How to use Redis pub/sub to handle socket.io sessions across multiple instances ?"
date: "2022-09-09"
description: "When we use socket.io we are generally trying to publish messages to all the collaborators of the workspace in an application even though the origin of the message was one of the collaborator specially in a collaborative application."
tags: ["redis", "go", "socketio", "pubsub"]
---

When we use socket.io we are generally trying to publish messages to all the collaborators of the workspace in an application even though the origin of the message was one of the collaborator specially in a collaborative application.

In this scenario where a server must convey its message to multiple users, a socket client creates a room and adds users to this room. when any change (like app update , new comment) happens in this room that particular change must be conveyed to all the members of the room.

The information, that there is a room called `xyz` and there are some collaborators say a, b, c in the room is stored by a socket instance on a particular server instance.

When multiple instances of the application or server are created a new instance of socket is also created for the new instance of application. Now, when the changes of a room comes to this server this instance does not know about the room created by previous instance and also does not know about the members / collaborators of the room. Therefore new server instance can’t send the changes to those connected to the existing server. I have tried to explain the problem visually here.

Before going into the solution of the problem, let’s understand the technologies i have used along with some snippet of the actual [code](https://github.com/sonu0702/redis-socket-blog/tree/master). I have written the server code in [golang](https://go.dev/learn/) and i am using [Postman](https://www.postman.com/) as client. The two technologies i would like to brief you all are [socket.io](http://socket.io) and [Redis pub/sub](https://redis.io/docs/manual/pubsub/).
If you are already familiar with the concepts just glance through my description to better understand the blog and solution provided by it.

### Brief description on socket.io

Socket.io helps in delivering messages to client in real time. Behind the scene [socket.io](http://socket.io) creates a bi-directional channel between server and client therefore enabling bidirectional communication between web client and server. For [demo code](https://github.com/sonu0702/redis-socket-blog/tree/master) I have used a [golang](https://go.dev/learn/) server therefor i have used a golang library called [go-socket.io](https://github.com/googollee/go-socket.io/tree/master/_examples) . Some advance socket.io concepts which you should be aware are channel, event, namespace and rooms.

Golang code snippet for Connecting to a socket.io - find full code [here](https://github.com/sonu0702/redis-socket-blog/tree/master)

```go
c.SocketServer.OnConnect("/", func(s socketio.Conn) error {
		log.Println("connected:", s.ID())
		return nil
	})
```

Golang code snippet for Disconnecting from a socket.io

```go
c.SocketServer.OnDisconnect("/", func(s socketio.Conn, reason string) {
		fmt.Println("Disconnected ", s.ID(), "reason", reason)
		s.Leave(reason)
		fmt.Println("Disconnected ", s.ID())
	})
```

### Brief description on rooms in socket.io

Room is a [socket.io](http://socket.io) feature. By using this a server can send a message to a particular group/subscribers. In order for the clients to receive the messages of particular group they have be subscribed/joined to a particular namespace called room. Then the socket.io uses broadcast method of the socket object to send messages to all the subscribed clients.

Socket.io rooms can be used in Chat applications and application which use workspace and collaborator architecture. In chat application the message is broadcasted to the group and all the users of the group receive the message in real-time. In collaborative applications messages are broadcasted to a workspace so that collaborators can receive them. Here is pictorial view explaining concept of rooms in socket.io.

Golang code snippet explaining the room join functionality

```go
c.SocketServer.OnEvent("/", "application",
		c.authenticate(c.authorize(func(s socketio.Conn, payload ApplicationSubscriptionPayload) error {
			log.Println("roomName : ", getRoomName(payload.Payload.WorkspaceId, payload.Payload.AppId),
				"payload : ", payload.Event)
			roomName := getRoomName(payload.Payload.WorkspaceId, payload.Payload.AppId)
			if payload.Event == Subscribe {
				sessionContext := getSessionContext(s)
				s.Join(roomName)
				log.Printf("subscribed : (%s), sessionId : %s, roomName : %s , allRooms : %v ",
					sessionContext, s.ID(),
					roomName, s.Rooms())
				newApplicationSubscriptionResponse := NewApplicationSubscriptionResponse("SUCCESS",
					payload.Event, payload.Payload)
				s.Emit("application_response", newApplicationSubscriptionResponse)
				return nil
			}
			log.Printf("%s un subscribed to roomName : %s , allRooms : %v ", s.ID(),
				roomName, s.Rooms())
			s.Leave(getRoomName(payload.Payload.WorkspaceId, payload.Payload.AppId))
			newNodezapSubscriptionResponse := NewApplicationSubscriptionResponse("SUCCESS",
				payload.Event, payload.Payload)
			s.Emit("application_response", newNodezapSubscriptionResponse)
			return nil
		})))
```

In the given code snippet authenticate, authorize functions are optional. These functions are middleware which can be used to add some more context to the event. `s.Join(roomName)` function is used to join the given room name. 

A sample payload which can be used to join the room is as given below it is specific to the application I have written. 

```json
{
    "event":"SUBSCRIBE",
    "payload":{
        "appId" :"test_app_id",
        "workspaceId" :"test_workspace_id"
    }
}
```

Let me explain [Redis pub/sub](https://redis.io/docs/manual/pubsub/)  which I am using as one of the solution for aforementioned problem.  

### Brief description on [Redis Pub/Sub](https://redis.io/docs/manual/pubsub/)

First of all let’s understand [Redis](https://redis.io/). It is an  in-memory data structure store which is used as a database, cache, message broker, and streaming engine. Redis pub/sub is one the features of Redis. You can get official documentation of Redis pub/sub [here](https://redis.io/docs/manual/pubsub/) . Let me give you a brief on few of the concepts of Redis pub/sub. There are two components in Redis pub/sub first one is Publisher. Its function is to send message payload to a given channel/Topic. Subscribers, these subscribe/unsubscribe to a particular topic. A message sent to a topic by a publisher is received by all  subscribers subscribed to that topic. Given figure explains the principle of Redis pub/sub.


Golang code snippet explaining subscription to a channel

```go
func (c *CacheStore) SubscribeSocketChannel(socketClient *Client) {
	ctx := context.Background()
	redisPubsub := c.RedisClient.Subscribe(ctx, storePubsubChannel)
	go func() {
		for msg := range redisPubsub.Channel() {
			switch msg.Channel {
			case storePubsubChannel:
				fmt.Println("received pubsub message:", msg.Payload)
				publishToSocketSubscribers(socketClient, msg.Payload)
			}
		}
	}()
}
```

Golang code snippet explaining publishing to a channel

```go
func Publish(ctx context.Context, redisClient *redis.Client,
	workspaceId, appId string, payload interface{}) error {
	publishableData, err := getPublishPayload(workspaceId, appId, payload)
	if err != nil {
		return err
	}
	redisStore := NewCacheStore(ctx, redisClient)
	err = redisStore.PublishToSocketChannel(publishableData)
	if err != nil {
		return err
	}
	return nil
}
```

Now that, you all have some idea of technologies I have used we can derive a viable solution to above mentioned problem. In simple words the problem is that all the server instance do not have the side effect / changes so they are unable to convey the changes/side effects to socket room subscribers. This can be easily solved by sending the changes from origin server to all the instances of the server.

The Redis real time database store with pub/sub like functionality fits quite easily in this scenario to solve this problem. It can help us to publish the changed data across the server instances and then the subscribers at each server can receive the changes and then using socket this changes can be sent to all the subscribed collaborators/clients. Therefore using Redis pub/sub we can handle socket sessions across multiple instances of application.

I have uploaded the Golang server code and Postman collection of apis and also the documentation on how to use the web socket using postman as a client on Github - [here](https://github.com/sonu0702/redis-socket-blog/tree/master)

This post is in collaboration with Redis.

**Learn more:**

- [Try Redis Cloud for free](https://redis.info/3NBGJRT)
- [Watch this video on the benefits of Redis Cloud over other Redis providers](https://redis.info/3Ga9YII)
- [Redis Developer Hub - tools, guides, and tutorials about Redis](https://redis.info/3LC4GqB)
- [RedisInsight Desktop GUI](https://redis.info/3wMR7PR)