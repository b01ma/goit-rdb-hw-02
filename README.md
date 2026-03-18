# Database Normalization: 1NF, 2NF, 3NF

This homework demonstrates how one denormalized orders table can be transformed step by step into **First Normal Form (1NF)**, **Second Normal Form (2NF)**, and **Third Normal Form (3NF)**.

The goal is to reduce data duplication, avoid update anomalies, and keep data consistent.

## 0) Initial (unnormalized) table

`initial_table`

- `order_number`
- `item`
- `address`
- `date`
- `client`

### Problem
In one order, there can be multiple items. If items are stored in the same table as order/client data, order details are repeated for each item row.

---

## 1) First Normal Form (1NF)

### Rule
A table is in **1NF** when:
- each column contains atomic (indivisible) values,
- there are no repeating groups or arrays in a single field,
- each row is unique.

### Applied table
`NF_1`

- `order_number`
- `product_name`
- `quantity`
- `address`
- `date`
- `client`

### What changed from initial table
- Repeating `item` values are split into separate rows.
- Each row now represents exactly one product in one order.

### Remaining issue
Order-level data (`address`, `date`, `client`) is still duplicated for each product row in the same order.

---

## 2) Second Normal Form (2NF)

### Rule
A table is in **2NF** when:
- it is already in 1NF,
- every non-key attribute depends on the **whole** primary key (no partial dependency).

This is especially important when a composite key is used, e.g. (`order_id`, `product_name`).

### Applied tables
`orders_nf2`

- `order_id` (PK)
- `order_date`
- `client_name`
- `client_address`

`items_nf2`

- `order_id` (FK → `orders_nf2.order_id`)
- `product_name`
- `quantity`

### What changed from 1NF
- Order-level attributes moved to `orders_nf2`.
- Product rows (line items) moved to `items_nf2`.
- Data that depends only on `order_id` is no longer repeated in each item row.

### Remaining issue
Client and product data are still text attributes and can be duplicated across many orders (`client_name`, `client_address`, `product_name`).

---

## 3) Third Normal Form (3NF)

### Rule
A table is in **3NF** when:
- it is already in 2NF,
- there are no transitive dependencies (non-key attributes depend only on the key, not on other non-key attributes).

### Applied tables
`clients`

- `client_id` (PK)
- `name`
- `address`

`products`

- `product_id` (PK)
- `product_name`

`orders`

- `order_id` (PK)
- `order_date`
- `client_id` (FK → `clients.client_id`)

`order_details`

- `order_id` (FK → `orders.order_id`)
- `product_id` (FK → `products.product_id`)
- `quantity`

### What changed from 2NF
- Client data is isolated in `clients` and referenced by `client_id`.
- Product data is isolated in `products` and referenced by `product_id`.
- `order_details` resolves many-to-many between orders and products.

### Benefits
- Less duplication.
- Better update consistency.
- Clear entity boundaries (clients, orders, products, line items).

---

## Normalization flow summary

- **Initial**: one wide table with repeated order/client values.
- **1NF**: atomic values and one product per row.
- **2NF**: split order header and order items.
- **3NF**: extract independent entities (`clients`, `products`) and connect with foreign keys.

This is exactly the progression represented by your schema diagrams (`NF-1.png`, `NF-2.png`, `NF-3.png`).
