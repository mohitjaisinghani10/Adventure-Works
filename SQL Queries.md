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
