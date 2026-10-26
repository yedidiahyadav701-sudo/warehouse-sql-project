# Warehouse Management System — Project Overview

## 1. Introduction

The **Warehouse Management System (Easy Version)** is a small database project built with MySQL. It models the basic movement of products through warehouses: suppliers send goods in, the business stores those goods as inventory, and customers receive goods through dispatches.

The project is designed as a learning example. It uses six related tables and demonstrates common SQL features, including primary and foreign keys, joins, views, stored procedures, and triggers. Sample records and demonstration queries are included so the database can be explored after setup.

### Project goals

- Keep a current stock quantity for every product.
- Record where products are stored and who supplies or receives them.
- Increase stock when goods arrive and decrease it when goods are dispatched.
- Prevent dispatches that exceed available stock.
- Provide simple reports for stock, low inventory, warehouses, and dispatches.

## 2. Database structure

The database is named `warehouse_easy`. Each table has an auto-incrementing ID as its primary key. Foreign keys connect stock movements to the products, suppliers, and customers they involve.

| Table | Purpose |
|---|---|
| `warehouses` | Stores warehouse names and locations. |
| `suppliers` | Stores supplier names, cities, and phone numbers. |
| `customers` | Stores customer names and cities. |
| `products` | Stores product names, prices, current stock, reorder levels, and the warehouse assigned to each product. |
| `inbound` | Records quantities received from suppliers and the dates they arrived. |
| `outbound` | Records quantities dispatched to customers and the dispatch dates. |

A product is assigned to one warehouse. The `inbound` and `outbound` tables act as movement history: they record individual receipts and dispatches, while `products.stock_qty` holds the current quantity available.

## 3. How stock is managed

Three triggers keep product quantities in sync with movement records:

1. **Inbound stock trigger** — after a row is added to `inbound`, it adds that row’s quantity to the product’s stock.
2. **Stock check trigger** — before a row is added to `outbound`, it checks whether enough stock is available. If not, MySQL raises an `Insufficient stock` error and rejects the insert.
3. **Outbound stock trigger** — after a valid outbound row is added, it subtracts the dispatched quantity from stock.

The project also provides three stored procedures:

- `sp_receive_stock` records a receipt using the current date. The inbound trigger updates stock automatically.
- `sp_dispatch_stock` checks stock and records a dispatch only when enough units are available. The trigger provides a second safeguard for direct inserts as well.
- `sp_warehouse_stock` lists products in a selected warehouse, with quantities and their stock value.

## 4. Reports and SQL examples

Four views provide reusable reports:

- `vw_stock_details` shows each product, warehouse, stock level, reorder level, and stock value.
- `vw_low_stock` lists products whose quantity is at or below the reorder level.
- `vw_warehouse_summary` totals the products, units, and stock value for each warehouse.
- `vw_outbound_details` combines dispatch, customer, and product information and calculates the dispatch amount.

The script also includes join queries that show products with their warehouses, dispatch details, warehouses without products, customers without dispatches, products never dispatched, and total quantities received by supplier. These examples demonstrate inner joins, left joins, filtering, grouping, and aggregate functions.

## 5. Setup and demo

Run the complete SQL script in **MySQL Workbench** or another MySQL client that supports stored procedures and triggers. It creates the `warehouse_easy` database, builds the tables and database objects, inserts sample data, and runs example queries.

> **Important:** The script begins with `DROP DATABASE IF EXISTS warehouse_easy`. Running it again deletes and recreates that database, including any data you added to it. Back up any changes you want to keep before rerunning the full script.

The sample data starts with products at zero stock, then adds inbound and outbound movements. The triggers calculate the resulting stock. The final demo calls the procedures to receive stock, attempt a dispatch that exceeds available stock, make a valid dispatch, and view warehouse stock and reports.

## 6. Learning outcomes and scope

This project is useful for practicing relational database design and understanding how database objects can enforce business rules. It shows why stock changes should be tied to recorded movements and how views can make repeated reporting queries easier to reuse.

This is an educational, single-database example rather than a complete production inventory application. It does not include user accounts, permissions, multi-item orders, updates or cancellations of historical movements, or application screens. Its focus is the core SQL workflow for tracking simple inventory.
