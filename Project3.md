















<div style='font-family: Arial, sans-serif;
        text-align: center;
        padding: 50px;'>
  <h1>Project 3</h1>
	<p><strong>WolfWR</strong></p>
  <p>
    Group M
  </p>
  <p> Min-Ting Tu(mtu), Yung-Hsuan Teng(yteng2), Tzu-Tang Liu(tliu33), Xiaoqin Pi(xpi)
</p>
  <p>April 8th, 2025</p>
</div>
<div style="page-break-after: always; break-after: page;"></div>

<div style="page-break-after: always; break-after: page;"></div>





#### **Assumptions:**

1. Membership can only be signed up/ cancel in store
2. Yearly membership
3. Membership levels can’t be changed during the membership period
4. The signed up and cancel process can only be handled by one staff
5. The same product would supplied by the same supplier
6. Each store has most one inventory record
7. All transactions are done by cashier (no self checkout)
8. Staff can only be added or removed by Admin
9. Same merchandise has same production data and expiration date it won’t change
10. All transactions have a unique transaction number, and each merchandise has its own transaction





#### Transaction:

Transaction 1:

```sql
/*
===========================================================
Procedure Name : p_createOrders
Create Date    : 2025-04-06
Last Modified  : 2025-04-06
Goal           : Create a new order and update the store's inventory accordingly.
Input Params   : 
                 p_storeID INT(11)   - Store identifier
                 p_supplierID INT(11)- Supplier identifier
                 p_productID INT(11) - Product identifier
                 p_staffID INT(11)   - Staff identifier responsible for the order
                 p_quantity INT(11)  - Quantity ordered
                 p_price DECIMAL(10,2)- Buy price per unit
Output         : Inserts a new order in the Orders table and either updates or creates a record in Inventory.
===========================================================
*/
CREATE PROCEDURE p_createTransaction(
    IN pStoreID INT,
    IN pCustomerID INT,
    IN pCashierID INT,
    IN pProductID INT,
    IN pQuantity INT
)
BEGIN
    -- Local variables
    DECLARE vPrice DECIMAL(10,2);
    DECLARE vDiscount DECIMAL(10,2);
    DECLARE vTotalPrice DECIMAL(10,2);
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    BEGIN
        ROLLBACK;
    END;
    
    START TRANSACTION;
    -- 1) Get Price from Merchandise
    SELECT Price INTO vPrice FROM Merchandise WHERE ProductID = pProductID;
    -- 2) Get Discount from Inventory
    SELECT DiscountInfo INTO vDiscount FROM Inventory WHERE StoreID = pStoreID AND ProductID = pProductID;
    -- 3) Calculate totalPrice
    --    For example: totalPrice = price * quantity * discount
    SET vTotalPrice = vPrice * pQuantity * (100 - vDiscount) / 100;
    -- 4) Insert into Transaction
    INSERT INTO `Transaction` (StoreID, CustomerID, StaffID, ProductID, Quantity, TotalPrice, PurchaseDate
    )
    VALUES (pStoreID, pCustomerID, pCashierID, pProductID, pQuantity, vTotalPrice, NOW());
    -- 5) Update Inventory
    UPDATE Inventory SET Quantity = Quantity - pQuantity WHERE StoreID = pStoreID AND ProductID = pProductID;
    COMMIT;
END $$ 
```



Transaction 2

```sql
/*
===========================================================
Procedure Name : p_createTransaction
Create Date    : 2025-04-06
Last Modified  : 2025-04-06 
Goal           : Record a sales transaction and update product inventory.
Input Params   : 
                 pStoreID INT      - Store identifier
                 pCustomerID INT   - Customer identifier
                 pCashierID INT    - Staff/cashier identifier
                 pProductID INT    - Product identifier
                 pQuantity INT     - Quantity sold
Output         : Inserts a record into the Transaction table and updates Inventory.
===========================================================
*/
CREATE PROCEDURE p_createOrders(
    IN p_storeID INT(11),
    IN p_supplierID INT(11),
    IN p_productID INT(11),
    IN p_staffID INT(11),
    IN p_quantity INT(11),
    IN p_price DECIMAL(10, 2)
) 
BEGIN
    DECLARE p_orderNumber BIGINT;
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    BEGIN
        ROLLBACK;
    END;
    START TRANSACTION;
    -- Insert into Orders table
    INSERT INTO Orders (StoreID, SupplierID, ProductID, StaffID, Date, Quantity, BuyPrice)
    VALUES (p_storeID, p_supplierID, p_productID, p_staffID, NOW(), p_quantity, p_price);
    SET p_orderNumber = LAST_INSERT_ID();
    -- Check if Inventory exists and update or insert
    IF EXISTS (SELECT 1 FROM Inventory WHERE StoreID = p_storeID AND ProductID = p_productID) THEN
        UPDATE Inventory SET Quantity = Quantity + p_quantity WHERE StoreID = p_storeID AND ProductID = p_productID;
    ELSE
        INSERT INTO Inventory (StoreID, ProductID, Quantity, DiscountInfo, ValidFrom, ValidTo)
        VALUES (p_storeID, p_productID, p_quantity, 0, NULL, NULL);
    END IF;

    SELECT * FROM Orders WHERE OrderNumber = p_orderNumber;
    COMMIT;
END $$ 
```





#### Design Decision:

Our main system has a main class(`Main.java`)that facilitates switching between the menu options and separate classes for each related group of operations.This includes classes like `ClubMemberProcedure.java` for member-related operations, `createMerchInfo.java` for merchandise operations, `createStore.java` for store operations, and `createSupplier.java` for supplier operations. This was done to break the application into more manageable and maintainable pieces of code.

The `Main.java` class also contains the database connection parameters and a `getConnection()` method that other classes can use to access the database. This centralizes the database connection management, making it easier to maintain and update connection details.

Each operation class follows a similar pattern:

1. It collects user input through console prompts
2. Constructs an SQL statement with the appropriate parameters
3. Executes the statement against the database
4. Processes the results and provides feedback to the user

For data modification operations (like creating a new store or supplier), we use PreparedStatement with proper parameter binding for security, and `executeUpdate()` to perform the database changes. For queries (like getting club member information), we use `executeQuery()` to retrieve the data and display it to the user.The database design supports the retail business with tables for stores, merchandise, supplier and club members, with appropriate relationships between them. Each entity has dedicated management functions that allow users to perform all necessary retail operations from a single interface.



#### Functional Rules:

Part 1:

Software Engineer: Yung-Hsuan Teng (Prime), Xiaoqin Pi(Backup)

Database Designer/Administrator: Min-Ting Tu (Prime), Tzu-Tang Liu(Backup)

Application Programmer: Yung-Hsuan Teng (Prime), Xiaoqin Pi(Backup)

Test Plan Engineer: Tzu-Tang Liu(Prime), Min-Ting Tu(Backup)

Part 2:

Software Engineer: Yung-Hsuan Teng (Prime), Xiaoqin Pi(Backup)

Database Designer/Administrator: Min-Ting Tu (Prime), Tzu-Tang Liu(Backup)

Application Programmer: Yung-Hsuan Teng (Prime), Xiaoqin Pi(Backup)

Test Plan Engineer: Tzu-Tang Liu(Prime), Min-Ting Tu(Backup)

Part 3:

Software Engineer: Yung-Hsuan Teng (Prime), Xiaoqin Pi(Backup)

Database Designer/Administrator: Min-Ting Tu (Prime), Tzu-Tang Liu(Backup)

Application Programmer: Yung-Hsuan Teng (Prime), Xiaoqin Pi(Backup)

Test Plan Engineer: Tzu-Tang Liu(Prime), Min-Ting Tu(Backup)

