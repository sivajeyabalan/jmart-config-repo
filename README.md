# JMart Config Repository

This repository contains externalized configuration for all JMart/ShopScale microservices.

## 📋 Overview

This is a Spring Cloud Config Server backend repository that stores configuration files for all microservices in the ShopScale e-commerce platform.

## 🗂️ Configuration Files

### Global Configuration
- **application.yml** - Global settings applied to all services
  - Logging configuration
  - Management endpoints
  - Eureka client defaults

### Service-Specific Configurations

#### 1. service-registry.yml
- **Service:** Eureka Service Registry
- **Port:** 8761
- **Purpose:** Service discovery and registration
- **Key Settings:**
  - Self-preservation enabled
  - Eviction interval: 60 seconds

#### 2. config-server.yml
- **Service:** Spring Cloud Config Server
- **Port:** 8888
- **Purpose:** Centralized configuration management
- **Key Settings:**
  - Eureka registration enabled
  - Enhanced management endpoints

#### 3. auth-service.yml
- **Service:** Authentication Service
- **Port:** 8081
- **Purpose:** User authentication and JWT token management
- **Key Settings:**
  - PostgreSQL database: `authdb`
  - Database host: `postgres-db:5432`
  - JPA auto-update enabled
  - Actuator endpoints exposed

#### 4. api-gateway.yml
- **Service:** API Gateway
- **Port:** 8080
- **Purpose:** Single entry point for all client requests
- **Key Settings:**
  - JWT validation via JWKS
  - CORS configuration for frontend
  - Route definitions for all services
  - Load balancing via Eureka

## 🔧 Configuration Structure

```yaml
# Standard configuration pattern
spring:
  application:
    name: service-name
  datasource:           # If needed
    url: jdbc:postgresql://postgres-db:5432/dbname
    username: admin
    password: admin123

management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics

eureka:
  client:
    service-url:
      defaultZone: http://service-registry:8761/eureka/
```

## 🌐 Environment-Specific Profiles

Currently configured for Docker environment:
- Database hosts use Docker service names (e.g., `postgres-db`)
- Eureka uses `service-registry` hostname
- Services communicate via Docker network

### Adding Environment Profiles

To add environment-specific configurations:

1. Create profile-specific files:
   - `auth-service-dev.yml`
   - `auth-service-prod.yml`
   - `auth-service-test.yml`

2. Services can activate profiles via:
   ```yaml
   spring:
     profiles:
       active: dev
   ```

## 🔄 Usage

### In Docker Compose

Services automatically fetch configuration from config-server:

```yaml
auth-service:
  environment:
    SPRING_CONFIG_IMPORT: configserver:http://config-server:8888
    SPRING_APPLICATION_NAME: auth-service
```

### Refresh Configuration

To refresh configuration without restart:

```bash
curl -X POST http://service-host:port/actuator/refresh
```

## 📝 Adding New Services

1. Create a new configuration file: `new-service.yml`
2. Add standard configuration structure
3. Commit and push to this repository
4. Config server will automatically pick up changes
5. Services can refresh configuration via actuator endpoint

## 🔒 Security Notes

- **DO NOT** commit sensitive data (passwords, API keys, etc.)
- Use environment variables for secrets:
  ```yaml
  datasource:
    password: ${DB_PASSWORD:default}
  ```
- For production, use encrypted values with Spring Cloud Config encryption

## 🚀 Deployment

### Pushing Changes

```bash
git add .
git commit -m "Update configuration for [service-name]"
git push origin main
```

### Config Server Auto-Reload

Config server is configured with `clone-on-start: true`, which means:
- On startup, it clones this repository
- Changes are fetched at runtime
- Services must call `/actuator/refresh` to reload

## 📊 Current Services

| Service | Port | Config File | Status |
|---------|------|-------------|--------|
| Service Registry | 8761 | service-registry.yml | ✅ Active |
| Config Server | 8888 | config-server.yml | ✅ Active |
| Auth Service | 8081 | auth-service.yml | ✅ Active |
| API Gateway | 8080 | api-gateway.yml | ✅ Active |

## 🔗 Related Repositories

- **Main Project:** [Ecommerce](https://github.com/sivajeyabalan/ecommerce)
- **Config Server:** [jmart-config-repo](https://github.com/sivajeyabalan/jmart-config-repo)

## 📚 Documentation

For more details, see the main project documentation:
- Architecture documentation
- Service-specific guides
- Deployment instructions

---

**Last Updated:** January 13, 2026  
**Maintainer:** ShopScale Development Team

