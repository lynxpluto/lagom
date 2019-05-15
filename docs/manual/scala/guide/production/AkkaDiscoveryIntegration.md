# Using Akka Discovery

As of version 1.5.1, Lagom has built-in integration with [Akka Discovery](https://doc.akka.io/docs/akka/2.5/discovery/index.html) throught a   [ServiceLocator](api/com/lightbend/lagom/scaladsl/api/ServiceLocator.html) that wraps Akka Discovery. This is the recommended implementation for production specially for users targeting Kubernetes and DC/OS (Marathon).

## Dependency

To use this feature add the following in your project's build:

@[akka-discovery-dependency](code/akka-discovery-dependency.sbt)

## Configuration

Once you have it in your project you can add the component to your `LagomApplicationLoader`.

@[akka-discovery-service-locator](code/AkkaDiscoveryIntegration.scala)

By default, Lagom uses [Aggregate multiple discovery methods](https://doc.akka.io/docs/akka/2.5/discovery/index.html#discovery-method-aggregate-multiple-discovery-methods). The first discovery method is set to Configuration and the second is set to DNS. 
So the static definition of service endpoins has a priority over DNS discovery.

To statically configure service endpoints in your `application.conf` file consult the [Aggregate multiple discovery methods](https://doc.akka.io/docs/akka/2.5/discovery/index.html#discovery-method-aggregate-multiple-discovery-methods) documentation.

To configure service discovery with DNS Lagom provides some configuration settings. The default settings are following:
```hocon
  lagom.akka.discovery {
  
    # When the service lookup regex fails, the defaults are used for the port and protocol.
    defaults {
  
      # The default port name. Blank if no port name should be added by default.
      port-name = http
  
      # The default port protocol. Blank if no port protocol should be added by default.
      port-protocol = tcp
  
      # The default scheme to use in returned URIs if not defined in the port-name-scheme-mappings.
      scheme = http
    }
  
    # A mapping of service names to lookup information. Each mapping should define the following:
    #
    # - lookup - An alternative name for the service. This can be configured with a simple name or a SRV lookup, for exampe:
    #   * my-service (simple name)
    #   * my-service.default.svc.cluster.local (simple fully-qualified name)
    #   * _http._tcp.my-service (SRV)
    #   * _http._tcp.my-service.default.svc.cluster.local (fully-qualified SRV)
    # - scheme - The scheme to return in the URI. If undefined, it will use the default scheme lagom.akka.discover.defaults.scheme.
    #
    # For example:
    # service-name-mappings {
    #   my-service-name {
    #     lookup = my-service.default.svc.cluster.local
    #     scheme = http
    #   }
    # }
    service-name-mappings {
    }
  
  
    # The timeout for a successful lookup.
    lookup-timeout = 5 seconds
  }
```

Note: this component was [previous published](https://github.com/lagom/lagom-akka-discovery-service-locator) as an independent library. If you have it on your classpath it's recommended to remove it and use the one being provided by Lagom directly.
