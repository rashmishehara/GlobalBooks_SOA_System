# 📚 GlobalBooks SOA Platform 📖  

GlobalBooks Inc. was facing challenges with a monolithic system that had become slow, rigid, and risky to update. Any small modification required redeploying the whole application, which increased downtime during peak loads. To address this, I re-engineered the platform using **Service-Oriented Architecture (SOA)**, separating it into four dedicated services: **Catalog, Orders, Payments, and Shipping**. Each runs independently, communicates via standard protocols (SOAP, REST, RabbitMQ, BPEL), and is secured with WS-Security (SOAP) and OAuth2 (REST).  

---

## ⚙️ Key Features  

- **Hybrid Service Model** – Smooth interaction of SOAP- and REST-based services  
- **Asynchronous Messaging** – RabbitMQ as the backbone for enterprise integration  
- **End-to-End Security** – OAuth2 for REST APIs, WS-Security for SOAP services  
- **Elastic Scaling** – Dockerized microservices, deployable on Kubernetes  
- **Observability** – Health probes and performance metrics included  
- **Comprehensive Docs** – APIs, architecture, and workflow documentation provided  
- **Robust Testing** – Automated unit, integration, and performance test suites  

---

## 🪩 Architecture Blueprint  

### 📌 Core Services  

1. **Catalog Service (SOAP)** – Port 8081  
   - Framework: Java Spring Boot with JAX-WS  
   - Protocol: SOAP secured by WS-Security  
   - Capabilities:  
     * Book catalog management  
     * Availability & price lookups  
     * Category browsing & search  
     * Inventory synchronization  
   - Exposes: WSDL for client discovery  

2. **Orders Service (REST)** – Port 8088  
   - Framework: Java Spring Boot  
   - Security: OAuth2  
   - Capabilities:  
     * Full order lifecycle handling  
     * Order history & tracking  
     * Orchestration of business logic  
     * Publishes domain events  
   - Integrates with RabbitMQ  

3. **Payments Service (REST)** – Port 8083  
   - Framework: Java Spring Boot  
   - Capabilities:  
     * Payment initiation & processing  
     * Multi-method payment options  
     * Transaction & refund management  
     * PCI-DSS ready for compliance  

4. **Shipping Service (REST)** – Port 8084  
   - Framework: Java Spring Boot  
   - Capabilities:  
     * Shipment creation & tracking  
     * Delivery time estimation  
     * Integration with multiple carriers  
     * Address validation & updates  

---

### 📌 Integration Components  

1. **RabbitMQ**  
   - Works as ESB backbone  
   - Supports async communication  
   - DLQ for failed events  
   - Reliable message persistence  

2. **BPEL Orchestration**  
   - Coordinates fulfillment process  
   - Handles distributed transactions  
   - Provides error compensation flows  
   - Enables monitoring of business processes  

3. **Security Stack**  
   - OAuth2 + JWT for REST APIs  
   - WS-Security (UsernameToken) for SOAP  
   - RBAC for role-level access  
   - Keycloak integration for IAM  

---

## 🖥️ System Setup Requirements  

### Development  
- JDK 17+  
- Maven 3.9+  
- Docker 20.10+ & Docker Compose 2.0+  
- Git 2.0+  
- At least 8GB RAM (16GB recommended)  
- 20GB free disk space  

### Production  
- Kubernetes cluster v1.20+  
- Per node: 16GB RAM, 4 CPU cores, 50GB storage  
- Load Balancer & Persistent Volumes enabled  

---

## ♨️ Quick Installation Guide  

1. **Get the Code**  
   ```bash
   # Clone the repository
   git clone <repository-url>
   cd SOA

   # Setup environment variables
   cp .env.example .env
   # Edit .env with your configs
   ```  

2. **Build & Deploy**  
   ```bash
   # Build all services
   ./13-scripts/build-all-services.sh

   # Deploy using Docker Compose
   cd 10-deployment
   docker compose up -d --build
   ```  

3. **Check Service Health**  
   ```bash
   # Run health check script
   ./13-scripts/verify-deployment.sh
   ```  
   Or visit:  
   - Catalog WSDL → http://localhost:8081/soap/catalog?wsdl  
   - Orders → http://localhost:8088/api/v1/orders/health  
   - Payments → http://localhost:8083/api/v1/payments/health  
   - Shipping → http://localhost:8084/api/v1/shippings/health  
   - RabbitMQ → http://localhost:15672 (admin/password123)  

4. **View Docs**  
   - API Docs: http://localhost:8082/swagger-ui.html  
   - Diagrams: `01-design-artifacts/architecture-diagrams/`  
   - Integration Guide: `11-documentation/integration-guide.md`  

---

## ⏺️ Service Endpoints  

### Orders REST API  

```
POST   /api/v1/orders                    # Create new order
GET    /api/v1/orders                    # Get all orders
GET    /api/v1/orders/{id}               # Get order by ID
PUT    /api/v1/orders/{id}/status        # Update order status
DELETE /api/v1/orders/{id}               # Cancel order
GET    /api/v1/orders/health             # Health check
```

### Payments REST API  

```
POST   /api/v1/payments/initiate         # Initiate payment
POST   /api/v1/payments/{id}/process     # Process payment
GET    /api/v1/payments/{id}             # Get payment details
GET    /api/v1/payments/order/{orderId}  # Get payments by order
GET    /api/v1/payments/health           # Health check
```

### Shipping REST API  

```
POST   /api/v1/shippings                 # Create shipping
POST   /api/v1/shippings/{id}/process    # Process shipping
GET    /api/v1/shippings/{id}            # Get shipping details
GET    /api/v1/shippings/tracking/{num}  # Track shipment
GET    /api/v1/shippings/health          # Health check
```

### Catalog SOAP API  
- WSDL: `http://localhost:8081/soap/catalog?wsdl`  
- Operations: `searchBooks`, `getBookById`, `getBookPrice`, `checkAvailability`  

---

## 📩 Event Workflow  

1. **Order Creation**: Client submits an order via Orders Service REST API
2. **Event Publishing**: Orders Service publishes events to RabbitMQ  
3. **Payment Processing**: Payments Service processes payment event  
4. **Shipping Processing**: Shipping Service handles shipment event  
5. **Orchestration**: BPEL manages orchestration of the overall flow  

---

## 🔐 Security  

- Current:  
  * REST → OAuth2 with JWT  
  * SOAP → Basic Auth (WS-Security optional)  
  * Keycloak available for identity management  

- Upcoming:  
  * Full WS-Security (UsernameToken) for SOAP  
  * Advanced OAuth2 configs  
  * Mutual TLS between services  

---

## ❓ Testing  

### Postman Collections
- `09-testing/postman/GlobalBooks-SOA-Catalog.postman_collection.json`
- `09-testing/postman/GlobalBooks_Orders_API.postman_collection.json`

### SOAP UI Tests
- `09-testing/soap-ui/CatalogService-TestSuite.xml`

---

## 🔀 Development Setup  

### Setting Up Development Environment

1. **Install Required Tools**
   ```bash
   # Verify Java 17+ installation
   java -version
   
   # Verify Maven 3.9+
   mvn -version
   
   # Verify Docker
   docker --version
   docker-compose --version
   ```

2. **Configure Environment**
   ```bash
   # Set up development environment
   cd 13-scripts
   ./setup-environment.sh
   ```

### Building Individual Services

```bash
# Catalog Service
cd 02-catalog-service
mvn clean package

# Orders Service
cd 03-orders-service
mvn clean package

# Payments Service
cd 04-payments-service
mvn clean package

# Shipping Service
cd 05-shipping-service
mvn clean package
```

### Running Services Locally
   ```bash
   # Catalog Service (Port 8081)
   cd 02-catalog-service
   mvn spring-boot:run

   # Orders Service (Port 8088)
   cd 03-orders-service
   mvn spring-boot:run

   # Payments Service (Port 8083)
   cd 04-payments-service
   mvn spring-boot:run

   # Shipping Service (Port 8084)
   cd 05-shipping-service
   mvn spring-boot:run
   ```

3. **Verify Services**
   ```bash
   # Check health endpoints
   curl http://localhost:8081/soap/catalog?wsdl
   curl http://localhost:8088/api/v1/orders/health
   curl http://localhost:8083/api/v1/payments/health
   curl http://localhost:8084/api/v1/shippings/health
   ```

### Development Best Practices

1. **Code Quality**
   - Run static code analysis:
     ```bash
     mvn sonar:sonar
     ```
   - Format code before commit:
     ```bash
     mvn formatter:format
     ```

2. **Testing**
   - Run unit tests:
     ```bash
     mvn test
     ```
   - Run integration tests:
     ```bash
     mvn verify
     ```
   - Load test scripts available in `09-testing/performance/`

3. **Documentation**
   - Update API documentation in `11-documentation/api-docs/`
   - Generate updated API docs:
     ```bash
     mvn javadoc:javadoc
     ```

---

## 📈 Monitoring  

- Health endpoints on each service  
- RabbitMQ dashboard for messaging stats  
- Spring Boot Actuator for metrics collection  

---

## 🔄 Deployment Options  

- **Docker** → `docker compose up -d --build`  
- **Kubernetes** → manifests in `10-deployment/kubernetes/`  

---

## 🧮 Architecture Patterns Demonstrated  

- **Service-Oriented Architecture (SOA)**
- **Enterprise Service Bus (ESB)**
- **Event-Driven Architecture**
- **Microservices**
- **API Gateway Pattern**
- **Circuit Breaker Pattern**
- **Saga Pattern** (via BPEL orchestration)

---

## 🛠️ Technologies Used  

- **Java 17** - Primary programming language
- **Spring Boot** - Application framework
- **Spring Web Services** - SOAP implementation
- **RabbitMQ** - Message broker
- **Docker** - Containerization
- **OAuth2** - REST API security
- **WS-Security** - SOAP security
- **BPEL** - Business process orchestration
- **H2 Database** - In-memory database for development

---

## ✏️ Project Structure 

<details>
<summary>01-design-artifacts/ – Architecture and Design Documentation</summary>

- `architecture-diagrams/` – System architecture visuals  
- `governance-policy.md` – SOA governance guidelines  
- `soa-design-document.md` – Detailed design specifications  

</details>

<details>
<summary>02-catalog-service/ – SOAP-based Catalog Service</summary>

- `src/` – Service source code  
- `Dockerfile` – Container configuration  
- `pom.xml` – Maven configuration  

</details>

<details>
<summary>03-orders-service/ – REST-based Orders Service</summary>

- `src/` – Service implementation  
- `Dockerfile` – Container configuration  
- `pom.xml` – Maven build config  

</details>

<details>
<summary>04-payments-service/ – Payment Processing Service</summary>

- `src/` – Service implementation  
- `Dockerfile` – Container configuration  
- `pom.xml` – Maven build config  

</details>

<details>
<summary>05-shipping-service/ – Shipping Management Service</summary>

- `src/` – Service implementation  
- `Dockerfile` – Container configuration  
- `pom.xml` – Maven build config  

</details>

<details>
<summary>06-bpel-orchestration/ – Business Process Orchestration</summary>

- `deployment/` – BPEL deployment configs  
- `processes/` – BPEL process definitions  
- `wsdl/` – Service WSDL files  

</details>

<details>
<summary>07-integration/ – Integration Components</summary>

- `consumers/` – Message consumers  
- `producers/` – Event producers  
- `rabbitmq/` – Message broker configs  

</details>

<details>
<summary>08-security/ – Security Configurations</summary>

- `certificates/` – SSL/TLS certificates  
- `oauth2/` – OAuth2 configurations  
- `ws-security/` – WS-Security settings  

</details>

<details>
<summary>09-testing/ – Testing Resources</summary>

- `integration-tests/` – Integration test suites  
- `performance/` – Performance test scripts  
- `postman/` – API test collections  
- `soap-ui/` – SOAP service tests  

</details>

<details>
<summary>10-deployment/ – Deployment Configurations</summary>

- `docker-compose.yml` – Local deployment  
- `kubernetes/` – K8s manifests  
- `cloud/` – Cloud-specific configs  
- `scripts/` – Deployment automation  

</details>

<details>
<summary>11-documentation/ – System Documentation</summary>

- `api-docs/` – API specifications  

</details>

<details>
<summary>12-reports/ – Project Reports</summary>

- `viva-presentation/` – Project presentations  

</details>

<details>
<summary>13-scripts/ – Utility Scripts</summary>

- `setup-environment.sh` – Environment setup  
- `test-soa-workflow.sh` – Workflow testing  

</details>

```plaintext
├── 01-design-artifacts/          # Architecture and Design Documentation
│   ├── architecture-diagrams/    # System architecture visuals
│   ├── governance-policy.md      # SOA governance guidelines
│   └── soa-design-document.md    # Detailed design specifications
│
├── 02-catalog-service/           # SOAP-based Catalog Service
│   ├── src/                      # Service source code
│   ├── Dockerfile                # Container configuration
│   └── pom.xml                   # Maven configuration
│
├── 03-orders-service/            # REST-based Orders Service
│   ├── src/                      # Service implementation
│   ├── Dockerfile                # Container configuration
│   └── pom.xml                   # Maven build config
│
├── 04-payments-service/          # Payment Processing Service
│   ├── src/                      # Service implementation
│   ├── Dockerfile                # Container configuration
│   └── pom.xml                   # Maven build config
│
├── 05-shipping-service/          # Shipping Management Service
│   ├── src/                      # Service implementation
│   ├── Dockerfile                # Container configuration
│   └── pom.xml                   # Maven build config
│
├── 06-bpel-orchestration/        # Business Process Orchestration
│   ├── deployment/               # BPEL deployment configs
│   ├── processes/                # BPEL process definitions
│   └── wsdl/                     # Service WSDL files
│
├── 07-integration/               # Integration Components
│   ├── consumers/                # Message consumers
│   ├── producers/                # Event producers
│   └── rabbitmq/                 # Message broker configs
│
├── 08-security/                  # Security Configurations
│   ├── certificates/             # SSL/TLS certificates
│   ├── oauth2/                   # OAuth2 configurations
│   └── ws-security/              # WS-Security settings
│
├── 09-testing/                   # Testing Resources
│   ├── integration-tests/        # Integration test suites
│   ├── performance/              # Performance test scripts
│   ├── postman/                  # API test collections
│   └── soap-ui/                  # SOAP service tests
│
├── 10-deployment/                # Deployment Configurations
│   ├── docker-compose.yml        # Local deployment
│   ├── kubernetes/               # K8s manifests
│   ├── cloud/                    # Cloud-specific configs
│   └── scripts/                  # Deployment automation
│
├── 11-documentation/             # System Documentation
│   └── api-docs/                 # API specifications
│
├── 12-reports/                   # Project Reports
│   └── viva-presentation/        # Project presentations
│
└── 13-scripts/                   # Utility Scripts
    ├── setup-environment.sh      # Environment setup
    └── test-soa-workflow.sh      # Workflow testing
```

---

## 🔃 Contributing  

1. Fork this repo  
2. Create a new feature branch  
3. Commit your changes with tests  
4. Open a pull request for review  

---

## 🔖 Final Notes  

This project illustrates how an enterprise-grade book ordering system can be built using **SOA principles combined with modern microservice practices**. With secure SOAP and REST services, RabbitMQ-based event-driven messaging, and BPEL-driven orchestration, the platform demonstrates scalability, fault tolerance, and maintainability. Complete documentation, automated testing, and Docker/Kubernetes deployment support make it a strong reference for building **robust distributed systems** in the enterprise domain.  
