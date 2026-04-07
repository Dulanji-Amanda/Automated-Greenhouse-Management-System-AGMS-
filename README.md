# Automated Greenhouse Management System (AGMS)

This README explains how to start the AGMS infrastructure in the correct order and verify all services are registered as `UP` in Eureka.

## 1) Prerequisites

- Java 21+
- Maven Wrapper support (`mvnw.cmd` is already included in each Java service)
- Node.js 18+ and npm
- Python 3.10+
- MongoDB running locally on `mongodb://127.0.0.1:27017` (used by `crop-service`)
- PostgreSQL running locally (used by `zone-service`, DB details are loaded from Config Server)
- Internet access (Config Server pulls from `https://github.com/Dulanji-Amanda/agms-config-repo.git`)

## 2) Open terminals at project root

Use one terminal per service (recommended), then run the commands below in order.

## 3) Start core infrastructure (in order)

### Step 1: Eureka Server (port `8761`)
```powershell
Set-Location "D:\AD2 Project\Automated-Greenhouse-Management-System-AGMS-\Eureka_Server"
.\mvnw.cmd spring-boot:run
```

### Step 2: Config Server (port `8888`)
```powershell
Set-Location "D:\AD2 Project\Automated-Greenhouse-Management-System-AGMS-\config_server"
.\mvnw.cmd spring-boot:run
```

### Step 3: API Gateway (port `8080`)
```powershell
Set-Location "D:\AD2 Project\Automated-Greenhouse-Management-System-AGMS-\API_Gateway"
.\mvnw.cmd spring-boot:run
```

### Step 4: Zone Service (config from Config Server)
```powershell
Set-Location "D:\AD2 Project\Automated-Greenhouse-Management-System-AGMS-\zone-service"
.\mvnw.cmd spring-boot:run
```

## 4) Start Node.js services

### Step 5: Automation Service (port `8083`)
```powershell
Set-Location "D:\AD2 Project\Automated-Greenhouse-Management-System-AGMS-\automation-service"
npm install
npx ts-node index.ts
```

### Step 6: Crop Service (port `8084`)
```powershell
Set-Location "D:\AD2 Project\Automated-Greenhouse-Management-System-AGMS-\crop-service"
npm install
npx ts-node index.ts
```

## 5) Start Python sensor service

### Step 7: Sensor Service (port `8082`)
```powershell
Set-Location "D:\AD2 Project\Automated-Greenhouse-Management-System-AGMS-\sensor-service"
pip install flask py-eureka-client apscheduler requests
python app.py
```

## 6) Verify infrastructure is healthy

- Eureka Dashboard: `http://localhost:8761`
- Config Server health endpoint: `http://localhost:8888/actuator/health` (if actuator is enabled in config)
- API Gateway base URL: `http://localhost:8080`

Expected registered services in Eureka (names may appear uppercase):
- `CONFIG-SERVER`
- `API-GATEWAY`
- `ZONE-SERVICE` (or the config-repo override name if configured there)
- `AUTOMATION-SERVICE`
- `CROP-INVENTORY-SERVICE`
- `SENSOR-SERVICE`

All should show status `UP`.

## 7) Required Eureka Dashboard evidence (`UP` services)

The required dashboard screenshot is included below:

![Eureka Dashboard showing AGMS services as UP](docs/Eureka%20Dashboard.png)
