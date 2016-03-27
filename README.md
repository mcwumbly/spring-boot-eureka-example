### Spring Boot Eureka Demo

Simple demo of service registration and discovery using Eureka.

Initial work based on this guide:

https://spring.io/guides/gs/service-registration-and-discovery/

### Try it out

#### Eureka Server

Start the server

```
cd eureka-server
mvn spring-boot:run
```

Visit the Eureka dashboard at http://localhost:8761

Note that there are no 'Instances' yet registered


#### Eureka Client

Start up a client

```
cd eureka-client
mvn spring-boot:run
```

Visit the client directly at http://localhost:8080/

The `/whoami` endpoint will show the client's self-knowledge of its application name and port

```
{
  "springApplicationName":"eureka-client",
  "serverPort":"8080"
}
```

The `/instances` endpoint will take up to a minute to update, but should eventually
show all the instances of `eureka-client` that have been registered with the
Eureka Discovery Client.

```
[
  {
    "host":"hostname",
    "port":8080,
    "serviceId":"EUREKA-CLIENT",
    "uri":"http://hostname:8080",
    "secure":false
  }
]
```

You can also visit the Eureka dashoboard again now and see it listed there.


#### Spin up another client with a different name

You can see that another client will be registred by doing the following:

```
cd eureka-client
mvn spring-boot:run -Dspring.application.name=foo -Dserver.port=8081
```

The `/whoami` endpoint will show the name `foo` and the port `8081`.

In a minute or so, the `/instances` endpoint will show the information about
this `foo` instance too.

On the Eureka dashboard, two clients will now be registered.


#### Sping up another client with the *same* name

Now try spinning up another instance of `eureka-client` by only over-riding the
port parameter:

```
cd eureka-client
mvn spring-boot:run -Dserver.port=8082
```

The `/whoami` endpoint for `http://localhost:8082` shows what we expect.

In a minute or so, the `/instances` endpoint now shows the instance running on
port 8082 also, but for some reason, it doesn't show the instance running on
port 8080.

And if we check the `/instances` endpoint on `http://localhost:8080` we also
now only see the instance running on 8082 (even though clearly, the one on
8080 is running since that's what we're asking for.

The Eureka dashboard only shows 1 instance of `eureka-client` running.

What's going on here?

I'm not sure yet...

Let's try killing the instance running on 8082 and see what happens.

When we query `/instances` on 8080, it still only shows the instance on 8082.

But a minute later, that goes away and we just see the instance on 8080 again.

The question is, why don't we see both instances of `eureka-client` when they are
both running?
