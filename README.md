# Service Registry with Consul

This Spring Boot application provides service discovery using HashiCorp Consul instead of Netflix Eureka.

## Prerequisites

1. **Java 17** or higher
2. **Maven 3.6+**
3. **Consul** - You need to have Consul running locally or access to a Consul server

## Running Consul

### Option 1: Using Docker (Recommended)
```bash
docker run -d --name consul -p 8500:8500 -p 8600:8600 consul:latest
```

### Option 2: Download and Run Locally
1. Download Consul from [https://www.consul.io/downloads](https://www.consul.io/downloads)
2. Extract and run:
```bash
consul agent -dev -client 0.0.0.0
```

## Configuration

The application is configured to connect to Consul at:
- **Host**: localhost
- **Port**: 8500
- **Service Name**: SERVICE-REGISTRY
- **Health Check Path**: /actuator/health
- **Health Check Interval**: 15 seconds

## Running the Application

1. **Start Consul** (see above)
2. **Build the project**:
   ```bash
   mvn clean install
   ```
3. **Run the application**:
   ```bash
   mvn spring-boot:run
   ```

The application will start on port 8761 and automatically register itself with Consul.

## Consul UI

Access the Consul web interface at: http://localhost:8500

You should see your `SERVICE-REGISTRY` service registered in the Services tab.

## Health Checks

The application exposes health information at: http://localhost:8761/actuator/health

Consul will use this endpoint to monitor the service health.

## Client Services

To register other services with this Consul instance, add the following dependency to their `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-consul-discovery</artifactId>
</dependency>
```

And configure them to connect to the same Consul instance:

```yaml
spring:
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        service-name: your-service-name
```

## Differences from Eureka

- **No @EnableEurekaServer annotation** - Consul discovery is automatically enabled
- **Health checks** - Consul uses Spring Boot Actuator health endpoints
- **Service registration** - Services automatically register with Consul on startup
- **Service discovery** - Use Consul's REST API or Spring Cloud LoadBalancer for service discovery

## Troubleshooting

1. **Service not registering**: Ensure Consul is running and accessible
2. **Health check failures**: Verify the actuator endpoints are working
3. **Connection refused**: Check if Consul is running on port 8500 