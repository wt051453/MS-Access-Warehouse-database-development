## Warehouse-database-development

### Narrative Description and Informational Needs
We are a small warehouse that provides receiving and temporary storage for the company’s internal customers, different operational units. We have been using spreadsheets and shipment receipts to keep track of customers, materials received and turned over. We would like to replace this manual method of tracking with a database.<br/>
Customers make orders in the company’s supply system. When orders arrived, the warehouse personnel would complete the orders in the same supply system with receipt date, and turn them over to the customers upon notifying. We need to keep track of the packages received with receipt date, quantity received and received by. While most packages are standard supplies, there are times that we would receive HAZMAT and high priority items which need to be stored separately with separate logs. <br/>
We also need to keep track of our Employees including their home address, contact information and rate of pay. We need to keep track of each Customer’s contact information. <br/>

### Initial List of Entities


![image](https://user-images.githubusercontent.com/27581761/115129932-a5d60e80-9fb8-11eb-8710-be62c775becd.png)

 
### ER Diagram
 
![image](https://user-images.githubusercontent.com/27581761/115129987-580dd600-9fb9-11eb-9c14-3cc61ca9bb36.png)
   
 
 

### Relationship Sentences
 
1)   One customer may place one or more orders
2)   One order must be placed by one and only one customer
3)   One order may contain one or more order items
4)   One order item must be assigned to one and only one order
5)   One order item must be included in one and only one material
6)   One material may contain one or more order items
7)   One order item must be handled by one and only one employee
8)   One employee may handle one or more order items
 
Conversion to Relational Model
 
1.	Customer (CustomerID (Key), FirstName, LastName, Address, City, State, ZipCode, PhoneNumber, Email)
2.	Orders (OrderID (Key), OrderBy, OrderDateTime, ShippingDetail, DeliveryDate, CustomerID (FK))
3.	Materials (MaterialID (key), Description, WeightInLB, SizeInFT, Hazmat)
4.	Employee (EmployeeID (key), FirstName, LastName, Address, City, State, ZipCode, PhoneNumber, Email, Certification)
5.	OrderItems (DateReceived, ReceivedBy, HighPriority, TurnoverDate, TurnedoverTo, OrderId(FK)(Key), EmployeeID(FK)(Key), MaterialsID(FK)(Key))
 
 
Normalization
 
Customer (CustomerID (Key), FirstName, LastName, Address, City, State, ZipCode, PhoneNumber, Email)
Key: CustomerID
FD1: CustomerID -> FirstName, LastName, Address, City, State, ZipCode, PhoneNumber, Email
FD2: Zipcode -> City, State
1NF because it meets the 6 criteria
2NF because there is no partial-key dependencies
3NF Not in 3NF due to transitive dependency: CustomerID -> Zipcode ;  Zipcode -> City, State. Solution is to split up relation resulting in a new relation:  ZipCodes(Zipcode(key), City, State). For the purposes of this project we elect to de-normalize ZipCodes to simplify the implementation.
 
 
Orders (OrderID (Key), OrderBy, OrderDateTime, ShippingDetail, DeliveryDate, CustomerID (FK))
Key: OrderID
FD1: OrderID -> OrderBy, OrderDateTime, ShippingDetail, DeliveryDate, CustomerID
1NF because it meets the 6 criteria
2NF because there is no partial-key dependencies
3NF because there is no transitive dependencies
 
Materials (MaterialID (key), Description, WeightInLB, SizeInFT, Hazmat)
Key: MaterialsID
FD1: MaterialsID -> Description, WeightInLB, SizeInFT, Hazmat
1NF because it meets the 6 criteria
2NF because there is no partial-key dependencies
3NF because there is no transitive dependencies
 
Employee (EmployeeID (key), FirstName, LastName, Address, City, State, ZipCode, PhoneNumber, Email, Certification)
Key: EmployeeID
FD1: EmployeeID -> FirstName, LastName, Address, City, State, ZipCode, PhoneNumber, Email, Certification
FD2: ZipCode-> City, State
1NF because it meets the 6 criteria
2NF because there is no partial-key dependencies
3NF Not in 3NF due to transitive dependency: EmployeeID -> Zipcode ;  Zipcode -> City, State. Solution is to split up relation resulting in a new relation:  ZipCodes(Zipcode(key), City, State). For the purposes of this project we elect to de-normalize ZipCodes to simplify the implementation.
 
OrderItems (DateReceived, ReceivedBy, HighPriority, TurnoverDate, TurnedoverTo, OrderID(FK)(Key), EmployeeID(FK)(Key), MaterialsID(FK)(Key))
Key: OrderID, EmployeeID, MaterialsID
FD1: OrderID, EmployeeID, MaterialsID  -> DateReceived, ReceivedBy, HighPriority, TurnoverDate, TurnedoverTo
1NF because it meets the 6 criteria
2NF because there is no partial-key dependencies
3NF because there is no transitive dependencies
 
Final set of relations: Customer, Orders, Materials, Employee, OrderItems
 
Creating and Altering Tables
 
CREATE TABLE Customer
(
	CustomerID   VARCHAR(10) NOT NULL,
	FirstName	VARCHAR(35),
	LastName 	VARCHAR(35),
	PhoneNumber  VARCHAR(15),
	Address  	VARCHAR(35),
	City         VARCHAR(36),
	State        VARCHAR(4),
	ZipCode  	VARCHAR(12),
	Email    	VARCHAR(50),
	CONSTRAINT pk_customer
      	PRIMARY KEY (CustomerID)
)
 
 
CREATE TABLE Orders
(
	OrderID   	 VARCHAR(10) NOT NULL,
	OrderBy  	  VARCHAR(70),
	OrderDateTime  DATE,
	ShippingDetail VARCHAR(70),
	DeliveryDate   DATE,
	CustomerID 	VARCHAR(10) NOT NULL,
	CONSTRAINT pk_orders
      	PRIMARY KEY (OrderID)
)
 
CREATE TABLE Materials
(
	MaterialID     	VARCHAR(10) NOT NULL,
    Description   	 VARCHAR(70),
	WeightInLB  	   NUMBER,
    SizeInFT  	     NUMBER,
    Hazmat     	    VARCHAR(4),
	CONSTRAINT pk_materials
    	PRIMARY KEY (MaterialID)
)
 
CREATE TABLE Employee
(
	EmployeeID 	VARCHAR(10) NOT NULL,
	FirstName  	VARCHAR(35),
    LastName   	VARCHAR(35),
    Address    	VARCHAR(45),
	City           VARCHAR(36),
	State          VARCHAR(4),
	ZipCode    	VARCHAR(12),
    PhoneNumber    VARCHAR(15),
	Email      	VARCHAR(50),
	Certification  VARCHAR(100),
 	CONSTRAINT pk_employee
   	PRIMARY KEY (EmployeeID)
)
 
 
CREATE TABLE OrderItems
(
    OrderID    	VARCHAR(10) NOT NULL,
	EmployeeID 	VARCHAR(10) NOT NULL,
	MaterialID 	VARCHAR(10) NOT NULL,
	DateReceived   DATE,
    ReceivedBy 	VARCHAR(70),
    HighPriority   VARCHAR(4),
    TurnoverDate   DATE,
	TurnedoverTo   VARCHAR(70),
	CONSTRAINT pk_orderitems
        PRIMARY KEY (OrderID, EmployeeID, MaterialID)
)
 
 
ALTER TABLE Orders
   ADD CONSTRAINT fk_orders_customer
 	FOREIGN KEY (CustomerID)
        REFERENCES Customer (CustomerID)
 
ALTER TABLE OrderItems
   ADD CONSTRAINT fk_orderitems_orders
 	FOREIGN KEY (OrderID)
        REFERENCES Orders (OrderID)
 
ALTER TABLE OrderItems
   ADD CONSTRAINT fk_orderitems_employee
 	FOREIGN KEY (EmployeeID)
        REFERENCES Employee (EmployeeID)
 
ALTER TABLE OrderItems
   ADD CONSTRAINT fk_orderitems_materials
 	FOREIGN KEY (MaterialID)
        REFERENCES Materials (MaterialID)
    
Relationships Map
 
 





Example printouts 

























































































































































●	CustomerDataEntry
○	This is a data entry form for creating new customers
○	The customerID field is formatted so that the 1st items is always a letter and the next 3 items will always be numbers (by changing input mark to L000)
○	 The first and last name field ensure that the first letter of the name will always be capitalized (please refer to VBA code image)
○	The state filed features a combo box, but we are not limited to the 3 choices shown  
●	EmployeeDataEntry
○	This form features the same traits as the CustomerDataEntry form 
●	MaterialsDataEntry
○	The materialID field is formatted so that the 1st items is always a letter and the next 3 items will always be numbers (by changing input mark to L000)
○	The hazmat field features a y/n selection combo box
●	OrdersDataEntry
○	The orderID field is formatted so that the 1st items is always a letter and the next 3 items will always be numbers (by changing input mark to L000)
○	The delivery date is always equal to or after the order date time
○	We have the ability to look up customer information by selecting the down arrow in customer ID
●	OrderItemsDataEntry
○	This form features the similar traits to the OrdersDataEntry form
○	The receive by field will always default to a hazmat certified employee if the incoming item has the hazmat property (code shown below)
Private Sub ReceivedBy_AfterUpdate()
If [MaterialID] = "M301" Then
[ReceivedBy] = "Levi Floyd"
End If
End Sub
●	OrdersMasterForm
○	This is the orders/detail form with orders as the master and order items as detail. This enables one to see multiple forms simultaneously 
○	The customerID field features a dropdown that enables you to see customer first and last names 
Queries/Reporting
HazmatQualifiedEmployee query/report
This is the query to lookup the employees who are Hazmat qualified to handle Hazmat orders. The SQL is the following:
SELECT Employee.EmployeeID, Employee.FirstName, Employee.LastName, Employee.Certification
FROM Employee
WHERE (((Employee.[Certification]) Like '*Hazmat*'));
 
TotalOrdersByCustomer query/report
This is the query to lookup the total orders by each customer. The SQL is the following:
SELECT o1.OrderID, o1.EmployeeID, o1.MaterialID, o2.OrderDateTime
FROM OrderItems AS o1 INNER JOIN Orders AS o2 ON o1.OrderID = o2.OrderID
WHERE o1.DateReceived IS NULL;
 
OutstandingOrders query/report
This is the query to lookup the outstanding orders that have not been received by the warehouse for tracking purpose. The SQL is the following:
SELECT OrderID, EmployeeID, MaterialID, DateReceived, ReceivedBy, HighPriority
FROM OrderItems
WHERE HighPriority = 'Yes' AND TurnoverDate IS NULL;


OutstandingTurover query/report
This is the query to lookup the orders that have been received by the warehouse but have not turned over to the customers. The SQL is the following:
SELECT COUNT(c.CustomerID) AS Total_Orders, c.FirstName, c.LastName, c.PhoneNumber, c.Address, c.City, c.State, c.Email
FROM Customer AS c INNER JOIN Orders AS o ON o.CustomerID = c.CustomerID
GROUP BY c.FirstName, c.LastName, c.PhoneNumber, c.Address, c.City, c.State, c.Email;

Conclusion
	This project was an eye-opening experience for all of us, it brought us from start to finish in the creation of a relation database. There were many challenges along the way but overall, it was a great learning experience. We thought that the creation of the conceptual model of the database was the most challenging aspect of the project, as at the time we did not grasp the concepts as fully as we do now. We feel that one of the most important skills that we learned was how to work with access, it’s a skill that we will bring with us into whatever field we will/are working in. 
Overall, we found that the natural progression of the project was very rewarding, completing one step at a time as we learned how to perform the necessary tasks through the lectures and tutorials provided. 
![image](https://user-images.githubusercontent.com/27581761/115129941-bc7c6580-9fb8-11eb-98e4-c81bcd4db1d1.png)

