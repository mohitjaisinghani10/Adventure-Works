create database AdventureWorks;

use AdventureWorks;

UPDATE `adventureworks`.`factsales` 
SET `OrderDate` = STR_TO_DATE(`OrderDate`, '%d/%m/%Y');

UPDATE `adventureworks`.`factsales` 
SET `ShipDate` = STR_TO_DATE(`ShipDate`, '%d/%m/%Y');


-- 1. Calculate the Total Sales and Profit per year

SELECT 
    YEAR(orderdate) AS `Year`,
    CEILING(SUM(salesamount)) AS 'Total Sales',
    CEILING(SUM(salesamount - (totalproductcost + taxamt + freight))) AS `Profit`
FROM
    factsales
GROUP BY YEAR(orderDate);

![image](https://github.com/mohitjaisinghani10/Adventure-Works/assets/85800857/133f2ca2-fc48-48ac-bcdb-bb15b1b77ab9)

-- 2. Calculate total Sales and Profit  as per Product Category

SELECT 
    pc.EnglishProductCategoryName AS 'Product Category',
    CEILING(SUM(fs.salesamount)) AS 'Total Sales',
    CEILING(SUM(salesamount - (totalproductcost + taxamt + freight))) AS `Profit`
FROM
    factsales fs
        LEFT JOIN
    product p ON p.ProductKey = fs.ProductKey
        JOIN
    subcategory s ON s.ProductSubcategoryKey = p.ProductSubcategoryKey
        JOIN
    productcategory pc ON pc.ProductCategoryKey = s.ProductCategoryKey
GROUP BY pc.EnglishProductCategoryName;

![image](https://github.com/mohitjaisinghani10/Adventure-Works/assets/85800857/431ca7cc-737e-479a-a914-470e2727a8c2)

-- 3. Find the top 10 products which generate the highest revenue

SELECT 
    p.EnglishProductName AS 'Product Name',
    SUM(fs.salesamount) 'Total Sales'
FROM
    factsales fs
        JOIN
    product p ON p.ProductKey = fs.ProductKey
GROUP BY p.EnglishProductName
ORDER BY SUM(fs.salesamount) DESC
LIMIT 10;

![image](https://github.com/mohitjaisinghani10/Adventure-Works/assets/85800857/779f1da2-ec18-45ff-952b-56eb16d15136)


-- 4. Find the top 10 products which generate the highest profits

SELECT 
    p.EnglishProductName AS 'Product Name',
    CEILING(SUM(salesamount - (totalproductcost + taxamt + freight))) AS `Profit`
FROM
    factsales fs
        JOIN
    product p ON p.ProductKey = fs.ProductKey
GROUP BY p.EnglishProductName
ORDER BY `Profit` DESC
LIMIT 10;

![image](https://github.com/mohitjaisinghani10/Adventure-Works/assets/85800857/49230332-8aac-47fe-8cce-93a4762f2a92)

-- 5. Calculate the year-on-year growth percentage in terms of revenue 

Select Year(orderDate) as 'Year' , truncate(sum(salesamount),2) as 'Revenue' , 
concat(Ceiling((sum(salesamount) - lag(sum(salesamount)) over (order by Year(orderDate)))/(lag(sum(salesamount)) over (order by Year(orderDate))) * 100), ' %') as 'YOY Growth %'
from factsales
group by Year(orderDate);

![image](https://github.com/mohitjaisinghani10/Adventure-Works/assets/85800857/eb3e208d-d7c5-4b12-a22c-59358df7a06f)

-- 6. Find the total orders and revenue generated from each country

SELECT 
    s.SalesTerritoryCountry AS 'Country',
    SUM(fs.orderquantity) AS 'Total Orders',
    TRUNCATE(SUM(fs.salesamount), 2) AS 'Total Revenue'
FROM
    factsales fs
        RIGHT JOIN
    salesterritory s ON s.SalesTerritoryKey = fs.SalesTerritoryKey
GROUP BY s.SalesTerritoryCountry;

![image](https://github.com/mohitjaisinghani10/Adventure-Works/assets/85800857/ab87bee5-f230-4c19-a08a-10b7e125b208)

-- 7. Calculate the rolling average profits as per every Quarter 

select Year(orderdate) as 'Year' , Quarter(orderdate) as 'Quarter',
truncate(avg(SUM(salesamount) - sum(totalproductcost + taxamt + freight)) over (rows between 3 preceding and current row),2) AS 'Rolling Avg Profit'
from factsales
group by Year(orderdate) , Quarter(orderdate)
order by Year(orderdate) asc , Quarter(orderdate) asc;

![image](https://github.com/mohitjaisinghani10/Adventure-Works/assets/85800857/2a7316da-cae4-4f4d-bb47-403648174fa1)

-- 8. Calculate the average number of days required to ship the products

Select Ceiling(avg(datediff(shipdate, orderdate))) as 'Average Days Required for Shipping' from factsales;

![image](https://github.com/mohitjaisinghani10/Adventure-Works/assets/85800857/2c81e66d-4ef4-4add-8216-6fb2a4921523)

-- 9. Find out the total number of Road Bikes sold in France 

SELECT 
    s.EnglishProductSubcategoryName, 
    SUM(fs.OrderQuantity) AS TotalOrderQuantity
FROM 
    factsales fs
LEFT JOIN 
    SalesTerritory st ON fs.SalesTerritoryKey = st.SalesTerritoryKey
LEFT JOIN 
    product p ON p.ProductKey = fs.ProductKey
JOIN 
    subcategory s ON s.ProductSubcategoryKey = p.ProductSubcategoryKey
WHERE 
    s.EnglishProductSubcategoryName = 'Road Bikes' AND st.SalesTerritoryCountry = 'France'
GROUP BY 
    s.EnglishProductSubcategoryName, st.SalesTerritoryCountry;

![image](https://github.com/mohitjaisinghani10/Adventure-Works/assets/85800857/d2ce54cc-9233-4022-94c2-1165d44750cb)

-- 10. Calculate the revenue distribution of Bikes within each sub-category 

SELECT 
    s.EnglishProductSubcategoryName AS 'Bikes',
    TRUNCATE(SUM(fs.salesamount), 2) AS 'Revenue'
FROM
    factsales fs
        LEFT JOIN
    product p ON p.ProductKey = fs.ProductKey
        JOIN
    subcategory s ON s.ProductSubcategoryKey = p.ProductSubcategoryKey
WHERE
    s.EnglishProductSubcategoryName LIKE '%Bikes'
GROUP BY s.EnglishProductSubcategoryName
ORDER BY 'Revenue' DESC;

![image](https://github.com/mohitjaisinghani10/Adventure-Works/assets/85800857/fb98b36d-330e-40ea-a559-cbd71f578f92)









