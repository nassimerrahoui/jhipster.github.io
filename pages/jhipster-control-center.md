---
layout: default
title: JHipster Control Center
permalink: /jhipster-control-center/
sitemap:
    priority: 0.7
    lastmod: 2020-10-20T00:00:00-00:00
---

# <i class="fa fa-dashboard"></i> The JHipster Control Center

## Overview

The main purpose of JHipster Control Center is to monitor and manage applications.
All those features are packaged into one external application with a modern Vue user interface.

Like the JHipster generator, it is an Open Source, Apache 2-licensed application. Its source code is available on GitHub under the JHipster organization at [jhipster/jhipster-control-center](https://github.com/jhipster/jhipster-control-center).

![]({{ site.url }}/images/jhipster-control-center-animation.gif)

## Summary

1. [Installation](#installation)
2. [Instances](#instances)
4. [Metrics](#metrics)

## <a name="installation"></a> Installation

### Specific Spring profiles

The JHipster Control Center uses the usual JHipster `dev` and `prod` Spring profiles, as well as the standard `composite` from Spring Cloud Config (See [official documentation](https://cloud.spring.io/spring-cloud-config/multi/multi__spring_cloud_config_server.html#composite-environment-repositories)).

As a result:

- Using the `dev` profile will run the JHipster Registry with the `dev` and the `composite` profiles.
- Using the `prod` profile will run the JHipster Registry with the `prod` and the `composite` profiles.

In order to work properly, the Control Center has to be started with a spring profile corresponding to a Spring Cloud discovery backend :

- `eureka`: Connect to an Eureka server and fetch its registered instances, configured in application-eureka.yml
- `consul`: Connect to a Consul server and fetch its registered instances, configured in application-consul.yml
- `static`: Uses a static list of instances provided as properties, configured in application-static.yml
- `kubernetes`: To be developed

This is very useful for microservices architectures: this is how the gateways know which microservices are available, and which instances are up

For all applications, including monoliths, this is how the Hazelcast distributed cache can automatically scale, see [the Hazelcast cache documentation]({{ site.url }}/using-cache/)

## Running locally

### Step 1 : Run server used by Spring Cloud discovery backend

Eureka and Consul docker-compose files exist under src/main/docker to ease testing the project.

- for Consul : run `docker-compose -f src/main/docker/consul.yml up -d`
- for Eureka : run `docker-compose -f src/main/docker/jhipster-registry.yml up -d`

### Step 2 : Choose your authentication profile

There is 2 types of authentication.

- JWT : This is the default authentication, if you choose this one, you have to do nothing.
- OAuth2 : To use OAuth2 authentication, you have to launch Keycloak. Run `docker-compose -f src/main/docker/keycloak.yml up -d`

### Step 3 : Run the cloned project

- For development with JWT, run `./mvnw -Dspring.profiles.active=consul,dev` or `./mvnw -Dspring.profiles.active=eureka,dev`.
- For development with OAuth2, run `./mvnw -Dspring.profiles.active=consul,dev,oauth2` or `./mvnw -Dspring.profiles.active=eureka,dev,oauth2`.
- To just start in development run `./mvnw`.

You can run `npm start` in another terminal pane for hot reload of client side code.

## Running from Docker

A container image has been made available on Docker hub. To use it, run these commands :

- `docker pull jhipster/jhipster-control-center`
- `docker run -d --name jhcc -p 7419:7419 jhipster/jhipster-control-center:latest`

## <a name="instances"></a> Instances

![]({{ site.url }}/images/jhipster-control-center-discovery.jpg)

The JHipster Control Center provides a list of application's instances. As soon as an application registers on the server, it will become available in the list.

In order to access sensitive information from the applications, the JHipster Control Center can use a JWT token. The JWT key used to sign the request should be the same for the applications and the JHipster Control Center: as by default the JHipster Control Center configures applications through Spring Cloud Config, this should work out-of-the-box, as it will send the same key to all applications.

Also, you can use a third-party authorization and authentication manager like Keycloak (or Okta soon). JHipster Control Center will use the OAuth2 protocole to generate a session when you connect to JHipster Control Center. So, a custom OAuth2 security configuration will use it to get the security context user. Finally, Spring Cloud Gateway will relay a token by a filter to get access to applications.

## <a name="metrics"></a> Metrics

![]({{ site.url }}/images/jhipster-control-center-metrics.jpg)

The metrics dashboard uses Micrometer to give a detailed view of the application performance.

It gives metrics on:

- the JVM
- HTTP requests
- cache usage
- database connection pool

By clicking on the Expand button next to the JVM thread metrics, you will get a stacktrace of the running application, which is very useful to find out blocked threads.
