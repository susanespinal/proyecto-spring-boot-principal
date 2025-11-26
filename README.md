# proyecto-spring-boot-principal

## Configuraciones:
 Configuraciones a tomar en cuenta
 
### Variables de entorno
```
${DB_USER} :ecommerce_user
${DB_PASSWORD} :ecommerce_pasword
${DB_HOST} :localhost
${DB_PORT} :3000
${DB_NAME} :ecommerce
${SERVER_PORT} : 9090
```
### HABILITAR LA ANNOTATION PROCESSORS
Realice algunas modificaciones para trabajar con MapStruct en el proyecto Inventory-Service y Order-service
```
 File → Settings → Build, Execution, Deployment → Compiler → Annotation Processors → Enable
```
### 1.- Spring
- server:
  ```
  port: 9090 (Products)
  port: 9091 (Orders)
  port: 9093 (Inventory)
  port: 9094 (Analytics Service)
  ```
### 2.- Postgres
- port:
       "3000"

### 2.- Kafka
- port:
       "9092"

## Repositorios Base de Datos
### GitHub
* Product-Service:  (https://github.com/susanespinal/product-service)

* Inventory-Service:  (https://github.com/susanespinal/inventory-service)

* Orders-Service:  (https://github.com/susanespinal/order-service)
  
## Creacción de base de datos (Adicionales) 

- Creación base de datos independientes **Orders**
  
```CREATE DATABASE ecommerce_orders;```

```CREATE DATABASE ecommerce_inventory;```

## Comando a ejecutar (TOPIC)
```
docker exec -it kafka bash

kafka-topics --bootstrap-server localhost:9092 \
  --create \
  --topic ecommerce.products.created \
  --partitions 5 \
  --replication-factor 1 \
  --config cleanup.policy=compact

kafka-topics --bootstrap-server localhost:9092 \
  --create \
  --topic ecommerce.orders.placed \
  --partitions 5 \
  --replication-factor 1 \
  --config cleanup.policy=compact

kafka-topics --bootstrap-server localhost:9092 \
  --create \
  --topic ecommerce.orders.confirmed \
  --partitions 5 \
  --replication-factor 1 \
  --config cleanup.policy=compact

kafka-topics --bootstrap-server localhost:9092 \
  --create \
  --topic ecommerce.orders.cancelled \
  --partitions 5 \
  --replication-factor 1 \
  --config cleanup.policy=compact

kafka-topics --bootstrap-server localhost:9092 \
  --create \
  --topic ecommerce.inventory.updated \
  --partitions 5 \
  --replication-factor 1 \
  --config cleanup.policy=compact

```
### Nuevas Dependencias
#Inventory-Service
```
    <!-- MapStruct -->
    <dependency>
      <groupId>org.mapstruct</groupId>
      <artifactId>mapstruct</artifactId>
      <version>${mapstruct.version}</version>
    </dependency>
    <dependency>
      <groupId>org.mapstruct</groupId>
      <artifactId>mapstruct-processor</artifactId>
      <version>${mapstruct.version}</version>
      <scope>provided</scope>
    </dependency>

    <!-- Lombok -->
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>${lombok.version}</version>
      <scope>provided</scope>
    </dependency>

    <!-- Lombok + MapStruct binding -->
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok-mapstruct-binding</artifactId>
      <version>0.2.0</version>
    </dependency>
```
### Aquitectura Product-Service
```
product/
 ├── controller/
 │    ├── CategoryController.java
 |    ├── ProductController.java
 |    └── KafkaTestController.java
 ├── dto/
 │    ├── CategoryRequest.java
 │    ├── CategoryResponse.java
 │    ├── ErrorResponse.java
 │    ├── ProductRequest.java
 │    ├── ProductResponse.java
 │    └── ProductSummaryResponse.java
 ├── exceptions/
 │    ├── CategoryAlreadyExistsException.java
 │    ├── GlobalExceptionHandler.java
 │    └── ResourceNotFoundException.java
 ├── kafka/
 |    └── consumer/
 |         └──
 |    └── event/
 |         └── ProductCreatedEvent.java
 |    └── producer/
 |         └── ProductEventProducer
 ├── mapper/
 │    └── ProductMapper.java
 ├── model/
 │    ├── Category.java
 │    └── Product.java
 ├── repository/
 │    ├── CategoryRepository.java
 │    └── ProductRepository.java
 ├── service/
 │    ├── CategoryService.java
 │    └── ProductService.java
 └── ProductServiceApplication.java
      
```

### Aquitectura Inventory-Service
```
inventory/
 ├── controller/
 |    └── InventoryController.java
 ├── dto/
 │    ├── ErrorResponse.java
 │    ├── InventoryItemRequest.java
 │    └── InventoryItemResponse.java
 ├── exceptions/
 │    ├── GlobalExceptionHandler.java
 │    └── ResourceNotFoundException.java
 ├── kafka/
 |    └── consumer/
 |         └── OrderEventConsumer.java
 |    └── event/
 │         ├── InventoryUpdateEvent.java
 │         ├── OrderCancelledEvent.java
 │         ├── OrderConfirmedEvent.java
 │         └── OrderPlacedEvent.java
 |    └── producer/
 |         └── InventoryEventProducer
 ├── mapper/
 │    └── InventoryMapper.java
 ├── model/
 │    └── Entity /
 │         └── InventoryItem.java
 ├── repository/
 │    └── InventoryRepository.java
 ├── service/
 │    └── InventoryService.java
 └── InventoryServiceApplication.java
```

### Aquitectura Orders-Service
```
orders/
 ├── controller/
 |    └── OrdersController.java
 ├── dto/
 │    ├── ErrorResponse.java
 │    ├── OrdersRequest.java
 │    └── OrdersResponse.java
 ├── exceptions/
 │    ├── GlobalExceptionHandler.java
 │    └── ResourceNotFoundException.java
 ├── kafka/
 |    └── consumer/
 |         └── OrderEventConsumer.java
 |    └── event/
 │         ├── OrderCancelledEvent.java
 │         ├── OrderConfirmedEvent.java
 │         └── OrderPlacedEvent.java
 |    └── producer/
 |         └── OrderEventProducer
 ├── mapper/
 │    └── OrderMapper.java
 ├── model/
 │    └── Entity /
 │         ├── Order.java
 │         └── OrderStatusItem.java
 ├── repository/
 │    └── OrderRepository.java
 ├── service/
 │    └── OrderService.java
 └── OrderServiceApplication.java
```

### Arquitectura final
3 microservicios
```
┌─────────────────────┐      ┌─────────────────────┐      ┌─────────────────────┐
│  product-service    │      │   order-service     │      │ inventory-service   │
│  (puerto 9090)      │      │   (puerto 9091)     │      │  (puerto 9093)      │
├─────────────────────┤      ├─────────────────────┤      ├─────────────────────┤
│ PostgreSQL          │      │ PostgreSQL          │      │ PostgreSQL          │
│ ecommerce           │      │ ecommerce_orders    │      │ ecommerce_inventory │
├─────────────────────┤      ├─────────────────────┤      ├─────────────────────┤
│ PRODUCER:           │      │ PRODUCER:           │      │ PRODUCER:           │
│ products.created    │      │ orders.placed       │      │ orders.confirmed    │
│                     │      │                     │      │ orders.cancelled    │
│                     │      │ CONSUMER:           │      │                     │
│                     │      │ orders.confirmed    │      │ CONSUMER:           │
│                     │      │ orders.cancelled    │      │ orders.placed       │
└─────────────────────┘      └─────────────────────┘      └─────────────────────┘
```

## Uso de EndPoint (PostMan)

## PRODUCTS

### *- Create Category*
  
<img width="1160" height="619" alt="image" src="https://github.com/user-attachments/assets/39a56bb8-41f4-49e9-95bb-3e3713f83bdf" />

### *- All Category*
  
<img width="977" height="730" alt="image" src="https://github.com/user-attachments/assets/8edf78e8-1460-4ef1-af54-f173932ca9d7" />

### *- Create Products*

<img width="856" height="746" alt="image" src="https://github.com/user-attachments/assets/f0c1f15d-2bce-49af-afb7-b0eb8edcb397" />

### *- All Products*

<img width="751" height="925" alt="image" src="https://github.com/user-attachments/assets/dd7d2d96-a341-4be0-b0f6-3c1b054b7c62" />

## INVENTORY

###*_Create_*

<img width="697" height="701" alt="image" src="https://github.com/user-attachments/assets/729e429d-40a2-4bf5-83b9-cbee73935121" />

###*_List Inventiry_*

<img width="716" height="735" alt="image" src="https://github.com/user-attachments/assets/81d4ae52-cfdd-4567-9bee-ec31dc6b64a1" />

## ORDERS

## *-Create Orders*

<img width="757" height="697" alt="image" src="https://github.com/user-attachments/assets/347653fb-02e6-4783-9cca-7c6285a80766" />


<img width="1796" height="260" alt="image" src="https://github.com/user-attachments/assets/bbda256b-eafa-42b8-9692-03d163d5e0c5" />


## *-Lista Ordenes*

<img width="586" height="712" alt="image" src="https://github.com/user-attachments/assets/2c01daf9-0222-42d3-a092-1ec7762827f8" />


## Manejo de Excepciones

#_Product_

<img width="930" height="672" alt="image" src="https://github.com/user-attachments/assets/e6d5958f-f08f-448b-a5e5-a92a3458b21b" />

#_Orders_

<img width="862" height="666" alt="image" src="https://github.com/user-attachments/assets/a10c504b-f6c9-4427-8d2c-43fe5592314f" />

<img width="772" height="632" alt="image" src="https://github.com/user-attachments/assets/f175d24e-32c8-49b8-891e-671e1f67bf9a" />

#_Inventory_*

<img width="715" height="613" alt="image" src="https://github.com/user-attachments/assets/004cd603-d3c6-4f59-8d59-8754679ee3ed" />

<img width="692" height="605" alt="image" src="https://github.com/user-attachments/assets/2f3c8ba5-d8e4-4860-babe-0c046ed0e6c8" />

## TOPIC Kafka

<img width="979" height="269" alt="image" src="https://github.com/user-attachments/assets/9c4eb681-72f9-4ffa-ae1a-5c0a2d9a2150" />

