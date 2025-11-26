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

## ORDERS

## *-Create Orders*

<img width="757" height="697" alt="image" src="https://github.com/user-attachments/assets/347653fb-02e6-4783-9cca-7c6285a80766" />

## Manejo de Excepciones

<img width="930" height="672" alt="image" src="https://github.com/user-attachments/assets/e6d5958f-f08f-448b-a5e5-a92a3458b21b" />
