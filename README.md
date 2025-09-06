# InventoryPro - API Architecture Planning

## Executive Summary

**InventoryPro** is an inventory management system designed for African retail businesses, tracking products, categories, suppliers, and stock levels across multiple store locations. Retail businesses like Jumia and sole proprietorships (small businesses owned by individuals) need an inventory management system to ensure the right amount of products in the right locations. This system helps in demand forecasting to avoid overstocking, understocking, and manage cash flow in their businesses.

## Business Domain Analysis

**InventoryPro** API supports different functions in the system, such as creating, updating, deleting, and retrieving information.

### Primary Business Entities

The primary entities that businesses need to track or manage and their attributes are:

| Entity | Attributes |
| :---- | :---- |
| **Product** | product_id (Primary Key)<br>name<br>description<br>category_id<br>price<br>supplier_id |
| **Stock/Inventory** | stock_id<br>product_id, store_id, quantity, address |
| **Category** | category_id<br>name<br>description |
| **Supplier** | supplier_id<br>name<br>email<br>address |
| **Customer** | customer_id<br>name<br>email<br>address |
| **Store** | store_id<br>name<br>address |
| **Order** | order_id<br>order_date<br>customer_id<br>store_id<br>amount<br>order_type |

### Relationships Between Entities

- **Product - Category**: One-to-many; each product belongs to exactly one category.
- **Product - Supplier**: Many-to-many; products can have multiple suppliers, and suppliers can have many products.
- **Product - Stock - Store**: Many-to-many; a store can have multiple stocks and products, and products can be available in multiple stores.
- **Order - Customer**: One-to-many; each order is unique and belongs to one customer.
- **Order - Product**: Many-to-many; a product can be in multiple orders, and orders can include many products.
- **Order - Store**: One-to-many; each order is unique to a specific store.

### InventoryPro API Supports Critical Business Operations like:

- Adding, fetching, updating, and deleting product information
- Stock management
- Inventory tracking
- Out of stock alerts
- Customer management
- Order management
- Supplier management

## Resource Architecture Design

Main resources in **InventoryPro** API:

| Resource | Description | Attributes | Data Type | Relationships |
| :---- | :---- | :---- | :---- | :---- |
| **Products** | Items that can be purchased, sold and restocked | productId<br>name<br>description<br>categoryId<br>price | uuid<br>string<br>string<br>uuid<br>decimal | Product - Category, Supplier, Stock |
| **Categories** | Organizes products by type | categoryId<br>name<br>description | uuid<br>string<br>string | Product - Category |
| **Suppliers**  | Supply products to vendors | supplierId<br>name<br>email<br>address | uuid<br>string<br>string<br>string | Product - Supplier |
| **Stores** | Fixed address locations holding stocks | storeId<br>name<br>address | uuid<br>string<br>string | Product - Store, Order - Store |
| **Stocks** | Collection of different products available at a store | stockId<br>productId<br>storeId<br>quantity | uuid<br>uuid<br>uuid<br>integer | Product - Stock, Stock - Store |
| **Customers** | Purchase products and make orders | customerId<br>name<br>email<br>address | uuid<br>string<br>string<br>string | Customer - Order |
| **Orders** | Requests for purchase or restocking | orderId<br>customerId<br>storeId<br>orderDate<br>amount<br>orderType | uuid<br>uuid<br>uuid<br>timestamp<br>decimal<br>string | Customer - Order, Order - Store |

## Endpoint Documentation

| Resource | Action | HTTP Method | URI | Request Body | Success Response | Error Response |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **Products** | Create/Add new product | POST | /products | ```{"productId": uuid, "name": string, "description": string, "categoryId": uuid, "price": decimal}``` | 201 Created Product<br>200 List Product<br>200 Updated Product<br>204 Deleted Product<br>200 Product Details | 400 Bad Request, 400/404 Not Found, 401 Unauthorized |
| | List all products | GET | /products | | 200 List Products | 400 Bad Request |
| | Update product info | PATCH | /products/{productId} | | 200 Updated Product | 400, 404 Not Found |
| | Delete product | DELETE | /products/{productId} | | 204 Deleted Product | 400, 404 Not Found |
| | Read single product details | GET | /products/{productId} | | 200 Product Details | 400, 404 Not Found |
| **Categories** | Create a new category | POST | /categories | ```{ categoryId: uuid, name: string, description: string }``` | 201 Created Category | 400 Bad Request, 404 Not Found |
| | List all categories | GET | /categories | | 200 List Categories | 400 Bad Request |
| | Update a single category | PATCH | /categories/{categoryId} | | 200 Updated Category | 400, 404 Not Found |
| | Retrieve details of category | GET | /categories/{categoryId} | | 200 Category Details | 400, 404 Not Found |
| | Delete a single category | DELETE | /categories/{categoryId} | | 204 Deleted Category | 400, 404 Not Found |
| **Suppliers** | Add a new supplier | POST | /suppliers | ```{ supplierId: uuid, name: string, email: string, address: string }``` | 201 Added Supplier | 400 Bad Request, 404 Not Found |
| | List all suppliers | GET | /suppliers | | 200 List Suppliers | 400 Bad Request |
| | Delete a single supplier | DELETE | /suppliers/{supplierId} | | 204 Deleted Supplier | 400, 404 Not Found |
| | Retrieve details of supplier | GET | /suppliers/{supplierId} | | 200 Supplier Details | 400, 404 Not Found |
| | Update supplier details | PATCH | /suppliers/{supplierId} | | 200 Updated Supplier Details | 400, 404 Not Found |
| **Stores** | Add a new store | POST | /stores | ```{ storeId: uuid, name: string, address: string }``` | 201 Added Store | 400 Bad Request, 404 Not Found |
| | Delete a single store | DELETE | /stores/{storeId} | | 204 Deleted Store | 400, 404 Not Found |
| | Update store details | PUT | /stores/{storeId} | | 200 Updated Store Details | 400, 404 Not Found |
| | List all stores | GET | /stores | | 200 List Stores | 400 Bad Request |
| | Details of a single store | GET | /stores/{storeId} | | 200 Store Details | 400, 404 Not Found |
| **Stocks** | Create new stock/inventory | POST | /stocks | ```{ stockId: uuid, productId: uuid, storeId: uuid, quantity: integer }``` | 201 Created Stock | 400 Bad Request, 404 Not Found |
| | Read products available in stock | GET | /stocks | | 200 List Products in Stock | 400 Bad Request |
| | Details of single product in stock | GET | /stocks/{stockId} | | 200 Stock Details | 400, 404 Not Found |
| | Delete stock | DELETE | /stock/{stockId} | | 204 Deleted Stock | 400, 404 Not Found |
| | Update stock details | PATCH | /stock/{stockId} | | 200 Updated Stock Details | 400, 404 Not Found |
| **Customers** | Create a new customer | POST | /customers | ```{ customerId: uuid, name: string, email: string, address: string }``` | 201 Created Customer | 400 Bad Request, 404 Not Found |
| | Update customer details | PATCH | /customers/{customerId} | | 200 Updated Customer Details | 400, 404 Not Found |
| | Delete customer | DELETE | /customer/{customerId} | | 204 Deleted Customer | 400, 404 Not Found |
| | Retrieve customer details | GET | /customers/{customerId} | | 200 Customer Details | 400, 404 Not Found |
| | List all customers | GET | /customers | | 200 List Customers | 400 Bad Request |
| **Orders** | Create a new order | POST | /orders | ```{ orderId: uuid, customerId: uuid, storeId: uuid, orderDate: timestamp, amount: decimal, orderType: string }``` | 201 Created Order | 400 Bad Request, 404 Not Found |
| | Update order details | PUT | /orders/{orderId} | | 200 Updated Order Details | 400, 404 Not Found |
| | Delete order | DELETE | /order/{orderId} | | 204 Deleted Order | 400, 404 Not Found |
| | Update order location | PATCH | /order/{orderId} | | 200 Order Location | 400, 404 Not Found |
| | List all orders | GET | /orders | | 200 List Orders | 400 Bad Request |
| | Get details of a single order | GET | /orders/{orderId} | | 200 Order Details | 400, 404 Not Found |

## Advanced Features

- **Filtering and searching** products by category, supplier, store, or stock availability:
  - `GET /products/categories`
  - `GET /products/suppliers`
  - `GET /products/stores`
  - `GET /products/stocks`
- Listing all suppliers of a certain product:
  - `GET /suppliers/products`
- Listing products supplied by a particular supplier:
  - `GET /products/{supplierId}`
- Listing stocks for certain stores:
  - `GET /stores/{storeId}/stocks`
- **Bulk operations** such as deleting all products in a store:
  - `DELETE /stores/{storeId}/products`

## Design Rationale

This API design can be adjusted to meet the demands of all types of businesses, large or small. This includes features like currency support for different countries, additional endpoints like receipts and tax, and actions that confirm order status such as approved, completed, canceled, and ongoing.
