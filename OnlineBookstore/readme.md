
# Online Bookstore System Design
:books: :notebook: :book: 

## Problem Statement
Design online bookstore LLD
- **Entities and Relationships**: What classes and methods would you have to deal with for books, patrons, and orders? How would you interrelate these entities?
- **Searching**: How will you allow books to be searched for by title, author, subject, etc.? What sort of algorithms do you employ for this searching to be efficient?
- **Inventory Management**: How will you manage book inventory in regards to stock and restocking levels?
- **Order Processing**: How would you handle order placing, processing payment, and proceeding to order fulfillment?


This provides a comprehensive overview of the Online Bookstore system design, including entity relationships, searching, inventory management, and order processing workflows.
The diagrams and detailed class definitions ensure clarity and structure for the system.

## Entities and Relationships

### Classes

#### Book
- Attributes:
  - `String id`
  - `String title`
  - `String author`
  - `String subject`
  - `double price`
  - `int stock`
- Methods:
  - `updateStock(int quantity)`
  - `getDetails()`

#### Patron
- Attributes:
  - `String id`
  - `String name`
  - `String email`
  - `List<Order> orders`
- Methods:
  - `placeOrder(Order order)`
  - `viewOrderHistory()`
  - `searchBook(String query)`

#### Order
- Attributes:
  - `String id`
  - `Patron patron`
  - `List<OrderItem> items`
  - `double totalPrice`
  - `String status`
  - `Address shippingAddress`
- Methods:
  - `calculateTotalPrice()`
  - `updateStatus(String status)`

#### OrderItem
- Attributes:
  - `Book book`
  - `int quantity`
  - `double price`
- Methods:
  - `calculatePrice()`

#### Inventory
- Attributes:
  - `List<Book> books`
- Methods:
  - `updateStock(Book book, int quantity)`
  - `getBookById(String id)`

#### Payment
- Attributes:
  - `String id`
  - `Order order`
  - `String paymentMethod`
  - `boolean isSuccessful`
- Methods:
  - `processPayment()`
  - `refundPayment()`

#### Address
- Attributes:
  - `String street`
  - `String city`
  - `String state`
  - `String zipCode`
  - `String country`
- Methods:
  - `getFullAddress()`

## Searching

### Search Algorithms
- Use a search engine library like Apache Lucene for efficient text searching.
- Implement a full-text search index for `Book` attributes such as title, author, and subject.
- Use a combination of exact match and fuzzy search to improve search accuracy.

### Search Functionality
- `Patron.searchBook(String query)`
  - Splits the query into tokens.
  - Searches the index for matching books based on title, author, and subject.
  - Returns a list of `Book` objects that match the query.

## Inventory Management

### Inventory Management
- Each `Book` object has a `stock` attribute indicating the number of available copies.
- `Inventory.updateStock(Book book, int quantity)`
  - Adjusts the `stock` level of the given `Book`.
  - If the `stock` falls below a threshold, triggers a restocking process.
- Implement a scheduled task to check stock levels and initiate restocking.

## Order Processing

### Order Processing Workflow

#### Placing an Order
- `Patron.placeOrder(Order order)`
  - Adds the `Order` to the patron’s order history.
  - Reduces the stock levels of the ordered books in the `Inventory`.

#### Processing Payment
- `Payment.processPayment()`
  - Validates payment details.
  - If successful, updates the order status to "Paid".
  - If unsuccessful, updates the order status to "Payment Failed".

#### Order Fulfillment
- `Order.updateStatus(String status)`
  - Changes the status of the order (e.g., "Processing", "Shipped", "Delivered").
- Triggers notifications to the patron regarding order status updates.

## Class Diagram

```plaintext
+----------------+       +----------------+       +----------------+       +----------------+       +----------------+
|     Book       |       |    Patron      |       |     Order      |       |   OrderItem    |       |    Inventory   |
+----------------+       +----------------+       +----------------+       +----------------+       +----------------+
| - id           |1     n| - id           |1     n| - id           |1     n| - book         |       | - books        |
| - title        |-------| - name         |-------| - patron       |-------| - quantity     |       +----------------+
| - author       |       | - email        |       | - items        |       | - price        |
| - subject      |       | - orders       |       | - totalPrice   |       +----------------+
| - price        |       +----------------+       | - status       |       
| - stock        |                               | - shippingAddress|       
+----------------+                               +----------------+       

+----------------+       +----------------+
|    Payment     |       |    Address     |
+----------------+       +----------------+
| - id           |       | - street       |
| - order        |       | - city         |
| - paymentMethod|       | - state        |
| - isSuccessful |       | - zipCode      |
+----------------+       | - country      |
                         +----------------+
```

## Sequence Diagram for Placing an Order

```plaintext
Patron -> Inventory: searchBook(String query)
Inventory -> Patron: return list of books
Patron -> Order: create order with selected books
Order -> Payment: processPayment()
Payment -> Order: update order status to "Paid"
Order -> Inventory: update stock levels
Order -> Patron: notify order confirmation
```

## Sequence Diagram for Processing an Order

```plaintext
Patron -> Order: view order status
Order -> Inventory: check stock
Inventory -> Order: confirm stock availability
Order -> Payment: process payment
Payment -> Order: update status to "Paid"
Order -> Patron: notify order status
```