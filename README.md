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


#### Spin up another client with the *same* name

Now try spinning up another instance of `eureka-client` by only over-riding the
port parameter:

By default, Eureka uses the hostname as the instance name, so you have to
override the `eureka.instance.metadataMap.instanceId` parameter when running
two instances locally for testing. ([reference](http://stackoverflow.com/a/30161798/3555741))

```
cd eureka-client
mvn spring-boot:run -Dserver.port=8082 -Deureka.instance.metadataMap.instanceId=instance2
```

The `/whoami` endpoint for `http://localhost:8082` shows what we expect.

In a minute or so, the `/instances` endpoint now shows two instances of
eureka-client running.

The Eureka dashboard also shows 2 instances of `eureka-client` running.

#### Access eureka-client service via the zuul proxy

Start up the zuul proxy application

```
cd zuul-proxy
mvn spring-boot:run
```

The proxy automatically maps a route based on the service names it finds in the eureka
registry and uses client side load balancing.

Repeated requests to `http://localhost:9999/eureka-client/whoami` will show a different
port depending on which instance it calls.