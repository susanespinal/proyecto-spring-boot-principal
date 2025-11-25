# proyecto-spring-boot-principal

## Configuraciones:
 Configuraciones a tomar en cuenta
  
### 1.- Spring
- server:
  
  **port:** 9090 (Products)
  
  **port:** 9091 (Orders)

  **port:** 9093 (Inventory)
  
### 2.- Postgres
- port:
      - "3000"

### 2.- Kafka
- port:
      - "9092"
  
## Creacción de base de datos (Adicionales) 

- Creación base de datos independientes **Orders**
  
```CREATE DATABASE ecommerce_orders;```

```CREATE DATABASE ecommerce_inventory;```

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

### Aquitectura Inventory
```
inventory/
 ├── controller/
 │    └── InventoryController.java
 ├── service/
 │    └── InventoryService.java
 ├── dto/
 │    ├── InventoryItemCreateRequest.java
 │    ├── InventoryItemUpdateRequest.java
 │    └── InventoryItemResponse.java
 ├── mapper/
 │    └── InventoryMapper.java
 ├── entity/
 │    └── InventoryItem.java
 └── repository/
      └── InventoryItemRepository.java
```

### Arquitectura final
3 microservicios
```
┌─────────────────────┐      ┌─────────────────────┐      ┌─────────────────────┐
│  product-service    │      │   order-service     │      │ inventory-service   │
│  (puerto 9090)      │      │   (puerto 8081)     │      │  (puerto 8082)      │
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

## Manejo de Excepciones

<img width="930" height="672" alt="image" src="https://github.com/user-attachments/assets/e6d5958f-f08f-448b-a5e5-a92a3458b21b" />
