# Usage Examples

This guide provides practical examples of how to interact with your SQL Server database using natural language through the MSSQL MCP Server.

## 🚀 Quick Start Conversations

Once configured, interact with your database using natural language:

### Data Exploration

```
You: "Show me all customers from New York"
AI: I'll query the customers table for you.

[Executes: SELECT * FROM customers WHERE city = 'New York']

Found 15 customers from New York:
- John Smith (john@email.com)
- Sarah Johnson (sarah@email.com)
- Mike Davis (mike@email.com)
...
```

```
You: "What are the top 5 best-selling products this month?"
AI: Let me find the top-selling products for you.

[Executes: SELECT TOP 5 p.name, SUM(oi.quantity) as total_sold
FROM products p
JOIN order_items oi ON p.id = oi.product_id
JOIN orders o ON oi.order_id = o.id
WHERE o.order_date >= DATEADD(month, -1, GETDATE())
GROUP BY p.name
ORDER BY total_sold DESC]

Top 5 products this month:
1. Wireless Headphones - 245 units
2. Smartphone Case - 189 units
3. USB Cable - 156 units
...
```

### Data Modification

```
You: "Update all pending orders to processing status"
AI: I'll update the pending orders for you.

[Executes: UPDATE orders SET status = 'processing' WHERE status = 'pending']

Updated 23 orders from 'pending' to 'processing' status.
```

```
You: "Add a new customer: Jane Doe, email jane@example.com, from Boston"
AI: I'll add the new customer to the database.

[Executes: INSERT INTO customers (name, email, city) VALUES ('Jane Doe', 'jane@example.com', 'Boston')]

Successfully added Jane Doe as a new customer.
```

### Schema Management

```
You: "Create a table for storing product reviews with rating and comments"
AI: I'll create a reviews table for you.

[Executes: CREATE TABLE reviews (
  id INT IDENTITY(1,1) PRIMARY KEY,
  product_id INT NOT NULL,
  customer_id INT NOT NULL,
  rating INT CHECK (rating BETWEEN 1 AND 5),
  comment NVARCHAR(1000),
  created_date DATETIME2 DEFAULT GETDATE(),
  FOREIGN KEY (product_id) REFERENCES products(id),
  FOREIGN KEY (customer_id) REFERENCES customers(id)
)]

Created 'reviews' table with rating validation and foreign key constraints.
```

## 🎯 Advanced Usage Patterns

### Complex Analytics

```
You: "Show me monthly sales trends for the last 6 months"
AI: [Executes complex aggregation query with date functions]

Monthly Sales Trends:
- January 2024: $45,230
- February 2024: $52,180
- March 2024: $48,950
- April 2024: $61,420
- May 2024: $58,760
- June 2024: $67,890
```

### Batch Operations

```
You: "Insert these 5 new products with their details"
AI: I'll insert all products in a single transaction.

[Executes: INSERT INTO products (name, price, category, stock) VALUES
('Wireless Mouse', 29.99, 'Electronics', 100),
('USB Hub', 19.99, 'Electronics', 50),
('Desk Lamp', 39.99, 'Office', 25),
('Notebook', 12.99, 'Office', 200),
('Phone Stand', 15.99, 'Electronics', 75)]

Successfully added 5 new products to inventory.
```

### Data Analysis

```
You: "Find customers who haven't placed an order in the last 90 days"
AI: I'll identify inactive customers for you.

[Executes: SELECT c.name, c.email, MAX(o.order_date) as last_order
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
GROUP BY c.id, c.name, c.email
HAVING MAX(o.order_date) < DATEADD(day, -90, GETDATE()) OR MAX(o.order_date) IS NULL]

Found 12 customers with no recent orders - consider re-engagement campaigns.
```

## 🔗 Integration Patterns

### Claude Desktop Integration

```json
{
  "mcpServers": {
    "mssql": {
      "command": "node",
      "args": ["/path/to/mssql-mcp-server/dist/index.js"],
      "env": {
        "SERVER_NAME": "localhost",
        "DATABASE_NAME": "MyDatabase",
        "SQL_USER": "myuser",
        "SQL_PASSWORD": "mypassword",
        "TRUST_SERVER_CERTIFICATE": "true"
      }
    }
  }
}
```

**Example Conversation:**
```
You: "What tables do we have in our database?"
Claude: I'll list all the tables for you.

[Uses list_tables tool]

Your database contains these tables:
- customers (156 rows)
- orders (1,247 rows)
- products (89 rows)
- order_items (3,421 rows)
```

### VS Code Agent Integration

```json
{
  "servers": {
    "mssql": {
      "command": "node",
      "args": ["./dist/index.js"],
      "env": {
        "SERVER_NAME": "myserver.database.windows.net",
        "DATABASE_NAME": "ProductionDB",
        "READONLY": "true"
      }
    }
  }
}
```

**Example Usage:**
```
You: "Describe the structure of the orders table"
VS Code Agent: I'll show you the orders table schema.

[Uses describe_table tool]

orders table structure:
- id (int, PRIMARY KEY, IDENTITY)
- customer_id (int, FOREIGN KEY → customers.id)
- order_date (datetime2, DEFAULT: GETDATE())
- status (varchar(20), CHECK: 'pending', 'processing', 'shipped', 'delivered')
- total_amount (decimal(10,2))
```

## 📊 Real-World Scenarios

### E-commerce Analytics

```
"Show me today's sales by category"
"Find customers who haven't ordered in 30 days"
"Update inventory levels after receiving shipment"
"Create a table for tracking promotional codes"
```

### Customer Support

```
"Find all orders for customer email john@example.com"
"Update order status to refunded for order #12345"
"Show customers with multiple support tickets"
"Add a note to customer record about their preference"
```

### Inventory Management

```
"Show products with low stock levels (less than 10 units)"
"Update product prices for the Electronics category"
"Create an index on the product_name column for faster searches"
"List all products that haven't sold in the last quarter"
```

### Financial Reporting

```
"Calculate total revenue for each month this year"
"Show average order value by customer segment"
"Find the most profitable product categories"
"Generate a sales report for the last quarter"
```

## 🛠️ Tool-Specific Examples

### ReadDataTool Examples

```
"Show me the first 10 customers"
"Find orders placed yesterday"
"Get product details for items under $50"
"List customers from California with their order counts"
```

### InsertDataTool Examples

```
"Add a new product: Gaming Keyboard, $89.99, Electronics category"
"Insert a customer record for Mike Johnson from Seattle"
"Add multiple order items for order #1001"
```

### UpdateDataTool Examples

```
"Update customer email from old@email.com to new@email.com"
"Change order status to 'shipped' for orders placed last week"
"Increase prices by 10% for all products in the Premium category"
```

### CreateTableTool Examples

```
"Create a table for storing employee information"
"Make a table for tracking website analytics"
"Create a log table for audit purposes"
```

### CreateIndexTool Examples

```
"Create an index on customer email for faster lookups"
"Add a composite index on order_date and customer_id"
"Create a unique index on product SKU"
```

### DropTableTool Examples

```
"Drop the temporary_data table"
"Remove the old_customers_backup table"
```

### ListTableTool Examples

```
"Show me all tables in the database"
"List tables in the sales schema"
"What tables do we have?"
```

### DescribeTableTool Examples

```
"Describe the customers table structure"
"Show me the columns in the orders table"
"What's the schema of the products table?"
```

---

**Next Steps:**
- [Advanced Configuration](ADVANCED_CONFIGURATION.md) - Complex setup scenarios
- [Best Practices](BEST_PRACTICES.md) - Performance and security guidance
- [Troubleshooting](TROUBLESHOOTING.md) - Common issues and solutions
