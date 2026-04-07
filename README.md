# Automated Greenhouse Management System (AGMS)

AGMS is a cloud-native, polyglot microservices platform for greenhouse automation. It ingests live IoT telemetry, evaluates climate rules, and triggers automated control actions to maintain optimal growing conditions.

## Overview

The platform separates responsibilities into independent services:

- Discovery and routing with Spring Cloud infrastructure
- Domain-focused microservices implemented in Java, TypeScript/Node.js, and Python
- Database-per-service pattern (`PostgreSQL` for zone data, `MongoDB` for crop inventory)
- Centralized externalized configuration through Spring Cloud Config

## Architecture and Technology Stack

### Infrastructure Services (Spring Cloud)

- **Service Registry**: Netflix Eureka (`Eureka_Server`, port `8761`)
- **Config Server**: Spring Cloud Config (`config_server`, port `8888`)
- **API Gateway**: Spring Cloud Gateway + JWT filtering (`API_Gateway`, port `8080`)

### Domain Microservices

- **Zone Management Service**: Spring Boot + PostgreSQL (`zone-service`)
- **Sensor Telemetry Service**: Flask + APScheduler (`sensor-service`, port `8082`)
- **Automation and Control Service**: Express + TypeScript (`automation-service`, port `8083`)
- **Crop Inventory Service**: Express + MongoDB (`crop-service`, port `8084`)

## Project Structure

```text
Automated-Greenhouse-Management-System-AGMS-/
|-- API_Gateway/            # Spring Cloud Gateway
|-- Eureka_Server/          # Eureka discovery server
|-- config_server/          # Centralized configuration server
|-- zone-service/           # Zone management microservice (Java)
|-- sensor-service/         # Sensor telemetry microservice (Python)
|-- automation-service/     # Automation rule engine (Node.js/TypeScript)
|-- crop-service/           # Crop inventory microservice (Node.js)
|-- Postman Collection/     # API test collection
|-- docs/                   # Project documentation assets
`-- README.md
```

## Prerequisites

- Java `21+`
- Node.js `18+` and npm
- Python `3.10+`
- MongoDB running locally at `mongodb://127.0.0.1:27017`
- PostgreSQL running locally (Zone service DB details are loaded from Config Server)
- Internet access for Config Server Git source:
  - `https://github.com/Dulanji-Amanda/agms-config-repo.git`

## Startup Order (Windows PowerShell)

Start each service in a separate terminal, in this exact order.

### Phase 1: Infrastructure Services

### 1) Start Eureka Server

```powershell
Set-Location "D:\AD2 Project\Automated-Greenhouse-Management-System-AGMS-\Eureka_Server"
.\mvnw.cmd spring-boot:run
```

### 2) Start Config Server

```powershell
Set-Location "D:\AD2 Project\Automated-Greenhouse-Management-System-AGMS-\config_server"
.\mvnw.cmd spring-boot:run
```

### 3) Start API Gateway

```powershell
Set-Location "D:\AD2 Project\Automated-Greenhouse-Management-System-AGMS-\API_Gateway"
.\mvnw.cmd spring-boot:run
```

### Phase 2: Domain Services

### 4) Start Zone Service

```powershell
Set-Location "D:\AD2 Project\Automated-Greenhouse-Management-System-AGMS-\zone-service"
.\mvnw.cmd spring-boot:run
```

### 5) Start Automation Service

```powershell
Set-Location "D:\AD2 Project\Automated-Greenhouse-Management-System-AGMS-\automation-service"
npm install
npx ts-node index.ts
```

### 6) Start Crop Service

```powershell
Set-Location "D:\AD2 Project\Automated-Greenhouse-Management-System-AGMS-\crop-service"
npm install
npx ts-node index.ts
```

### 7) Start Sensor Service

```powershell
Set-Location "D:\AD2 Project\Automated-Greenhouse-Management-System-AGMS-\sensor-service"
pip install flask py-eureka-client apscheduler requests
python app.py
```

## Service Endpoints and Health Checks

After startup, confirm these URLs:

- Eureka Dashboard: `http://localhost:8761`
- Config Server: `http://localhost:8888`
- API Gateway: `http://localhost:8080`
- Sensor Service check: `http://localhost:8082/api/sensors/latest`
- Automation logs: `http://localhost:8083/api/automation/logs`
- Crop list: `http://localhost:8084/api/crops`

`zone-service` port can be supplied by Config Server; the integration path currently expects `8081` for zone APIs.

## Expected Eureka Registrations

When all services are healthy, Eureka should show entries similar to:

- `CONFIG-SERVER`
- `API-GATEWAY`
- `ZONE-SERVICE` (or config-repo override name)
- `SENSOR-SERVICE`
- `AUTOMATION-SERVICE`
- `CROP-INVENTORY-SERVICE`

Each service status should be `UP`.

## Required Eureka Dashboard Screenshot (UP Services)

![Eureka Dashboard showing AGMS services as UP](docs/Eureka%20Dashboard.png)

## API Testing

Use the included Postman collection:

- `Postman Collection/AGMS.postman_collection.json`

## Troubleshooting

- If `zone-service` fails at startup, verify Config Server is running and PostgreSQL is reachable.
- If Node services fail to register with Eureka, confirm Eureka is available at `http://localhost:8761/eureka`.
- If `sensor-service` starts but no telemetry appears, validate external API reachability and credentials in `sensor-service/app.py`.
- If routes through gateway fail, check service names in Eureka match gateway route `lb://` targets.
