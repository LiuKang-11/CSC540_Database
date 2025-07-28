















<div style='font-family: Arial, sans-serif;
        text-align: center;
        padding: 50px;'>
  <h1>Project 2</h1>
	<p><strong>WolfWR</strong></p>
  <p>
    Group M
  </p>
  <p> Min-Ting Tu(mtu), Yung-Hsuan Teng(yteng2), Tzu-Tang Liu(tliu33), Xiaoqin Pi(xpi)
</p>
  <p>March 5th, 2025</p>
</div>



<div style="page-break-after: always; break-after: page;"></div>



[toc]



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

#### 1. Global Relational Database Schema

* **Store(StoreID, ManagerID, StoreAddress, PhoneNumber)**

**StoreID → StoreID,ManagerID, StoreAddress, PhoneNumber** holds because each StoreID is unique, and identifies a single store with a particular manager, address, and phone number. If we tried to use any combination of the other attributes to identify a store, we could encounter duplicates (multiple stores might share an address/phone structure in certain scenarios, or addresses might be reused).Because the left-hand side StoreID is a superkey, this FD is in BCNF (and thereby 3NF)

* **Staff(StaffID, StoreID, JobTitle, Name, Age, PhoneNumber, Email, HomeAddress,** 

**TimeOfEmployment)**

**Staff → StaffID, StoreID, JobTitle, Name, Age, PhoneNumber, Email, HomeAddress, TimeOfEmployment** holds because each StaffID uniquely identifies a single staff member with specific personal details(name, age, phoneNumber, etc.) and a store assignment. That staff member has a StoreID (the store where they work), a JobTitle ( manager, assistant manager, cashier, warehouse checker, billing staff, etc.), plus personal information (Name, Age, PhoneNumber, Email, HomeAddress, TimeOfEmployment).If we tried to use a non‐key attribute like JobTitle on the left (“JobTitle → StaffID”), it would fail because many different staff members could share the same job title (e.g., multiple cashiers). And also If we tried to use any combination of the non-key attributes(like name +PhoneNumber) as an identifier, it would fail in cases where multiple employees might share the name or phone number. Because StaffID is a superkey, the relation is in BCNF (and thus in 3NF)

* **AdminStaff(StaffID)**

**StaffID → StaffID** has only one attribute” StaffID” ,which is itself the primary key. A single‐attribute table where that attribute is the PK implies the dependency is StaffID → StaffID (trivial). Because the left‐hand side is a superkey, the relation is in BCNF (and therefore 3NF).

* **RegistrationStaff(StaffID)**

**StaffID → StaffID** has only one attribute” StaffID” ,which is itself the primary key. A single‐attribute relation with that attribute as the key is in BCNF (and therefore 3NF).

* **WarehouseStaff(StaffID)**

**StaffID → StaffID** has only one attribute” StaffID” ,which is itself the primary key. A single‐attribute relation with that attribute as the key is in BCNF (and therefore 3NF).

* **BillingStaff(StaffID)**

**StaffID → StaffID** has only one attribute” StaffID” ,which is itself the primary key. No other attributes exist, so the FD is trivially in BCNF (and therefore 3NF).

* **Cashier(StaffID)**

**StaffID → StaffID** has only one attribute” StaffID” ,which is itself the primary key. A single‐attribute relation with that attribute as the key is in BCNF (and therefore 3NF).

* **ClubMember(CustomerID, Email, PhoneNumber, FirstName, LastName, HomeAddress, MembershipLevel, ActiveStatus)**

**CustomerID → CustomerID, Email, PhoneNumber, FirstName, LastName, HomeAddress, MembershipLevel, ActiveStatus** the CustomerID is the unique identifier for a club member, determining all other member attributes.For example: could fail if two family members share a phone or email. Because CustomerID is a superkey, this FD is in BCNF and hence 3NF.

* **Merchandise(ProductID, ProductName, Price, SupplierID, ProductionDate, ExpirationDate)**

**ProductID → ProductID, ProductName, Price, SupplierID, ProductionDate, ExpirationDate** Each ProductID uniquely identifies the merchandise item. If we took any subset of attributes (such as ProductName + ProductionDate) as a key, there could be repeats (multiple items with the same name or production date). Because the left‐hand side is a superkey (ProductID), this dependency is in BCNF/3NF.

* **Inventory(StoreID, ProductID, Quantity, DiscountInfo, ValidDiscountDate)**

**(StoreID, ProductID) → StoreID, ProductID, Quantity, DiscountInfo, ValidDiscountDate** is a composite key that identifies a unique row in Inventory (the quantity and discount for that specific product in that specific store). No subset of these attributes alone can uniquely identify the row because you need both a store and a product. Hence, the left‐hand side is a superkey, making this in BCNF (and 3NF).

* **Supplier(SupplierID, Name, Location, PhoneNumber, Email)**

**SupplierID → SupplierID, Name, Location, PhoneNumber, Email :** Each SupplierID uniquely identifies one supplier, including all their details (name, location, contact info). If we tried to use just (Name + Location) or other attributes, there could be clashes (two suppliers with the same name in the same large city). Because SupplierID is a superkey, this FD is in BCNF/3NF.

* **SignUp(StaffID, CustomerID, SignUpNumber, Date, StoreID)**

**SignUpNumber → SignUpNumber, StaffID, CustomerID, Date, StoreID** Each signup record is uniquely identified by SignUpNumber, which determines the staff member, the customer, date, and store involved in that signup. If we tried to rely on other attributes (like StaffID + CustomerID), multiple signups could happen for the same staff‐customer pair on different dates or stores. Because SignUpNumber is a superkey, it is in BCNF/3NF.

* **Cancel(StaffID, CustomerID, CancelNumber, Date)**

**CancelNumber → CancelNumber, StaffID, CustomerID, Date** Each cancellation is uniquely identified by CancelNumber. If we tried to use just StaffID + Date, for instance, the same staff member could cancel multiple members on the same date. Because the left side is a superkey, this FD is in BCNF/3NF.

* **Transfer(store1ID, store2ID, product1ID, product2ID, Date, StaffID, Quantity, TransferNumber)**

**TransferNumber → TransferNumber, store1ID, store2ID, product1ID, product2ID, Date, StaffID, Quantity** A TransferNumber uniquely identifies one transfer event, which might involve products moving from one store to another. Using any other combination of attributes ( store1ID + store2ID + Date) could lead to duplicates if multiple transfers happen the same day. Since TransferNumber is a superkey, it is in BCNF/3NF.

* **Orders(StoreID,SupplierID, ProductID, StaffID, Date, Quantity, Price, OrderNumber)**

**OrderNumber → OrderNumber, StoreID, SupplierID, ProductID, StaffID, Date, Quantity, Price** Each OrderNumber is unique for a particular order. If we tried to use (StoreID + SupplierID + Date) as a key, it might fail if the store places multiple orders to the same supplier on the same date. Because OrderNumber is a superkey, this relation is in BCNF/3NF.

* **Returns( StoreID, ProductID, StaffID, CustomerID, Date, Quantity, ReturnNumber)**

**ReturnNumber → ReturnNumber, StoreID, ProductID, StaffID, CustomerID, Date, Quantity** Each return is identified by ReturnNumber. Relying on (StaffID + Date) would not be unique if one staff member processes multiple returns in the same day. Since ReturnNumber is a superkey, it satisfies BCNF/3NF.

* **Transaction( StoreID, CustomerID, CashierID, ProductID, Quantity, TotalPrice, PurchaseDate, TransactionNumber)**

**TransactionNumber → TransactionNumber, StoreID, CustomerID, CashierID, ProductID, Quantity, TotalPrice, PurchaseDate** Each TransactionNumber uniquely identifies a purchase transaction. If we tried using (CustomerID + PurchaseDate) or other combos, different transactions could share those attributes. Because TransactionNumber is a superkey, this FD is in BCNF (and therefore 3NF).

#### 2. Design decisions for the global schema

**Entity Sets to Relations:**

- The entity sets in our diagram were made into relations, with the attributes the same for Staff, Store, Club Member, Supplier, Merchandise, Inventory, and Transaction.
- The entity sets that are subsets of Staff (e.g., Cashier) were made into relations based on the E/R diagram to avoid redundancy and save table space.
- Inventory was modeled as a weak entity set, dependent on both Stores and Merchandise, with a composite key of StoreID and ProductID to ensure unique identification and accurate stock tracking.

**Combining Many-One Relationships:**

- Inventory was made into a relation with all its attributes plus the StoreID and ProductID attributes, which are foreign keys from the Store and Merchandise entity sets. Including these attributes represents the “of” relationships in the diagram between Inventory and both Store and Merchandise.
- Merchandise was made into a relation using all its attributes plus the SupplierID attribute from combining its many-one relationship to Supplier. Thus, we do not have a separate relationship for the “SuppliedBy” relationship in the schema.
- Transaction was made into a relation using all its attributes plus the Billing StaffID/Cashier ID attribute combining its many-one relationship to Staff.
- Orders was made into a relation using its attributes and the SupplierID and StoreID attributes from its many-one relationships with Supplier and Store.
- Returns was made into a relation using all its attributes plus the CustomerID from its many-one relationship to ClubMember and StoreID and ProductID from its many-one relationships to Store and Merchandise.

**Relationships to Relations:**

- SignUp: Turned into a relation with attributes StaffID (foreign key from Staff), SignUpNumber, CustomerID (foreign key from ClubMember), Date, and ActiveStatus.
- Cancel: Turned into a relation with attributes CancelNumber, CustomerID (foreign key from ClubMember), Date, and StaffID (foreign key from Staff).
- Transfer: Turned into a relation with attributes TransferNumber, SourceStoreID and DestinationStoreID (foreign keys from Store), SourceProductID and DestinationProductID(foreign key from Merchandise), Date, and Quantity.
- Returns: Turned into a relation with attributes ReturnNumber, CustomerID (foreign key from ClubMember), StoreID (foreign key from Store), ProductID (foreign key from Merchandise), StaffID (foreign key from Staff), Date, and Quantity.
- Orders: Turned into a relation with attributes OrderNumber, StoreID (foreign key from Store), SupplierID (foreign key from Supplier), ProductID (foreign key from Merchandise), StaffID (foreign key from Staff), Date, Quantity, and Price.

**Store(StoreID, ManagerID, StoreAddress, PhoneNumber)**

- StoreID is the primary key.
- ManagerID can be NULL in case that the store temporarily doesn't have a manager.
- PhoneNumber can be NULL in case that the store doesn't have a phone yet.
- StoreAddress can’t be NULL.

**Staff(StaffID, StoreID, JobTitle, Name, Age, PhoneNumber, Email, HomeAddress,TimeOfEmployment)**

- StaffID is the primary key.
- StoreID, JobTitle, Name, Age, PhoneNumber, Email, HomeAddress are not allowed to be NULL.
- TimeOfEmployment is not allowed to be NULL and it will have a default value 0 for it which means the time of this employment is starting from 0 years.

**AdminStaff(StaffID)**

- StaffID is the primary key.

**Registration staff(StaffID)**

- StaffID is the primary key.

**WarehouseStaff(StaffID)**

- StaffID is the primary key.

**BillingStaff(StaffID)**

- StaffID is the primary key.

**Cashier(StaffID)**

- StaffID is the primary key.

**ClubMember(CustomerID, Email, PhoneNumber, FirstName, LastName, HomeAddress, MembershipLevel, ActiveStatus)**

- CustomerID is the primary key.
- Email, PhoneNumber, FirstName, LastName, HomeAddress, MembershipLevel are not allowed to be NULL.
- ActiveStatus is not allowed to be NULL. it will automatically set to true which means the customer account is activated.

**Merchandise(ProductID, ProductName, Price, SupplierID, ProductionDate, ExpirationDate)**

- ProductID is the primary key.
- ProductName, Price, SupplierID, ProductionDate, and ExpirationDate are not allowed to be NULL.

**Inventory(StoreID, ProductID, Quantity, DiscountInfo, ValidDiscountDate)**

- StoreID, ProductID are composed of primary keys.
- Quantity is not allowed to be NULL.
- DiscountInfo and ValidDiscountDate are allowed to be NULL because some products may not have discount information so they will also not have a valid discount date.

**Supplier(SupplierID, Name, Location, PhoneNumber, Email)**

- SupplierID is the primary key.
- Name, Location, PhoneNumber, and Email are not allowed to be NULL.

**Sign up(StaffID, CustomerID, SignUpNumber, Date, StoreID)**

- StaffID, CustomerID,SignUpNumber are composed of primary keys.
- SignUpNumber, Date, StoreID are not allowed to be NULL.
- SignUpNumber is a serial number generated by DBMS automatically.
- Date will be set as the current time.

**Cancel(StaffID, CustomerID, CancelNumber, Date)**

- StaffID, CustomerIDare,CancelNumber composed of primary keys.
- CancelNumber, and Date are not allowed to be NULL.
- CancelNumber is a serial number generated by DBMS automatically.

- Date will be set as the current time.

**Transfer(Store1ID, Store2ID, Product1ID, Product2ID, Date, Quantity, TransferNumber)**

- Store1ID, Store2ID, Product1ID, and Product2ID, TransferNumber are composed of primary keys.
- Date, Quantity, and TransferNumber are not allowed to be NULL.
- TransferNumber is a serial number generated by DBMS automatically.
- Date will be set as the current time.

**Orders(StoreID, SupplierID, ProductID, StaffID, Date, Quantity, Price, OrderNumber)**

- StoreID, SupplierID, and ProductID, OrderNumber are composed of primary keys.
- StaffID, Date, Quantity, Price, and OrderNumber are not allowed to be NULL.
- OrderNumber is a serial number generated by DBMS automatically.
- Date will be set as the current time.

**Returns(StoreID, ProductID, StaffID, Date, Quantity, ReturnNumber)**

- StoreID, and ProductID, ReturnNumber are composed of primary keys.
- StaffID, Date, Quantity, and ReturnNumber are not allowed to be NULL.
- ReturnNumber is a serial number generated by DBMS automatically.
- Date will be set as the current time.

**Transaction(StoreID, CustomerID, StaffID, ProductID, Quantity, TotalPrice, PurchaseDate, TransactionNumber)**

- StoreID, CustomerID, StaffID, and ProductID, TransactionNumber are composed of primary keys.
- Quantity, TotalPrice, PurchaseDate, and TransactionNumber are not allowed to be NULL.
- TransactionNumber is a serial number generated by DBMS automatically.
- PurchaseDate will be set as current time.



#### 3. Base Relations

```sql
-- Store Table
CREATE TABLE Store (
    StoreID INT AUTO_INCREMENT PRIMARY KEY,
    ManagerID INT,
    StoreAddress VARCHAR(255) NOT NULL,
    PhoneNumber VARCHAR(15),
    FOREIGN KEY (ManagerID) REFERENCES Staff(StaffID) ON DELETE SET NULL
);

-- Staff Table
CREATE TABLE Staff (
    StaffID INT AUTO_INCREMENT PRIMARY KEY,
    StoreID INT NOT NULL,
    JobTitle VARCHAR(50) NOT NULL,
    Name VARCHAR(100) NOT NULL,
    Age INT NOT NULL,
    PhoneNumber VARCHAR(15) NOT NULL,
    Email VARCHAR(100) NOT NULL,
    HomeAddress VARCHAR(255) NOT NULL,
    TimeOfEmployment INT NOT NULL DEFAULT 0,
    FOREIGN KEY (StoreID) REFERENCES Store(StoreID)
);

-- AdminStaff Table
CREATE TABLE AdminStaff (
    StaffID INT PRIMARY KEY,
    FOREIGN KEY (StaffID) REFERENCES Staff(StaffID)
);

-- RegistrationStaff Table
CREATE TABLE RegistrationStaff (
    StaffID INT PRIMARY KEY,
    FOREIGN KEY (StaffID) REFERENCES Staff(StaffID)
);

-- WarehouseStaff Table
CREATE TABLE WarehouseStaff (
    StaffID INT PRIMARY KEY,
    FOREIGN KEY (StaffID) REFERENCES Staff(StaffID)
);

-- BillingStaff Table
CREATE TABLE BillingStaff (
    StaffID INT PRIMARY KEY,
    FOREIGN KEY (StaffID) REFERENCES Staff(StaffID)
);

-- Cashier Table
CREATE TABLE Cashier (
    StaffID INT PRIMARY KEY,
    FOREIGN KEY (StaffID) REFERENCES Staff(StaffID)
);

-- ClubMember Table
CREATE TABLE ClubMember (
    CustomerID INT AUTO_INCREMENT PRIMARY KEY,
    Email VARCHAR(100) NOT NULL,
    PhoneNumber VARCHAR(15) NOT NULL,
    FirstName VARCHAR(50) NOT NULL,
    LastName VARCHAR(50) NOT NULL,
    HomeAddress VARCHAR(255) NOT NULL,
    MembershipLevel VARCHAR(50) NOT NULL,
    ActiveStatus BOOLEAN NOT NULL DEFAULT TRUE
);

-- Merchandise Table
CREATE TABLE Merchandise (
    ProductID INT AUTO_INCREMENT PRIMARY KEY,
    ProductName VARCHAR(100) NOT NULL,
    Price DECIMAL(10, 2) NOT NULL,
    SupplierID INT,
    ProductionDate DATE NOT NULL,
    ExpirationDate DATE NOT NULL,
    FOREIGN KEY (SupplierID) REFERENCES Supplier(SupplierID) ON DELETE SET NULL
);

-- Inventory Table
CREATE TABLE Inventory (
    StoreID INT,
    ProductID INT,
    Quantity INT NOT NULL,
    DiscountInfo DECIMAL(2, 0),
    ValidDiscountDate DATE,
    PRIMARY KEY (StoreID, ProductID),
    FOREIGN KEY (StoreID) REFERENCES Store(StoreID),
    FOREIGN KEY (ProductID) REFERENCES Merchandise(ProductID)
);

-- Supplier Table
CREATE TABLE Supplier (
    SupplierID INT AUTO_INCREMENT PRIMARY KEY,
    Name VARCHAR(100) NOT NULL,
    Location VARCHAR(255) NOT NULL,
    PhoneNumber VARCHAR(15) NOT NULL,
    Email VARCHAR(100) NOT NULL
);

-- SignUp Table
CREATE TABLE SignUp (
    StaffID INT,
    CustomerID INT,
    SignUpNumber SERIAL, 
    Date Date, 
    StoreID INT NOT NULL,
    PRIMARY KEY (StaffID, CustomerID,SignUpNumber),
    FOREIGN KEY (StaffID) REFERENCES RegistrationStaff(StaffID),
    FOREIGN KEY (CustomerID) REFERENCES ClubMember(CustomerID),
    FOREIGN KEY (StoreID) REFERENCES Store(StoreID)
);

-- Cancel Table
CREATE TABLE Cancel (
    StaffID INT,
    CustomerID INT,
    CancelNumber SERIAL,
    Date Date,
    PRIMARY KEY (StaffID, CustomerID, CancelNumber),
    FOREIGN KEY (StaffID) REFERENCES RegistrationStaff(StaffID),
    FOREIGN KEY (CustomerID) REFERENCES ClubMember(CustomerID)
);

-- Transfer Table
CREATE TABLE Transfer (
    Store1ID INT,
    Store2ID INT,
    Product1ID INT,
    Product2ID INT,
    Date Date,
    StaffID INT NOT NULL,
    Quantity INT NOT NULL,
    TransferNumber SERIAL,
    PRIMARY KEY (Store1ID, Store2ID, Product1ID, Product2ID, TransferNumber),
    FOREIGN KEY (Store1ID) REFERENCES Store(StoreID),
    FOREIGN KEY (StaffID) REFERENCES WarehouseStaff(StaffID),
    FOREIGN KEY (Store2ID) REFERENCES Store(StoreID),
    FOREIGN KEY (Product1ID) REFERENCES Merchandise(ProductID),
    FOREIGN KEY (Product2ID) REFERENCES Merchandise(ProductID)
);

-- Order Table
CREATE TABLE Orders (
    StoreID INT,
    SupplierID INT,
    ProductID INT,
    StaffID INT NOT NULL,
    Date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    Quantity INT NOT NULL,
    Price DECIMAL(10, 2) NOT NULL,
    OrderNumber SERIAL,
    PRIMARY KEY (StoreID, SupplierID, ProductID, OrderNumber),
    FOREIGN KEY (StoreID) REFERENCES Store(StoreID),
    FOREIGN KEY (SupplierID) REFERENCES Supplier(SupplierID),
    FOREIGN KEY (ProductID) REFERENCES Merchandise(ProductID),
    FOREIGN KEY (StaffID) REFERENCES WarehouseStaff(StaffID)
);

-- Return Table
CREATE TABLE Returns (
    StoreID INT,
    ProductID INT,
    StaffID INT NOT NULL,
    CustomerID INT NOT NULL,
    Date Date,
    Quantity INT NOT NULL,
    ReturnNumber SERIAL,
    PRIMARY KEY (StoreID, ProductID,ReturnNumber),
    FOREIGN KEY (StoreID) REFERENCES Store(StoreID),
    FOREIGN KEY (ProductID) REFERENCES Merchandise(ProductID),
    FOREIGN KEY (StaffID) REFERENCES WarehouseStaff(StaffID),
    FOREIGN KEY (CustomerID) REFERENCES ClubMember(CustomerID)
);

-- Transaction Table
CREATE TABLE Transaction (
    StoreID INT,
    CustomerID INT,
    StaffID INT,
    ProductID INT,
    Quantity INT NOT NULL,
    TotalPrice DECIMAL(10, 2) NOT NULL,
    PurchaseDate TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    TransactionNumber SERIAL,
    PRIMARY KEY (StoreID, CustomerID, StaffID, ProductID, TransactionNumber),
    FOREIGN KEY (StoreID) REFERENCES Store(StoreID),
    FOREIGN KEY (CustomerID) REFERENCES ClubMember(CustomerID),
    FOREIGN KEY (StaffID) REFERENCES Cashier(StaffID),
    FOREIGN KEY (ProductID) REFERENCES Merchandise(ProductID)
);
```

```sql
select * from AdminStaff;
```

<img src="/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/1.png" alt="1" style="zoom:50%;" />

```sql
select * from BillingStaff;
```

<img src="/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/2.png" alt="2" style="zoom:50%;" />

```sql
select * from Cancel;
```

<img src="/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/3.png" alt="3" style="zoom:50%;" />

```sql
select * from Cashier;
```

<img src="/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/4.png" alt="4" style="zoom:50%;" />

```sql
select * from ClubMember;
```

![5](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/5.png)

```sql
select * from Inventory;
```

<img src="/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/6.png" alt="6" style="zoom:50%;" />

```sql
select * from Merchandise;
```

<img src="/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/7.png" alt="7" style="zoom:50%;" />

```sql
select * from Orders;
```

![8](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/8.png)

```sql
select * from RegistrationStaff;
```

<img src="/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/9.png" alt="9" style="zoom:50%;" />

```sql
select * from Returns;
```

![10](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/10.png)

```sql
select * from SignUp;
```

<img src="/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/11.png" alt="11" style="zoom:50%;" />

```sql
select * from Staff;
```

![12](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/12.png)

```sql
select * from Store;
```

<img src="/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/13.png" alt="13" style="zoom:50%;" />

```sql
select * from Supplier;
```

<img src="/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/14.png" alt="14" style="zoom:50%;" />

```sql
select * from Transaction;
```

![15](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/15.png)

```sql
select * from Transfer;
```

![16](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/16.png)

```sql
select * from WarehouseStaff;
```

<img src="/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/17.png" alt="17" style="zoom:50%;" />

#### 4. Tasks and Operations- Realistic Situations

##### 4.1

##### INFORMATION PROCESSING

```sql
-- getClubMemberInfo
SELECT * FROM ClubMember WHERE CustomerID = 2;
```

![24](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/24.png)

```sql
-- createClubMemberInfo
CALL p_createClubMemberInfo(firstName, lastName, membershipLevel, email, phoneNumber, homeAddress, registrationStaffID, storeID);
Query OK, 2 rows affected (0.0077 sec)
-- updateClubMemberInfo
UPDATE ClubMember SET Email = 'johnjohndoe@gmail.com', PhoneNumber = '919-485-4708' WHERE CustomerID = 2;
Query OK, 1 row affected (0.0043 sec)
-- cancelClubMemberInfo
CALL p_createCancelInfo(registrationStaffID, customerID);
Query OK, 2 rows affected (0.0037 sec)
```

```sql
-- getSignUpInfo
SELECT * FROM SignUp WHERE CustomerID = 1;
```

<img src="/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/25.png" alt="25" style="zoom:50%;" />

```sql
-- getMemberCancelInfo
SELECT * FROM Cancel WHERE CustomerID = 4;
```

<img src="/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/26.png" alt="26" style="zoom:50%;" />

```sql
-- getStoreInfo
SELECT * FROM Store WHERE StoreID = 2;
```

<img src="/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/27.png" alt="27" style="zoom:50%;" />

```sql
-- updateStoreInfo
UPDATE Store SET PhoneNumber = '919-331-6048' WHERE StoreID = 1;
Query OK, 1 row affected (0.0040 sec)
```

```sql
-- deleteStoreInfo
DELETE FROM Store WHERE StoreID = 1;
Query OK, 1 row affected (0.0120 sec)
```

```sql
-- getStaffInfo
SELECT * FROM Staff WHERE StaffID = 2;
```

![28](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/28.png)

```sql
-- updateStaffInfo
UPDATE Staff SET PhoneNumber = '715-427-3511' WHERE StaffID = 5;
Query OK, 1 row affected (0.0041 sec)
-- deleteStaffInfo
DELETE FROM Staff WHERE StaffID = 7
Query OK, 1 row affected (0.0360 sec)
```

```sql
-- getMechInfo
SELECT * FROM Merchandise WHERE ProductID = 1;
```

![29](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/29.png)

```sql
-- createMerchInfo
INSERT INTO Merchandise (ProductName, Price, SupplierID, ProductionDate, ExpirationDate)
VALUES('Black Tea', 6.99, 1, '2025-07-01', '2026-02-01');
Query OK, 1 row affected (0.0047 sec)
```

```sql
-- updateMerchInfo
UPDATE Merchandise SET Price = '2.50' WHERE ProductID = 1;
Query OK, 1 row affected (0.0045 sec)
-- deleteMerchInfo
UPDATE Merchandise SET SupplierID = NULL WHERE ProductID = 1;
Query OK, 1 row affected (0.0043 sec)
```

```sql
-- getSupplierInfo
SELECT * FROM Supplier WHERE SupplierID = 2;
```

![30](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/30.png)

```sql
-- updateSupplierInfo
UPDATE Supplier SET PhoneNumber = '919-347-4771' WHERE SupplierID = 10;
Query OK, 1 row affected (0.0021 sec)
-- deleteSupplierInfo
DELETE FROM Supplier WHERE SupplierID = 10;
Query OK, 1 row affected (0.0023 sec)
```

##### MAINTAINING INVENTORY RECORDS

```sql
-- getInventoryInfo
SELECT * FROM Inventory WHERE StoreID = 1 AND ProductID = 2;
```

![31](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/31.png)

```sql
-- updateInventoryInfo
UPDATE Inventory SET Quantity = 5000 WHERE StoreID = 1 AND ProductID = 1;
Query OK, 1 row affected (0.0051 sec)
-- deleteInventoryInfo
DELETE FROM Inventory WHERE StoreID = 1 AND ProductID = 1;
Query OK, 1 row affected (0.0043 sec)
-- update inventory discount info
CALL p_updateInventoryDiscount(1, 1, 10, '2025-12-31');
Query OK, 1 rows affected (0.0033 sec)
```

```sql
-- getOrderInfo
SELECT * FROM Orders WHERE StoreID = 1 AND SupplierID = 1 AND ProductID = 1 AND StaffID = 4;
```

![32](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/32.png)

```sql
-- createOrderInfo
CALL p_createOrders(storeID, supplierID, productID, warehouseStaffID, quantity, buyPrice);
Query OK, 1 rows affected (0.0047 sec)
-- transferProduct
CALL p_createTransfer(store1ID, store2ID, product1ID, product2ID, staffID, quantity);
Query OK, 1 rows affected (0.0052 sec)
```

```sql
-- getTransferInfo
SELECT * FROM Transfer WHERE Store1ID = 1 AND Store2ID = 2 AND Product1ID = 1 AND Product2ID = 1;
```

![33](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/33.png)

```sql
-- returnProduct
CALL p_returnProduct(storeID, productID, warehouseStaffID, quantity);
Query OK, 1 rows affected (0.0023 sec)
```

```sql
-- getReturnInfo
SELECT * FROM Returns WHERE StoreID = 2 AND ProductID = 1 AND StaffID = 4;
```

![34](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/34.png)

##### MAINTAINING BILLING AND TRANSACTION RECORDS

```sql
-- getTransactionInfo
SELECT * FROM Transaction WHERE StoreID = 2 AND ProductID = 3 AND StaffID = 10 AND CustomerID = 2;
```

![35](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/35.png)

```sql
-- createTransactionInfo
CALL p_createTransaction(storeID, customerID, cashierID, productID, quantity);
Query OK, 1 rows affected (0.0083 sec)
```

```sql
-- generateRewardCheck
CALL p_createRewardCheck(2025);
```

![36](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/36.png)

```sql
-- generateSupplierBill
CALL p_createSupplierBill(2025, 3);
```

![37](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/37.png)

##### Reports

```sql
-- generateSalesReport
CALL p_generateSalesReport(1, 2025, 0, 0);
```

![38](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/38.png)

```sql
-- generateSalesGrowthReport
 CALL p_generateSalesGrowthReport(1, '2025-02', '2025-03');
```

![39](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/39.png)

```sql
-- generateMerchandiseStockReport
select * from Inventory where storeID = 1;
select * from Inventory where productID = 2;
```

![40](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/40.png)

![41](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/41.png)

```sql
-- createCustomerGrowthReportByMonth
CALL p_createCustomerGrowthReportByMonth();
```

![44](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/44.png)

```sql
-- createCustomerGrowthReportByYear
CALL p_createCustomerGrowthReportByYear();
```

![45](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/45.png)

```sql
-- createCustomerActivityReport
CALL p_createCustomerActivityReport('2022-02-07', '2025-02-04');
```

![46](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/46.png)

##### Procedure

Because some of the operations we used procedure to do it, so the following are all procedure source code we created in the system.

```sql 
-- create Club Member Info and SignUp
DELIMITER $$ 
CREATE PROCEDURE p_createClubMemberInfo(
    IN p_firstName VARCHAR(50),
    IN p_lastName VARCHAR(50),
    IN p_membershipLevel VARCHAR(50),
    IN p_email VARCHAR(100),
    IN p_phoneNumber VARCHAR(15),
    IN p_homeAddress VARCHAR(255),
    IN p_staffID INT,
    IN p_storeID INT
)
BEGIN
    INSERT INTO ClubMember (FirstName, LastName, MembershipLevel, Email, PhoneNumber, HomeAddress)
    VALUES (p_firstName, p_lastName, p_membershipLevel, p_email, p_phoneNumber, p_homeAddress);

    INSERT INTO SignUp (StaffID, CustomerID, Date, StoreID)
    VALUES (p_staffID, LAST_INSERT_ID(), NOW(), p_storeID);
END $$ DELIMITER ;

-- create Cancel Info
DELIMITER $$
CREATE PROCEDURE p_createCancelInfo(
    IN p_staffID INT,
    IN p_customerID INT
)
MODIFIES SQL DATA
BEGIN
    UPDATE ClubMember SET ActiveStatus = 0 WHERE CustomerID = p_customerID;

    INSERT INTO Cancel (StaffID, CustomerID, Date)
    VALUES (p_staffID, p_customerID, NOW());
END $$ DELIMITER ;
-- create Orders
DELIMITER $$ 
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
    -- Insert into Orders table (uncomment and adjust as needed)
    INSERT INTO Orders (StoreID, SupplierID, ProductID, StaffID, Date, Quantity, Price)
    VALUES (p_storeID, p_supplierID, p_productID, p_staffID, NOW(), p_quantity, p_price);
    SET p_orderNumber = LAST_INSERT_ID();
    -- Check if Inventory exists and update or insert
    IF EXISTS (SELECT 1 FROM Inventory WHERE StoreID = p_storeID AND ProductID = p_productID) THEN
        UPDATE Inventory SET Quantity = Quantity + p_quantity WHERE StoreID = p_storeID AND ProductID = p_productID;
    ELSE
        INSERT INTO Inventory (StoreID, ProductID, Quantity, DiscountInfo, ValidDiscountDate)
        VALUES (p_storeID, p_productID, p_quantity, 0, NULL);
    END IF;

    SELECT * FROM Orders WHERE OrderNumber = p_orderNumber;
END $$ DELIMITER ;
-- create Product Info
DELIMITER $$
CREATE PROCEDURE p_createTransaction(
    IN pStoreID INT,
    IN pCustomerID INT,
    IN pCashierID INT,
    IN pProductID INT,
    IN pQuantity INT
    -- You could add more parameters if needed, e.g. pPurchaseDate, pTransactionNumber, etc.
)
BEGIN
    -- Local variables
    DECLARE vPrice DECIMAL(10,2);
    DECLARE vDiscount DECIMAL(10,2);
    DECLARE vTotalPrice DECIMAL(10,2);
    -- 1) Get Price from Merchandise
    SELECT Price INTO vPrice FROM Merchandise WHERE ProductID = pProductID;
    -- 2) Get Discount from Inventory
    --    (Assuming DiscountInfo is stored as a numeric value, e.g. 0.9 for 10% off,
    --     or 1.0 if no discount. If it's a string like '10%', you'd need to parse it.)
    SELECT DiscountInfo INTO vDiscount FROM Inventory WHERE StoreID = pStoreID AND ProductID = pProductID;
    -- 3) Calculate totalPrice
    --    For example: totalPrice = price * quantity * discount
    SET vTotalPrice = vPrice * pQuantity * (100 - vDiscount) / 100;
    -- 4) Insert into Transaction
    --    If TransactionNumber is auto-generated, omit it. Otherwise pass it as a parameter.
    INSERT INTO `Transaction` (StoreID, CustomerID, StaffID, ProductID, Quantity, TotalPrice, PurchaseDate
    )
    VALUES (pStoreID, pCustomerID, pCashierID, pProductID, pQuantity, vTotalPrice, NOW());
    -- 5) Update Inventory
    UPDATE Inventory SET Quantity = Quantity - pQuantity WHERE StoreID = pStoreID AND ProductID = pProductID;
END $$ DELIMITER ;

-- create Transfer
DELIMITER $$
CREATE PROCEDURE p_createTransfer (
    IN p_Store1ID INT,
    IN p_Store2ID INT,
    IN p_Product1ID INT,
    IN p_Product2ID INT,
    IN p_StaffID INT
    IN p_Quantity INT,
)
BEGIN
    DECLARE v_TransferNumber BIGINT;
    -- Insert a new transfer record
    INSERT INTO Transfer (Store1ID, Store2ID, Product1ID, Product2ID, Date, StaffID, Quantity)
    VALUES (p_Store1ID, p_Store2ID, p_Product1ID, p_Product2ID, CURRENT_TIMESTAMP, p_staffID, p_Quantity);
    -- Retrieve the newly created TransferNumber (since it's AUTO_INCREMENT)
    SET v_TransferNumber = LAST_INSERT_ID();
    -- Update inventory: Deduct quantity from source store
    UPDATE Inventory SET Quantity = Quantity - p_Quantity WHERE StoreID = p_Store1ID AND ProductID = p_Product1ID;
    -- Update inventory: Add quantity to destination store
    UPDATE Inventory  SET Quantity = Quantity + p_Quantity WHERE StoreID = p_Store2ID AND ProductID = p_Product2ID;
    -- Return the inserted transfer record
    SELECT * FROM Transfer WHERE TransferNumber = v_TransferNumber;
END $$ DELIMITER ;

-- create Return
DELIMITER $$
CREATE PROCEDURE p_returnProduct  (
    IN p_StoreID INT,
    IN p_ProductID INT,
    IN p_StaffID INT,
    IN p_Quantity INT,
    IN p_customerID INT
)
BEGIN
    DECLARE v_ReturnNumber BIGINT;

    INSERT INTO Returns (StoreID, ProductID, StaffID, Date, Quantity, CustomerID)
    VALUES (p_StoreID, p_ProductID, p_StaffID, CURRENT_TIMESTAMP, p_Quantity, p_customerID);
    
    SET v_ReturnNumber = LAST_INSERT_ID();
    
    UPDATE Inventory SET Quantity = Quantity + p_Quantity WHERE StoreID = p_StoreID AND ProductID = p_ProductID;
    
    SELECT * FROM Returns WHERE ReturnNumber = v_ReturnNumber;
END $$ DELIMITER ;

-- create Reward Check
DELIMITER $$ 
CREATE PROCEDURE p_createRewardCheck(
    IN p_year YEAR
) 
BEGIN
    SELECT c.customerID, ROUND((SUM(t.totalPrice) * 0.02), 2) AS TotalReward
    FROM Transaction AS t
    JOIN ClubMember AS c ON t.CustomerID = c.CustomerID
    WHERE c.membershipLevel = 'platinum' AND YEAR(t.PurchaseDate) = p_year
    GROUP BY c.CustomerID;
END $$ DELIMITER ;

-- create Supplier Bill
DELIMITER $$ 
CREATE PROCEDURE p_createSupplierBill(
    IN p_year YEAR, 
    IN p_month INT
) 
BEGIN
    SELECT s.SupplierID, SUM(o.Price * o.Quantity) AS TotalBill
    FROM Supplier AS s
    JOIN Orders AS o ON s.SupplierID = o.SupplierID
    WHERE YEAR(o.Date) = p_year AND MONTH(o.Date) = p_month
    GROUP BY s.SupplierID;
END $$ DELIMITER ;

-- create Sales Report
DELIMITER $$
CREATE PROCEDURE p_generateSalesReport(
    IN pStoreID INT,
    IN pYear INT,   -- 0 means "no specific year"
    IN pMonth INT,  -- 0 means "no specific month"
    IN pDay INT     -- 0 means "no specific day"
)
BEGIN
    /*
      This procedure returns total sales (SUM(TotalPrice)) from the `Transaction` table,
      filtered by StoreID and optionally by Year, Month, Day, depending on the parameter values:
      
      1) pYear = 0  => Summarize across ALL years (group by year).
      2) pYear != 0 AND pMonth = 0 => Summarize for that year, grouped by month.
      3) pYear != 0 AND pMonth != 0 AND pDay = 0 => Summarize for that (year, month), grouped by day.
      4) pYear != 0 AND pMonth != 0 AND pDay != 0 => Summarize that single day (one row).
    */
    IF pYear = 0 THEN
        /* Case 1: pYear=0 => Summarize across ALL years (group by year) */
        SELECT YEAR(PurchaseDate) AS SalesYear, SUM(TotalPrice) AS TotalSales 
        FROM Transaction
        WHERE StoreID = pStoreID
        GROUP BY YEAR(PurchaseDate)
        ORDER BY SalesYear;
    ELSEIF pYear != 0 AND pMonth = 0 THEN
        /* Case 2: Summarize that year by month */
        SELECT MONTH(PurchaseDate) AS SalesMonth, SUM(TotalPrice) AS TotalSales 
        FROM Transaction
        WHERE StoreID = pStoreID AND YEAR(PurchaseDate) = pYear
        GROUP BY MONTH(PurchaseDate)
        ORDER BY SalesMonth;
    ELSEIF pYear != 0 AND pMonth != 0 AND pDay = 0 THEN
        /* Case 3: Summarize that (year, month) by day */
        SELECT DAY(PurchaseDate) AS SalesDay, SUM(TotalPrice) AS TotalSales
        FROM Transaction
        WHERE StoreID = pStoreID AND YEAR(PurchaseDate) = pYear AND MONTH(PurchaseDate) = pMonth
        GROUP BY DAY(PurchaseDate)
        ORDER BY SalesDay;
    ELSE
        /* Case 4: pYear != 0, pMonth != 0, pDay != 0 => Single day */
        SELECT pYear AS SalesYear, pMonth AS SalesMonth, pDay AS SalesDay, SUM(TotalPrice) AS TotalSales
        FROM Transaction
        WHERE StoreID = pStoreID AND YEAR(PurchaseDate) = pYear AND MONTH(PurchaseDate) = pMonth AND DAY(PurchaseDate) = pDay;
    END IF;
END $$ DELIMITER ;

-- create Sales Growth Report
DELIMITER $$ 
CREATE PROCEDURE p_generateSalesGrowthReport(
    IN p_StoreID INT,
    IN p_Month1 VARCHAR(7),
    IN p_Month2 VARCHAR(7)
)
BEGIN
    WITH MonthlySales AS (
        SELECT DATE_FORMAT(PurchaseDate, '%Y-%m') AS SalesMonth, SUM(TotalPrice) AS Total_Sales
        FROM Transaction
        WHERE StoreID = p_StoreID
        AND (DATE_FORMAT(PurchaseDate, '%Y-%m') = p_Month1 OR DATE_FORMAT(PurchaseDate, '%Y-%m') = p_Month2)
        GROUP BY SalesMonth
    )
    SELECT 
        MAX(CASE WHEN SalesMonth = p_Month1 THEN Total_Sales END) AS Sales_Month1,
        MAX(CASE WHEN SalesMonth = p_Month2 THEN Total_Sales END) AS Sales_Month2,
        COALESCE(
            (MAX(CASE WHEN SalesMonth = p_Month2 THEN Total_Sales END) - 
             MAX(CASE WHEN SalesMonth = p_Month1 THEN Total_Sales END)) 
            / NULLIF(MAX(CASE WHEN SalesMonth = p_Month1 THEN Total_Sales END), 0) * 100, 0
        ) AS Growth_Rate
    FROM MonthlySales;
END $$ DELIMITER ;

-- create Customer Growth Report by Month
DELIMITER $$
CREATE PROCEDURE p_createCustomerGrowthReportByMonth()
BEGIN
   CREATE TEMPORARY TABLE TempGrowthReport (
       Year INT,
       Month INT,
       NewCustomerCount INT,
       PreviousMonthCount INT DEFAULT 0,
       Growth INT DEFAULT 0,
       GrowthRate DECIMAL(10, 2) DEFAULT NULL
   );

   INSERT INTO TempGrowthReport (Year, Month, NewCustomerCount)
   SELECT
       YEAR(SignUp.Date) AS Year,
       MONTH(SignUp.Date) AS Month,
       COUNT(SignUp.CustomerID) AS NewCustomerCount
   FROM SignUp
   GROUP BY Year, Month
   ORDER BY Year, Month;

   UPDATE TempGrowthReport t1
   JOIN TempGrowthReport t2
   ON (t1.Year = t2.Year AND t1.Month = t2.Month + 1)
      OR (t1.Month = 1 AND t2.Month = 12 AND t1.Year = t2.Year + 1)
   SET t1.PreviousMonthCount = t2.NewCustomerCount,
       t1.Growth = t1.NewCustomerCount - t2.NewCustomerCount;

   UPDATE TempGrowthReport
   SET GrowthRate = CASE
       WHEN PreviousMonthCount = 0 THEN NULL
       ELSE ROUND((NewCustomerCount - PreviousMonthCount) / PreviousMonthCount * 100, 2)
   END;
   SELECT * FROM TempGrowthReport;

   DROP TEMPORARY TABLE TempGrowthReport;
END $$ DELIMITER ;

-- create Customer Growth Report by Year
DELIMITER $$
CREATE PROCEDURE p_createCustomerGrowthReportByYear()
BEGIN
   CREATE TEMPORARY TABLE TempGrowthReport (
       Year INT,
       NewCustomerCount INT,
       PreviousYearCount INT DEFAULT 0,
       Growth INT DEFAULT 0,
       GrowthRate DECIMAL(10, 2) DEFAULT NULL
   );

   INSERT INTO TempGrowthReport (Year, NewCustomerCount)
   SELECT
       YEAR(SignUp.Date) AS Year,
       COUNT(SignUp.CustomerID) AS NewCustomerCount
   FROM SignUp
   GROUP BY Year
   ORDER BY Year;

   UPDATE TempGrowthReport t1
   JOIN TempGrowthReport t2
   ON t1.Year = t2.Year + 1
   SET t1.PreviousYearCount = t2.NewCustomerCount,
       t1.Growth = t1.NewCustomerCount - t2.NewCustomerCount;

   UPDATE TempGrowthReport
   SET GrowthRate = CASE
       WHEN PreviousYearCount = 0 THEN NULL
       ELSE ROUND(((NewCustomerCount - PreviousYearCount) / PreviousYearCount) * 100, 2)
   END;

   SELECT * FROM TempGrowthReport;
   DROP TEMPORARY TABLE TempGrowthReport;
END $$ DELIMITER ;

-- create Customer Activity Report
DELIMITER $$
CREATE PROCEDURE p_createCustomerActivityReport(
   IN p_startDate DATE,
   IN p_endDate DATE
)                                
BEGIN   
    SELECT c.CustomerID, c.FirstName, c.LastName, SUM(t.TotalPrice) AS TotalPurchaseAmount
    FROM Transaction AS t 
    JOIN ClubMember AS c ON t.CustomerID = c.CustomerID
    WHERE t.PurchaseDate BETWEEN p_startDate AND p_endDate
    GROUP BY c.CustomerID;
END $$ DELIMITER ;

-- update inventory discount info
DELIMITER $$

CREATE PROCEDURE p_updateInventoryDiscount(
    IN pStoreID INT,
    IN pProductID INT,
    IN pDiscount DECIMAL(10, 2),     -- integer between 0 and 100
    IN pValidDate DATE    -- date for discount validity
)
BEGIN
    -- Optional: Validate that discount is between 0 and 100
    IF pDiscount < 0 OR pDiscount > 100 THEN
        SIGNAL SQLSTATE '45000'
            SET MESSAGE_TEXT = 'Discount must be between 0 and 100.';
    END IF;

    -- Update the Inventory row
    UPDATE Inventory
    SET DiscountInfo = pDiscount,
        ValidDiscountDate = pValidDate
    WHERE StoreID = pStoreID
      AND ProductID = pProductID;

    -- Return the updated row
    SELECT *
    FROM Inventory
    WHERE StoreID = pStoreID
      AND ProductID = pProductID;
END$$

DELIMITER ;
```



##### 4.2

```sql
-- 1. Return SignUp information in certain period
EXPLAIN SELECT *
FROM SignUp
WHERE Date BETWEEN '2023-10-01 10:00:00' AND LAST_DAY('2023-10-02 10:30:00');
```

![18](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/18.png)

```sql
-- 2. Create index “idx-signip_date” on SignUp(Date)

CREATE INDEX idx_signup_date
ON SignUp (Date);
```

![19](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/19.png)

```sql
-- 3. Return all Clubmember with firstname “John”

EXPLAIN SELECT *
FROM ClubMember
WHERE FirstName = 'John';
```

![20](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/20.png)

```sql
--4. Create index “idx_firstname” on ClubMember(FirstName)

CREATE INDEX idx_firstname
ON ClubMember (FirstName);
```

![21](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/21.png)

##### 4.3

Query 1: Calculating Rewards for Platinum Members

```sql
SELECT c.customerID, ROUND((SUM(t.totalPrice) * 0.02), 2) AS TotalReward 
FROM Transaction AS t 
JOIN ClubMember AS c ON t.CustomerID = c.CustomerID
WHERE c.membershipLevel = 'platinum' AND YEAR(t.PurchaseDate) = 2025
GROUP BY c.CustomerID;
```

- Relational algebra

![22](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/22.png)

- Correctness Proof:
  - The query joins the ClubMember table and the Transaction table using CustomerID, ensuring that only transactions linked to actual customers are considered.
  - The WHERE condition ensures that only Platinum members and transactions from 2025 are included.
  - The GROUP BY c.CustomerID ensures that each customer’s transactions are aggregated separately.
  - The reward calculation is based on SUM(t.totalPrice) * 0.02, which correctly computes 2% of the total transaction amount per customer.
  - The ROUND(..., 2) ensures monetary precision, which is necessary for financial calculations.

Query 2: Calculating Total Bill for Suppliers in March 2025

```sql
SELECT s.SupplierID, SUM(o.Price) AS TotalBill
FROM Supplier AS s 
JOIN Orders AS o ON s.SupplierID = o.SupplierID 
WHERE YEAR(o.Date) = 2025 AND MONTH(o.Date) = 3
GROUP BY s.SupplierID;
```

- Relational algebra

![23](/Users/katydu/Documents/Obsidian Vault/CSC540 DB/project1/pic/23.png)

- Correctness Proof:
  - The query joins Supplier and Orders based on SupplierID, ensuring that every order is associated with a valid supplier.
  - The WHERE clause ensures that only orders placed in March 2025 are included.
  - The GROUP BY s.SupplierID ensures that the total bill is calculated per supplier.
  - The SUM(o.Price) correctly computes the total amount spent on each supplier’s orders.
