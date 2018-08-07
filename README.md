# rest-api-with-spring-actuator
In this post, we’re going to introduce **Spring Boot Actuator**, by first covering the basics. Afterwards, you will create a Spring project and learn how to use, configure and extend this monitoring tool.


## Download
Help us find bugs, add new features or simply just feel free to use it. Download **rest-api-with-spring-actuator** from our [GitHub](https://github.com/canchito-dev/rest-api-with-spring-actuator) site.


## License
The MIT License (MIT)  

Copyright (c) 2018, canchito-dev  

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the “Software”), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:  

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.  

THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.


## Contribute Code
If you would like to become an active contributor to this project please follow these simple steps:

1.  Fork it
2.  Create your feature branch
3.  Commit your changes
4.  Push to the branch
5.  Create new Pull Request

**Source code** can be downloaded from [GitHub](https://github.com/canchito-dev/rest-api-with-spring-actuator).


## What you’ll need
*   About 40 minutes
*   A favorite IDE. In this post, we use:Eclipse IDE for Java DevelopersVersion: Mars.2 Release (4.5.2) Build id: 20160218-0600
*   [JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/index.html) or later. It can be made to work with JDK6, but it will need configuration tweaks. Please check the Spring Boot documentation
*   An empty Spring project. You can follow the steps from [here](http://canchito-dev.com/public/blog/2017/04/16/spring-initializer/).


## Introduction
In this article, we’re going to introduce **Spring Boot Actuator**, by first covering the basic and afterwards by learning how to use, configure and extend this monitoring tool.

The current article is based on our previous article [“REST API with Spring JPA Criteria”](http://www.canchito-dev.com/public/blog/2018/05/30/rest-api-with-spring-jpa-criteria/). Please refer to it, in order to understand how the application was constructed.


## Overview
**Spring Boot** is a framework aimed to help developers to easily create and build stand-alone, production-grade Spring based Applications that you can "just run".

In addition, **Spring Boot** also comes with a series of additional features designed to help you monitor and manage your applications at the moment they are pushed into production. You can decide whether to manage and monitor them via HTTP endpoints or using JMX.

**Spring Boot Actuator** is a sub-project of **Spring Boot**. **Spring Boot** includes several built-in endpoints, and you can also add your own or even configure existing endpoints to be exposed on any custom endpoints of your choice.

For now, we will focus on showing how to use, configure and expose the already available endpoints, and how to configure **Spring Boot Actuator** in order to suit your requirements better.


## Definition of Actuator
An actuator is a manufacturing term that refers to a mechanical device or a component of a machine responsible for moving or controlling a mechanism or system. Actuators can generate a large amount of motion from a small change.

Thanks to the features provided by the actuator, we can easily monitor our app, by gathering metrics, understanding the traffic or the current state of our database.

One of the main advantages of this library is that we can get production ready tools and features without the need of actually developing and implementing them ourselves.


## Enabling Production-ready Features
Let’s start by enabling the built-in endpoints. To do so, we need to include the [spring-boot-actuator](https://github.com/spring-projects/spring-boot/tree/v2.0.2.RELEASE/spring-boot-project/spring-boot-actuator) module into our project. The simplest way to enable the features is to add the `spring-boot-starter-actuator` dependency.

So, let’s begin by adding the actuator to our Maven based project. Open the _pom.xml_ and add the following XML snipped:

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
  </dependency>
</dependencies>
```

Take into consideration that, most endpoints are sensitive – meaning they’re not fully public. Due to this, most information will be omitted. How to handle these sensitive endpoints is explained later in this article.


## Endpoints
Throw the endpoints, you can easily monitor and interact with your application. There are several built-in endpoints included within **Spring Boot**. But you are not limited to them, you can easily develop and implement your own.

Each individual endpoint can be enabled or disabled. Thus, allowing you to control which endpoint is created and consequently exposed. Exposing means making and endpoint remotely accessible via HTTP or JMX.

For our purpose, we will expose endpoints via HTTP, where the ID of the endpoint along with a prefix of `/actuator` is mapped to a URL.


## So far…
Until this moment, we have included the [spring-boot-actuator](https://github.com/spring-projects/spring-boot/tree/v2.0.2.RELEASE/spring-boot-project/spring-boot-actuator) module into our _pom.xml_ file. So let’s execute our project and test the actuator’s endpoint with the help of [curl](https://curl.haxx.se/) or [Postman](https://www.getpostman.com/):

```
http://localhost:8080/actuator
```

In the response body you should see something like this:

```json
{
    "_links": {
        "self": {
            "href": "http://localhost:8080/actuator",
            "templated": false
        },
        "health": {
            "href": "http://localhost:8080/actuator/health",
            "templated": false
        },
        "info": {
            "href": "http://localhost:8080/actuator/info",
            "templated": false
        }
    }
}
```

This shows us an overview of the exposed actuator endpoints. As you can see, only three actuator endpoints are exposed by default. The _url_ we just tested is known as the _“discovery page”_, and it includes links to all the available endpoints. Here is a brief description of the out-of-the-box endpoints:

*   `/health`: Shows application health information (a simple ‘status’ when accessed over an unauthenticated connection or full message details when authenticated); it’s not sensitive by default.
*   `/info:` Displays arbitrary application info; not sensitive by default. Please refer to [Configuring `/info` Endpoint](#_Configuring_/info_Endpoint) section for customizing this endpoint.


## Enabling Endpoints
By default, all endpoints except for `/shutdown `are enabled. To configure the enablement of an endpoint, use its `management.endpoint.<id>.enabled` property.

If you prefer endpoint enablement to be opt-in rather than opt-out, set the `management.endpoints.enabled-by-default` property to `false `and use individual endpoint `enabled `properties to opt back in. The following example enables the `/info` endpoint and disables all other endpoints:

```
management.endpoints.enabled-by-default=false
management.endpoint.info.enabled=true
```

For a complete list of available endpoints, please refer to [Spring Boot’s official documentation](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#production-ready-endpoints-exposing-endpoints).

## Configuring `/info` Endpoint
As we have already said, `/info` endpoint displays arbitrary application information; not sensitive by default. But this information has to be provided to the endpoint.

This can be done by setting `info.*` in **Spring**’s _application.properties_ file or rather than hardcoding those values, you could also expand info properties at build time, or the option I like the most, by automatically expanding the property using **Maven**.

So, open _pom.xml_ your file and paste the following:

```xml
<plugin>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-maven-plugin</artifactId>
  <configuration>
    <additionalProperties>
      <encoding.source>UTF-8</encoding.source>
      <encoding.reporting>UTF-8</encoding.reporting>
      <java.source>${maven.compiler.source}</java.source>
      <java.target>${maven.compiler.target}</java.target>
    </additionalProperties>
  </configuration>
  <executions>
    <execution>
      <goals>
        <goal>build-info</goal>
      </goals>
    </execution>
  </executions>
</plugin>
```

If you call the `/info` endpoint with the help of [curl](https://curl.haxx.se/) or [Postman](https://www.getpostman.com/):

```
http://localhost:8080/actuator/info
```

In the response body you should see something like this:

```json
{
    "build": {
        "name": "rest-api-actuator",
        "time": "2018-06-10T17:16:04.892Z",
        "java": {
            "target": "1.8",
            "source": "1.8"
        },
        "encoding": {
            "source": "UTF-8",
            "reporting": "UTF-8"
        },
        "version": "0.0.1-SNAPSHOT",
        "group": "com.canchitodev.example",
        "artifact": "rest-api-actuator"
    }
}
```


## Secure the Endpoints
For obvious reasons, we do not want this information exposed by actuator’s endpoints to be accessible by everyone. In that case, we can secure the actuator endpoints by adding **Spring Boot Security** to the _pom.xml_ file:

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

If you execute the application, you will notice in the console the following line:

Using generated security password: 88283e85-3dfb-4baf-bc24-cc8222fd1ac6

From this moment on, you have to use _Basic Authentication_ to gain access to not only the actuator’s endpoints, but to any exposed endpoint. The default user account is _user_. You can modify this behavior by adding the following options in _application.properties_ the file:

```
spring.security.user.name=user # Default user name.
spring.security.user.password= # Password for the default user name.
spring.security.user.roles= # Granted roles for the default user name.
```

And to complement it, include this **_`@Configuration`_**:

```java
@Configuration
public static class ActuatorWebSecurityConfigurationAdapter extends WebSecurityConfigurerAdapter {
  protected void configure(HttpSecurity http) throws Exception {
    http.requestMatcher(EndpointRequest.toAnyEndpoint()).authorizeRequests()
      .anyRequest().hasRole("ACTUATOR")
      .and()
      .httpBasic();
  }
}
```

Please note that we have set up the role as `“ACTUATOR”`. This is the same role specified in the spring.security.user.roles  property. Remember that all the built-in endpoints except `/info` are sensitive by default. By using **Spring Boot Security**, we can secure these endpoints by defining the default security properties – username, password, and role – in the _application.properties_ file.

## Further Customization
To further secure you application, you might decide to expose the actuator endpoints over por different than the standard one. The following properties restrict where the endpoints can be accessed from over the network:

```
management.server.port= # Management endpoint HTTP port (uses the same port as the application by default). Configure a different port to use management-specific SSL.
```

You can even change the IP from which the actuator’s endpoints can be accessed, simply by modifying the following properties:

```
management.server.address= # Network address to which the management endpoints should bind. Requires a custom management.server.port.
```

## Exposing Endpoints
Before exposing any endpoint, give it a hard though, as they might contain sensitive information which should not be exposed at all. You can use the technology-specific `include` and `exclude` properties to change which endpoints are exposed:

```
management.endpoints.web.exposure.include= # Endpoint IDs that should be included or '*' for all.
management.endpoints.web.exposure.exclude= # Endpoint IDs that should be excluded.
```

The endpoints that are exposed are listed in the `include`. While the endpoints of those which should not be exposed, are listed in the `exclude` property. The exclude property takes precedence over the `include` property. Both `include` and `exclude` properties can be configured with a list of endpoint IDs.

## The `/health` Endpoint
The `/health` endpoint is useful to check the current status of your running application. It is commonly used together with any monitoring software to alert when a production system goes down. The information exposed by the `/health` endpoint depends on the `management.endpoint.health.show-details` property.

There is a very complete list of health indicators that can be used. Please refer to section [Auto-configured HealthIndicators](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#_auto_configured_healthindicators) found in **Spring Boot**’s official documentation.


## Summary
The presented implementation of **Spring Boot Actuator** is simple, but yet very powerful, as it exposes a lot of endpoints that as useful for monitoring system running under a production environment.

We hope that, even though this was a very basic introduction, you understood how to use, configure and extend this monitoring tool. 

Please feel free to contact us. We will gladly response to any doubt or question you might have.

The full implementation of this article can be found in the [GitHub](https://github.com/canchito-dev/rest-api-with-spring-actuator) project – this is a Maven-based project, so it should be easy to import and run as it is.
