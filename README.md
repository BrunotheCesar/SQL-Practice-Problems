# SQL-Practice-Problems
[SQL Practice Problems](https://www.amazon.com/SQL-Practice-Problems-learn-doing/dp/1520807635) by Sylvia Moestl Vasilik

With SQL Practice Problems, you can get that level of experience by solving sets of targeted problems. These aren't just problems designed as syntax examples. These are the most common problems you encounter when you deal with data

## Introductory

**1.                  Which shippers do we have?**

    SELECT * FROM shippers   


**2.                 Certain fields from Categories**

    SELECT categoryname, description FROM categories  


**3.                 Sales Representatives**

    SELECT firstname, lastname, hiredate FROM employees
    WHERE Title = 'Sales Representative'
  
  
**4.                 Sales Representatives in the United States**

	SELECT firstname, lastname, hiredate FROM employees
	WHERE Title = 'Sales Representative' AND Country = 'USA'
  
**5.                 Orders placed by specific EmployeeID**

    SELECT * FROM orders
    WHERE EmployeeID = '5'


**6.                 Suppliers and ContactTitles**

    SELECT supplierID, contactname, contacttitle FROM suppliers
    WHERE contacttitle <> 'Marketing Manager'


**7.                 Products with “queso” in ProductName**

    SELECT productID, productname FROM products
	  WHERE productname LIKE '%queso%'
  
  
**8.                 Orders shipping to France or Belgium**

    SELECT orderID, customerID, shipcountry FROM orders
    WHERE shipcountry = 'France' OR shipcountry = 'Belgium'
    
    
**9.                 Orders shipping to any country in Latin America**

    SELECT orderID, customerID, shipcountry FROM orders
    WHERE shipcountry IN ('Brazil', 'Mexico', 'Argentina', 'Venezuela')
    
**10.             Employees, in order of age** 

    SELECT firstname, lastname, title, birthdate FROM employees
    ORDER BY birthdate
**11.              Showing only the Date with a DateTime field**

    SELECT firstname, lastname, title, CAST(birthdate AS DATE) FROM employees
    ORDER BY birthdate
**12.             Employees full name**

    SELECT firstname, lastname, firstname + ' ' + lastname AS fullname
    FROM employees
**13.             OrderDetails amount per line item**

    SELECT orderID, productID, unitprice, quantity, unitprice * quantity AS totalprice
    FROM orderdetails
    ORDER BY orderID, productid
**14.             How many customers?**

    SELECT COUNT(customerID) as TotalCustomers FROM customers
    
**15.             When was the first order?**

    SELECT MIN(orderDate) as FirstOrder FROM orders
    
**16.             Countries where there are customers**

    SELECT DISTINCT country FROM customers
**17.             Contact titles for customers**

    SELECT contacttitle, COUNT(contacttitle) AS Total FROM customers
    GROUP BY contacttitle
    ORDER BY total DESC
**18.             Products with associated supplier names**

    SELECT productID, productName, companyname FROM products
    INNER JOIN suppliers ON products.supplierID = suppliers.supplierID
    ORDER BY productID
**19.             Orders and the Shipper that was used**

    SELECT OrderID, CAST(OrderDate AS DATE) AS OrderDate, CompanyName FROM orders
    INNER JOIN shippers ON orders.shipvia = shippers.shipperID
    WHERE OrderId < 10300
    ORDER BY OrderID

## Intermediate

**20.            Categories, and the total products in each category**

    SELECT CategoryName, COUNT(*) AS TotalProducts FROM products
    INNER JOIN categories ON categories.CategoryID = products.categoryID
    GROUP BY CategoryName
    ORDER BY TotalProducts DESC
    
**21.             Total customers per country/city**

    SELECT City, Country, COUNT(*) AS total FROM customers
    GROUP BY country, city
    ORDER BY total DESC
**22.            Products that need reordering**

    SELECT ProductID, Productname, UnitsInStock, ReorderLevel
    FROM products
    WHERE UnitsInStock < ReorderLevel
    ORDER BY productid
**23.            Products that need reordering, continued**

    SELECT ProductID, Productname, UnitsInStock, UnitsOnOrder, ReorderLevel, Discontinued
    FROM products
    WHERE UnitsInStock + UnitsOnOrder <= ReorderLevel AND Discontinued = 0
    ORDER BY productid
    
**24.           Customer list by region**

    SELECT CustomerID, CompanyName, Region FROM customers
    ORDER BY (CASE
    WHEN region IS NULL THEN 1
    ELSE 0 
    END),
    region, customerID
**25.            High freight charges**

    SELECT TOP 3 ShipCountry, AVG(Freight) AS AverageFreight FROM orders
    GROUP BY ShipCountry
    ORDER BY AverageFreight DESC
**26.            High freight charges - 2015**

    SELECT TOP 3 ShipCountry, AVG(Freight) AS AverageFreight FROM orders
    WHERE Orderdate >= '20150101' AND Orderdate < '20160101'
    GROUP BY shipcountry
    ORDER BY AverageFreight DESC
**27.            High freight charges with between**

    SELECT TOP 3 ShipCountry, AVG(Freight) AS AverageFreight FROM orders
    WHERE Orderdate >= '20150101' AND Orderdate < '20160101'
    GROUP BY shipcountry
    ORDER BY AverageFreight DESC
**28.           High freight charges - last year**

    SELECT TOP 3 ShipCountry, AVG(Freight) AS AverageFreight FROM Orders
    WHERE OrderDate >= DATEADD(year, -1, (SELECT MAX(OrderDate) FROM orders))
    GROUP BY shipcountry
    ORDER BY AverageFreight DESC
**29.            Inventory list**

    SELECT orders.EmployeeId, employees.LastName, orders.OrderID, products.ProductName, orderdetails.Quantity
    FROM orders
    INNER JOIN Employees ON orders.employeeID = employees.employeeID
    INNER JOIN Orderdetails ON Orders.orderID = Orderdetails.orderID
    INNER JOIN products ON products.productID = orderdetails.productID
    ORDER BY orderID, products.ProductID
**30.            Customers with no orders**

    SELECT customers.CustomerID, Orders.CustomerID FROM customers
    LEFT JOIN Orders ON orders.customerID = customers.customerID
    WHERE Orders.CustomerID IS NULL
**31.             Customers with no orders for EmployeeID 4**

    SELECT customers.CustomerID, Orders.CustomerID FROM customers
    LEFT JOIN Orders ON orders.customerID = customers.customerID AND orders.employeeID = '4'
    WHERE Orders.CustomerID IS NULL
    
## Advanced

**32.            High-value customers**

    SELECT C.CustomerID, C.CompanyName, O.OrderID, SUM(OD.quantity * OD.unitprice) AS TotalOrderAmount
    FROM Customers AS C
    INNER JOIN Orders AS O ON O.CustomerID = C.CustomerID
    INNER JOIN orderdetails AS OD ON OD.orderid = O.orderID
    WHERE YEAR(O.OrderDate) = 2016
    GROUP BY C.CustomerID, C.CompanyName, O.OrderID
    HAVING SUM(OD.quantity * OD.unitprice) > '10000'
    ORDER BY TotalOrderAmount DESC
**33.            High-value customers - total orders**

    SELECT C.CustomerID, C.CompanyName, SUM(OD.quantity * OD.unitprice) AS TotalOrderAmount
    FROM Customers AS C
    INNER JOIN Orders AS O ON O.CustomerID = C.CustomerID
    INNER JOIN orderdetails AS OD ON OD.orderid = O.orderID
    WHERE YEAR(O.OrderDate) = 2016
    GROUP BY C.CustomerID, C.CompanyName
    HAVING SUM(OD.quantity * OD.unitprice) >= '15000'
    ORDER BY TotalOrderAmount DESC
**34.           High-value customers - with discount**

    SELECT C.CustomerID, C.CompanyName, SUM(OD.quantity * OD.unitprice) AS TotalsWithoutDiscount,
    SUM(OD.quantity * OD.unitprice * (1 - Discount)) AS TotalsWithDiscount
    FROM Customers AS C
    JOIN Orders AS O ON O.CustomerID = C.CustomerID
    JOIN orderdetails AS OD ON O.orderID = OD.orderid 
    WHERE OrderDate >= '20160101' AND OrderDate < '20170101'
    GROUP BY C.CustomerID, C.CompanyName
    HAVING SUM(OD.quantity * OD.unitprice * (1 - Discount)) > 10000
    ORDER BY TotalsWithDiscount DESC
