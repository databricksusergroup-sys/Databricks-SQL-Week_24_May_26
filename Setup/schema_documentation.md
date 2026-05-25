# E-Commerce Analytics Database Schema Documentation

This document describes the structure, relationships, and fields of the database tables used in the Databricks SQL Week Workshop.

---

## 📊 Entity-Relationship (ER) Diagram

The diagram below represents the logical database model of the simplified, denormalized workshop schema implemented in the **Setup Notebook**.

---

## 🔑 Key Relationships

1. **Category Hierarchy (Self-Join / Recursive)**:
   * `categories.parent_id` refers back to `categories.id`. This enables infinite nesting of subcategories (e.g., *Electronics* $\rightarrow$ *Laptops* $\rightarrow$ *Gaming Laptops*).
2. **Product Categorization**:
   * `products.category_id` references `categories.id` in a **One-to-Many** relationship (one category contains many products).
3. **Inventory Stock Tracking**:
   * `inventory.product_id` references `products.id` in a **One-to-One / Unique** relationship (each product has exactly one real-time inventory row).
4. **Order Sales Transactions**:
   * `orders.user_id` references `users.id` in a **One-to-Many** relationship (one customer can place multiple orders).
   * `orders.product_id` references `products.id` in a **One-to-Many** relationship (a product can be sold across multiple orders).

---

## 🗂️ Detailed Data Dictionary

### 1. `categories` Table
Stores product categories and subcategories in a hierarchical structure.

| Field Name   | Data Type   | Key / Constraint | Description                                   |
|--------------|-------------|:---------------:|-----------------------------------------------|
| id           | STRING      | **PK**          | Unique identifier for the category.           |
| name         | STRING      | NOT NULL        | Display name of the category.                 |
| slug         | STRING      | NOT NULL        | URL-friendly unique identifier slug.          |
| description  | STRING      |                 | Summary description of the category.          |
| parent_id    | STRING      | **FK**          | ID of the parent category (null for root).    |
| created_at   | TIMESTAMP   |                 | Date and time the category was created.       |
| updated_at   | TIMESTAMP   |                 | Date and time the category was last modified. |

### 2. `users` Table
Stores customer accounts and administrator profiles.

| Field Name    | Data Type   | Key / Constraint | Description                                   |
|---------------|-------------|:---------------:|-----------------------------------------------|
| id            | STRING      | **PK**          | Unique identifier for the user.               |
| email         | STRING      | NOT NULL        | User email address (primary login).           |
| password_hash | STRING      | NOT NULL        | Securely hashed representation of the password.|
| first_name    | STRING      | NOT NULL        | User's first name.                            |
| last_name     | STRING      | NOT NULL        | User's last name.                             |
| role          | STRING      |                 | Account access level (e.g., admin, customer). |
| phone         | STRING      |                 | Contact telephone number.                     |
| created_at    | TIMESTAMP   |                 | Account registration timestamp.               |
| updated_at    | TIMESTAMP   |                 | Account last-modified timestamp.              |

### 3. `products` Table
Stores the catalog of active items for sale.

| Field Name   | Data Type        | Key / Constraint | Description                                   |
|--------------|------------------|:---------------:|-----------------------------------------------|
| id           | STRING           | **PK**          | Unique identifier for the product.            |
| category_id  | STRING           | **FK**          | Foreign key linking to the product's category.|
| name         | STRING           | NOT NULL        | Retail title/name of the product.             |
| slug         | STRING           | NOT NULL        | URL-friendly product path slug.               |
| description  | STRING           |                 | In-depth product specification or details.    |
| price        | DECIMAL(10,2)    |                 | Unit sale price of the item.                  |
| sku          | STRING           |                 | Stock Keeping Unit (unique SKU code).         |
| is_active    | BOOLEAN          |                 | Flag indicating if the product is live.       |
| created_at   | TIMESTAMP        |                 | Timestamp when the product was cataloged.     |
| updated_at   | TIMESTAMP        |                 | Timestamp when the product was updated.       |

### 4. `inventory` Table
Manages real-time stock levels and reordering points.

| Field Name    | Data Type   | Key / Constraint   | Description                                   |
|---------------|-------------|:-----------------:|-----------------------------------------------|
| id            | STRING      | **PK**            | Unique identifier for the inventory record.    |
| product_id    | STRING      | **FK, UNIQUE**    | Foreign key linking back to the product.       |
| quantity      | INT         |                   | Physical stock quantity in warehouses.         |
| reorder_level | INT         |                   | Threshold at which new stock should be ordered.|
| updated_at    | TIMESTAMP   |                   | Last stock audit or inventory adjustment time. |

### 5. `orders` Table
Tracks sales transactions. In this workshop schema, it has been denormalized to contain single-item transactions directly for simplified querying.

| Field Name       | Data Type      | Key / Constraint | Description                                   |
|------------------|---------------|:---------------:|-----------------------------------------------|
| id               | STRING        | **PK**          | Unique transaction ID for the order.          |
| user_id          | STRING        | **FK**          | Foreign key of the customer placing the order.|
| product_id       | STRING        | **FK**          | Foreign key of the purchased product.         |
| quantity         | INT           |                 | Quantity of the product ordered.              |
| unit_price       | DECIMAL(10,2) |                 | Historical price at time of purchase.         |
| total_amount     | DECIMAL(12,2) |                 | Total cost of this order.                     |
| status           | STRING        |                 | Fulfillment status (e.g., delivered, etc.).   |
| shipping_address | STRING        |                 | Delivery address destination.                 |
| billing_address  | STRING        |                 | Financial billing address.                    |
| created_at       | TIMESTAMP     |                 | Timestamp when the order was placed.          |
| updated_at       | TIMESTAMP     |                 | Timestamp of the last order status update.    |

---