## Warehouse-database-development

### Narrative Description and Informational Needs
We are a small warehouse that provides receiving and temporary storage for the company’s internal customers, different operational units. We have been using spreadsheets and shipment receipts to keep track of customers, materials received and turned over. We would like to replace this manual method of tracking with a database.<br/>
Customers make orders in the company’s supply system. When orders arrived, the warehouse personnel would complete the orders in the same supply system with receipt date, and turn them over to the customers upon notifying. We need to keep track of the packages received with receipt date, quantity received and received by. While most packages are standard supplies, there are times that we would receive HAZMAT and high priority items which need to be stored separately with separate logs. <br/>
We also need to keep track of our Employees including their home address, contact information and rate of pay. We need to keep track of each Customer’s contact information. <br/>
 
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
 
### Conversion to Relational Model
 
1.	Customer (CustomerID (Key), FirstName, LastName, Address, City, State, ZipCode, PhoneNumber, Email)
2.	Orders (OrderID (Key), OrderBy, OrderDateTime, ShippingDetail, DeliveryDate, CustomerID (FK))
3.	Materials (MaterialID (key), Description, WeightInLB, SizeInFT, Hazmat)
4.	Employee (EmployeeID (key), FirstName, LastName, Address, City, State, ZipCode, PhoneNumber, Email, Certification)
5.	OrderItems (DateReceived, ReceivedBy, HighPriority, TurnoverDate, TurnedoverTo, OrderId(FK)(Key), EmployeeID(FK)(Key), MaterialsID(FK)(Key))
 
 
### Normalization
 
Customer (CustomerID (Key), FirstName, LastName, Address, City, State, ZipCode, PhoneNumber, Email) <br/>
Key: CustomerID <br/>
FD1: CustomerID -> FirstName, LastName, Address, City, State, ZipCode, PhoneNumber, Email <br/>
FD2: Zipcode -> City, State <br/>
1NF because it meets the 6 criteria <br/>
2NF because there is no partial-key dependencies <br/>
3NF Not in 3NF due to transitive dependency: CustomerID -> Zipcode ;  Zipcode -> City, State. Solution is to split up relation resulting in a new relation:  ZipCodes(Zipcode(key), City, State). For the purposes of this project we elect to de-normalize ZipCodes to simplify the implementation. <br/>
 
Orders (OrderID (Key), OrderBy, OrderDateTime, ShippingDetail, DeliveryDate, CustomerID (FK)) <br/>
Key: OrderID <br/>
FD1: OrderID -> OrderBy, OrderDateTime, ShippingDetail, DeliveryDate, CustomerID <br/>
1NF because it meets the 6 criteria <br/>
2NF because there is no partial-key dependencies <br/>
3NF because there is no transitive dependencies <br/>

Materials (MaterialID (key), Description, WeightInLB, SizeInFT, Hazmat) <br/>
Key: MaterialsID <br/>
FD1: MaterialsID -> Description, WeightInLB, SizeInFT, Hazmat <br/>
1NF because it meets the 6 criteria <br/>
2NF because there is no partial-key dependencies <br/>
3NF because there is no transitive dependencies <br/>

Employee (EmployeeID (key), FirstName, LastName, Address, City, State, ZipCode, PhoneNumber, Email, Certification) <br/>
Key: EmployeeID <br/>
FD1: EmployeeID -> FirstName, LastName, Address, City, State, ZipCode, PhoneNumber, Email, Certification <br/>
FD2: ZipCode-> City, State <br/>
1NF because it meets the 6 criteria <br/>
2NF because there is no partial-key dependencies <br/>
3NF Not in 3NF due to transitive dependency: EmployeeID -> Zipcode ;  Zipcode -> City, State. Solution is to split up relation resulting in a new relation:  ZipCodes(Zipcode(key), City, State). For the purposes of this project we elect to de-normalize ZipCodes to simplify the implementation. <br/>

OrderItems (DateReceived, ReceivedBy, HighPriority, TurnoverDate, TurnedoverTo, OrderID(FK)(Key), EmployeeID(FK)(Key), MaterialsID(FK)(Key)) <br/>
Key: OrderID, EmployeeID, MaterialsID <br/>
FD1: OrderID, EmployeeID, MaterialsID  -> DateReceived, ReceivedBy, HighPriority, TurnoverDate, TurnedoverTo <br/>
1NF because it meets the 6 criteria <br/>
2NF because there is no partial-key dependencies <br/>
3NF because there is no transitive dependencies <br/>

Final set of relations: Customer, Orders, Materials, Employee, OrderItems <br/>
 
### Creating and Altering Tables
 
CREATE TABLE Customer <br/>
( <br/>
	CustomerID   VARCHAR(10) NOT NULL, <br/>
	FirstName	VARCHAR(35), <br/>
	LastName 	VARCHAR(35), <br/>
	PhoneNumber  VARCHAR(15), <br/>
	Address  	VARCHAR(35), <br/>
	City         VARCHAR(36), <br/>
	State        VARCHAR(4), <br/>
	ZipCode  	VARCHAR(12), <br/>
	Email    	VARCHAR(50), <br/>
	CONSTRAINT pk_customer <br/>
      	PRIMARY KEY (CustomerID) <br/>
) <br/>
 
 
CREATE TABLE Orders <br/>
( <br/>
	OrderID   	 VARCHAR(10) NOT NULL, <br/>
	OrderBy  	  VARCHAR(70), <br/>
	OrderDateTime  DATE, <br/>
	ShippingDetail VARCHAR(70), <br/>
	DeliveryDate   DATE, <br/>
	CustomerID 	VARCHAR(10) NOT NULL, <br/>
	CONSTRAINT pk_orders <br/>
      	PRIMARY KEY (OrderID) <br/>
) <br/>
 
CREATE TABLE Materials <br/>
( <br/>
	MaterialID     	VARCHAR(10) NOT NULL, <br/>
    Description   	 VARCHAR(70), <br/>
	WeightInLB  	   NUMBER, <br/>
    SizeInFT  	     NUMBER, <br/>
    Hazmat     	    VARCHAR(4), <br/>
	CONSTRAINT pk_materials <br/>
    	PRIMARY KEY (MaterialID) <br/>
) <br/>
 
CREATE TABLE Employee <br/>
( <br/>
	EmployeeID 	VARCHAR(10) NOT NULL, <br/>
	FirstName  	VARCHAR(35), <br/>
    LastName   	VARCHAR(35), <br/>
    Address    	VARCHAR(45), <br/>
	City           VARCHAR(36), <br/>
	State          VARCHAR(4), <br/>
	ZipCode    	VARCHAR(12), <br/>
    PhoneNumber    VARCHAR(15), <br/>
	Email      	VARCHAR(50), <br/>
	Certification  VARCHAR(100), <br/>
 	CONSTRAINT pk_employee <br/>
   	PRIMARY KEY (EmployeeID) <br/>
) <br/>
 
 
CREATE TABLE OrderItems <br/>
( <br/>
    OrderID    	VARCHAR(10) NOT NULL, <br/>
	EmployeeID 	VARCHAR(10) NOT NULL, <br/>
	MaterialID 	VARCHAR(10) NOT NULL, <br/>
	DateReceived   DATE, <br/>
    ReceivedBy 	VARCHAR(70), <br/>
    HighPriority   VARCHAR(4), <br/>
    TurnoverDate   DATE, <br/>
	TurnedoverTo   VARCHAR(70), <br/>
	CONSTRAINT pk_orderitems <br/>
        PRIMARY KEY (OrderID, EmployeeID, MaterialID) <br/>
) <br/>
 
 
ALTER TABLE Orders <br/>
   ADD CONSTRAINT fk_orders_customer <br/>
 	FOREIGN KEY (CustomerID) <br/>
        REFERENCES Customer (CustomerID) <br/>
 
ALTER TABLE OrderItems <br/>
   ADD CONSTRAINT fk_orderitems_orders <br/>
 	FOREIGN KEY (OrderID) <br/>
        REFERENCES Orders (OrderID) <br/>
 
ALTER TABLE OrderItems <br/>
   ADD CONSTRAINT fk_orderitems_employee <br/>
 	FOREIGN KEY (EmployeeID) <br/>
        REFERENCES Employee (EmployeeID) <br/>
 
ALTER TABLE OrderItems <br/>
   ADD CONSTRAINT fk_orderitems_materials <br/>
 	FOREIGN KEY (MaterialID) <br/>
        REFERENCES Materials (MaterialID) <br/>
    
### Relationships Map
 
 
![image](https://user-images.githubusercontent.com/27581761/115130110-b9827480-9fba-11eb-8dea-6b1ced0d067c.png)





### Example printouts 

![image](https://user-images.githubusercontent.com/27581761/115130117-c30bdc80-9fba-11eb-9bcc-ec0e9194f69d.png)

### CustomerDataEntry
-	This is a data entry form for creating new customers
-	The customerID field is formatted so that the 1st items is always a letter and the next 3 items will always be numbers (by changing input mark to L000)
-	 The first and last name field ensure that the first letter of the name will always be capitalized (please refer to VBA code image)
-	The state filed features a combo box, but we are not limited to the 3 choices shown  


![image](https://user-images.githubusercontent.com/27581761/115130121-c69f6380-9fba-11eb-8869-f2fe1e1063a1.png)


![image](https://user-images.githubusercontent.com/27581761/115130122-cbfcae00-9fba-11eb-91d3-dd147ecce80b.png)

### EmployeeDataEntry
-	This form features the same traits as the CustomerDataEntry form 

![image](https://user-images.githubusercontent.com/27581761/115130124-d028cb80-9fba-11eb-8918-a290ad87b058.png)

### MaterialsDataEntry
-	The materialID field is formatted so that the 1st items is always a letter and the next 3 items will always be numbers (by changing input mark to L000)
-	The hazmat field features a y/n selection combo box

![image](https://user-images.githubusercontent.com/27581761/115130128-d4ed7f80-9fba-11eb-9764-ac7a5b8c0cd4.png)

### OrdersDataEntry
-	The orderID field is formatted so that the 1st items is always a letter and the next 3 items will always be numbers (by changing input mark to L000)
-	The delivery date is always equal to or after the order date time
-	We have the ability to look up customer information by selecting the down arrow in customer ID

![image](https://user-images.githubusercontent.com/27581761/115130129-d9199d00-9fba-11eb-81a5-197c2ff16539.png)

### OrderItemsDataEntry
-	This form features the similar traits to the OrdersDataEntry form
-	The receive by field will always default to a hazmat certified employee if the incoming item has the hazmat property (code shown below)

Private Sub ReceivedBy_AfterUpdate() <br/>
If [MaterialID] = "M301" Then <br/>
[ReceivedBy] = "Levi Floyd" <br/>
End If <br/>
End Sub <br/>

![image](https://user-images.githubusercontent.com/27581761/115130132-dd45ba80-9fba-11eb-97e1-08056e9c615b.png)

### OrdersMasterForm
-	This is the orders/detail form with orders as the master and order items as detail. This enables one to see multiple forms simultaneously 
-	The customerID field features a dropdown that enables you to see customer first and last names 


![image](https://user-images.githubusercontent.com/27581761/115130135-e20a6e80-9fba-11eb-953d-7965c0ef66ff.png)

![image](https://user-images.githubusercontent.com/27581761/115130138-e6368c00-9fba-11eb-9825-ab5a0f87e1dc.png)

![image](https://user-images.githubusercontent.com/27581761/115130140-eafb4000-9fba-11eb-9bb1-f7879382b8f2.png)

![image](https://user-images.githubusercontent.com/27581761/115130142-ef275d80-9fba-11eb-9eb4-95fcdabf1f50.png)



### Queries/Reporting

HazmatQualifiedEmployee query/report

This is the query to lookup the employees who are Hazmat qualified to handle Hazmat orders. The SQL is the following: <br/>
SELECT Employee.EmployeeID, Employee.FirstName, Employee.LastName, Employee.Certification <br/>
FROM Employee <br/>
WHERE (((Employee.[Certification]) Like '*Hazmat*')); <br/>
 
TotalOrdersByCustomer query/report

This is the query to lookup the total orders by each customer. The SQL is the following: <br/>
SELECT o1.OrderID, o1.EmployeeID, o1.MaterialID, o2.OrderDateTime <br/>
FROM OrderItems AS o1 INNER JOIN Orders AS o2 ON o1.OrderID = o2.OrderID <br/>
WHERE o1.DateReceived IS NULL; <br/>
 
OutstandingOrders query/report

This is the query to lookup the outstanding orders that have not been received by the warehouse for tracking purpose. The SQL is the following: <br/>
SELECT OrderID, EmployeeID, MaterialID, DateReceived, ReceivedBy, HighPriority <br/>
FROM OrderItems <br/>
WHERE HighPriority = 'Yes' AND TurnoverDate IS NULL; <br/>


OutstandingTurover query/report

This is the query to lookup the orders that have been received by the warehouse but have not turned over to the customers. The SQL is the following: <br/>
SELECT COUNT(c.CustomerID) AS Total_Orders, c.FirstName, c.LastName, c.PhoneNumber, c.Address, c.City, c.State, c.Email <br/>
FROM Customer AS c INNER JOIN Orders AS o ON o.CustomerID = c.CustomerID <br/>
GROUP BY c.FirstName, c.LastName, c.PhoneNumber, c.Address, c.City, c.State, c.Email; <br/>

### Conclusion

This project was an eye-opening experience for all of us, it brought us from start to finish in the creation of a relation database. There were many challenges along the way but overall, it was a great learning experience. We thought that the creation of the conceptual model of the database was the most challenging aspect of the project, as at the time we did not grasp the concepts as fully as we do now. We feel that one of the most important skills that we learned was how to work with access, it’s a skill that we will bring with us into whatever field we will/are working in. 
Overall, we found that the natural progression of the project was very rewarding, completing one step at a time as we learned how to perform the necessary tasks through the lectures and tutorials provided. 



