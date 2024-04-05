# E-COMMERCE RETAIL DATA ANALYSIS
E-commerce retail data analysis refers to the process of examining and interpreting data related to online reatil operations to gain insights into customer behavior,sales trends,inventory management,marketing effectiveness and other aspects of business. Using the sql to extract,transform, and analyze data from ecommerce datasets stored on GitHub repositories. This approach entails querying tables containing information such as customer orders,product details,sales transactions,identify aptterns and ehance overall business performance withing the ecomerce retail sector.

# DATASETS: Customer.xlsx,Transactions.xlsx,Prod_cat_info.xlsx

# Customers:
This file contains the information about the customers and lists basi. There are 4 attributes in the customer table are customer_Id, Gender, DOB, city_code. In this table there are 4 columns and 5647 rows.

# Transactions: 
A transaction file is used to hold data during transaction processing. In this transaction table there are 10 variables and 23054 records. The variables are transaction_id, cust_id, tran_date, prod_subcat_code, prod_cat_code, Qty, Rate, Tax, total_amt, store_type. This file is collected on an ongoing basis as part of an organizations daily business operations. The data is often associated with customer sales , payments type, product categories which are used for the whole project analysis.

# Product category info:
This file contains the information about the  product categories and subproduct categories. There are 4 attributes and 23 rows in the product category table.




# Data Cleaning
-----------------------------------------------------------------------------------------------------------------------------
Create DATABASE DB_ecommerceanalysis;
USE DB_ecommerceanalysis;

# Organizational Objecives
----------------------------------------------------------------------------------------------------------------------------
### 1 Total number of rows in each of the 3 tables in the database
----------------------------------------------------------------------------------------------------------------------------
SELECT COUNT(*) AS total_no_of_transactions FROM transactions;














SELECT COUNT(*) AS total_no_of_customers FROM customers;

![image](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/772d6bca-ee67-45ac-af3b-719bf806765a)



SELECT COUNT(*) AS total_no_of_category FROM prod_category;

![image](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/25a5ce28-6481-469a-a84d-3fc4dda3cb7d)




### 2 The total number of transactions that have a return

SELECT COUNT(transaction_id) AS return_s
FROM transactions
WHERE QTY < 0;

![image](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/dbff76d7-f3ef-489f-b4fe-188474abb800)




### 3 As you would have noticed, the dates provided across the datasets are not 
### in a correct format. As first steps, please convert the date variables into valid date formats
### before proceeding ahead

ALTER TABLE transactions
ADD new_date date;

SELECT (DATE_FORMAT(STR_TO_DATE(tran_date, '%d-%m-%Y0'), '%Y-%m-%d')) AS new_date FROM transactions;
UPDATE transactions set new_date = (DATE_FORMAT(STR_TO_DATE(tran_date, '%d-%m-%Y0'), '%Y-%m-%d'));

SELECT * FROM transactions;

ALTER TABLE transactions
drop tran_date;

SELECT * FROM transactions;

![image](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/80c61749-09ed-4f93-a156-5829f046aa0a)




###  4 What is the time range of the transaction data available for analysis? Show the output in number of days, months and years simuntaneously in different columns.
SELECT new_date,CURDATE(),month(CURDATE()),day(new_date),year(new_date) FROM transactions;
SELECT MIN(new_date) AS minimum_date, MAX(new_date) as maximum_date FROM transactions;

![image](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/6348fc91-ae5c-491c-84b6-1eafe98c2e5c)


![image](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/0902f390-6dee-44a6-bdb7-fc9ca3bedc90)



### 5 Which product category does the sub-category "DIY" belong to?

SELECT * FROM prod_category
WHERE prod_subcat = 'DIY';

![image](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/94d200a1-b6db-4b16-bd8b-bff93b4745ea)



# Data Interpretation

### 1 Which channel is most frequently used for transactions?

SELECT  COUNT(store_type) AS fre_trans,Store_type
FROM transactions
GROUP BY store_type
ORDER BY COUNT(store_type) DESC;



![image](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/404af13d-0473-4026-afbc-55a4bf0daf08)




Here, from the above solution, the most frequently used channel is e-Shop with a count of 4595. Second the most 
used store_type is flagship store with 2301 buyers.


### 2 What is the count of male and female customers in the database?

SELECT Gender, COUNT(Gender) AS count_genders
FROM customers
Where Gender IS NOT NULL
GROUP BY Gender;




![image](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/442fb7b4-d43b-4ec5-b9e1-fa696a782ff3)


This SQL query selects he gender column from the customers table and count the occurences of gender value.
The COUNT function used here to count the number of unique values in the column and to  the find "Customer Gender Analysis" with the GROUP BY function 




### 3  From which city do we have the maximun number of customers and how many?
select city_code, COUNT(customer_Id) AS max_no_customers
FROM customers
GROUP BY city_code
ORDER BY COUNT(customer_Id) DESC LIMIT 1;

![image](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/5f15d2fc-6864-4637-8df7-0555908f9d80)


This sql query selects the city_code Column from the customers table and count the number of customers through customer_id column for each city. It then orders the results by count of customers in descending order and limits the result to only the first row, which is giving the city with maximum number of customers.

### 4 How many sub_categories are there under the Books category?

SELECT prod_cat, prod_subcat 
FROM prod_category
WHERE prod_cat = 'Books'
GROUP BY prod_subcat;





![image](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/524610bb-5d35-4aa4-b3a2-ec0ff0a5d323)






According to the result 6 sub categories are there under the books category.They are Fiction, Academic, Non-Fiction, Children, Comics and DIY. In this objective Where is used for filtering the data and GROUP BY used for grouping the identical recors together.






### 5 What is the maximum quantity of products ever ordered?

SELECT P.prod_cat, COUNT(qty) AS no_product_category 
FROM Transactions T
JOIN prod_category P
ON T.prod_subcat_code = P.prod_sub_cat_code GROUP BY P.prod_cat;




![image](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/ec36ca81-ae57-4b3c-9937-c02c51b00dc5)

![image](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/dd01a6f4-a575-4198-92e6-71cde8ace2f9)









The query joins 'Transactions' with 'prod_category' to count products by category. Grouped by product category (P.prod_cat), it provides insights into the distribution of product categories based on the quantity of transactions. This analysis aids in understanding purchasing patterns and helps optimize inventory for different product categories to enhance sales and customer satisfaction.











### 6 What is the total revenue generated in categories electronics and books?
select * from transactions;
select * from prod_category;

SELECT * from prod_category A
LEFT JOIN transactions B
ON A.prod_cat_code = B.prod_cat_code;

SELECT prod_cat,sum(total_amt)  AS total_revenue FROM prod_category A
LEFT JOIN transactions B
ON A.prod_cat_code = B.prod_cat_code
WHERE prod_cat IN('Electronics','Books')
GROUP BY prod_cat;

![image](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/27672a55-d82e-4ad0-ad5a-005ef59bc437)






According to the result the total revenue generated in categories Electronics and Books are 27130794.27
36592713.68 . In this objective Sum is used for find the net total revenue. LEFT JOIN  is used for joining  the two table Transactons and Prod_category, Where is used for filter the data and GROUP BY is used to club together the identical rows present in the prod_cat column.



### 7 How many customers have > 10 transactions with us, excluding returns?

SELECT * FROM transactions;
SELECT * FROM customers;

ALTER TABLE customers
DROP MyUnknownColumn;


SELECT * FROM customers A
INNER JOIN transactions B
ON A.customer_Id = B.cust_id;

SELECT customer_Id,COUNT( DISTINCT transaction_id) AS TOTAL_COUNT FROM customers A
INNER JOIN transactions B
ON A.customer_id = B.cust_id
GROUP BY customer_Id
HAVING COUNT( DISTINCT transaction_id)>10;



![image](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/b47fad2c-3c20-4794-93bc-b2d299463662)





This query generates the 6 customers are having greater than 10 transactions, excluding the returns. Having clause is used in this query to filter the data with the given condition.



### 8 What is the combined revenue earned from the 'Electronics' and 'Clothing' categories, from 'flagship stores'?


SELECT SUM(total_amt) AS amount FROM transactions T
INNER JOIN prod_category P
ON T.prod_cat_code = P.prod_cat_code
AND T.prod_subcat_code = P.prod_sub_cat_code
WHERE prod_cat IN('Clothing','Electronics') AND Store_type = 'Flagship store';



![image](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/1c5e28c2-4e45-4464-9a89-53d9d7663d93)




The query calculates the total revenue from transactions table  related to 'electronics' and 'clothing' product categories, specifically for the 'Flagship store' store type.  The result 1859142.6099999999 is the combined revenue earned from the "Electronics "and "Clothing"categories from "Flagship store". In this query  inner Join is used for joining the two table transactons and prod_category with the help of same columns, it provides insights into revenue generated from these categories at flagship stores.




### Question no. 9 What is the total revenue generated from 'Male' customers in 'Electronics' category? Output should display total revenue by prod sub-cat

SELECT * FROM transactions T
JOIN prod_category P
ON T.prod_subcat_code = P.prod_sub_cat_code
RIGHT JOIN customers C 
ON C.customer_Id = T.cust_id;

![Screenshot (537)](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/aba26af1-f81b-45fd-8888-4af284fc24a0)

![Screenshot (538)](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/b6b76f4a-383d-407b-87b3-c21762335c40)


SELECT Gender,prod_cat,SUM(total_amt) AS total_revenue FROM transactions T 
JOIN prod_category P
ON T.prod_subcat_code = P.prod_sub_cat_code
RIGHT JOIN customers C
ON C.customer_Id = T.cust_id
WHERE Gender = 'M'
GROUP BY prod_cat
HAVING prod_cat LIKE 'Electronics';



![Screenshot (539)](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/6de5cd37-fb17-4160-9d66-077f9a3c016e)




 The total revenue generated from this query is 1845889.2400000007 from 'Male' customers in 'Electronics' .In this objective Sum is used for find the total revenue. Right Join is used for join the two table Transactons and Customers. Where is used for filter the data.


### 10  What is the percenateg of sales and returns by product sub category; display only top  5 sub categories in terms of sales?
 
 SELECT (prod_subcat),SUM(total_amt) AS sales_amount FROM transactions AS T 
 INNER JOIN prod_category AS P
 ON T.prod_cat_code = P.prod_cat_code
 WHERE T.total_amt > 0
 GROUP BY prod_subcat
 ORDER BY sales_amount DESC
 LIMIT 5;



 
![Screenshot (540)](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/b9038c90-f353-4efb-866c-2b16fda9aade)

 WITH perABS AS(SELECT prod_subcat,
                                ABS(SUM(CASE WHEN total_amt < 0 THEN total_amt ELSE 0 END)) AS returns_v,
                                SUM(CASE WHEN total_amt > 0 THEN total_amt ELSE 0 END) AS Sales
FROM transactions AS T
INNER JOIN prod_category AS P
ON T.prod_cat_code = P.prod_cat_code
GROUP BY prod_subcat
ORDER BY sales DESC
LIMIT 5)

SELECT prod_subcat,ROUND(((returns_v/(returns_v+sales))*100),2) AS Return_percent,
ROUND(((sales/(returns_v+sales))*100),2) AS Sales_percent from perABS;

![Screenshot (541)](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/cda8b82f-2239-4b7f-8f60-4e50225c318a)






According to the result we get top 5 percentage of sale and returns by product sub category such as Women, Mens, Comics, Children and Academic. In this objective CTEs are used for finding the solution. inner Join is used for join the two table Transactons and Prod_category. Where is used for filtering the data. Group by and Order by are used for sorting the data.





### Question NO. 11 Finding the total revenue generated by customers aged 25 to 35 in the last 30 days of transactions

alter table customers
 add  new_DOB date;
 SELECT (DATE_FORMAT(STR_TO_DATE(DOB, '%d-%m-%Y'), '%Y-%m-%d')) AS new_DOB  from customers;
UPDATE  customers set new_DOB= (DATE_FORMAT(STR_TO_DATE(DOB, '%d-%m-%Y'), '%Y-%m-%d')) ;

alter table customers
drop DOB;
alter table transactions
drop new_DOB;
select * from customers;
select * from transactions;
select new_date from transactions
group by new_date
order by new_date desc
limit 30;
with ABC
as (select new_date,sum(total_amt) as Total_amount from customers as cust
inner join transactions as tran
on tran.cust_id=cust.customer_id
where timestampdiff(year,new_DOB,curdate()) between 25 and 35
group by new_date
order by new_date desc limit 5)
select sum(Total_amount) as final_revenue from ABC;



![Screenshot (542)](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/8b8d1170-35c0-4872-b6d3-43f34e7e1a07)


This  query calculates the total revenue generated from transactions by customers aged between 25 and 35 years old, considering the top 30 transaction dates based on total amount. We are using here, Common Table Expression (CTE) named 'ABC' to first identify the top transaction dates within the specified age range. The main query then sums up the 'Total_amount' from the CTE to provide the final revenue generated from this customer segment. 






### 12 Which product category has seen the maximum value of returns int the last 3 months of transactions 

SELECT prod_cat,COUNT(Qty) AS No_of_returns FROM transactions T
INNER JOIN prod_category P
ON T.prod_cat_code = P.prod_cat_code
WHERE total_amt < 0 AND timestampdiff(month,'2014-09-01',new_date)=3
GROUP BY prod_cat;

WITH ABC AS
(SELECT prod_cat,transaction_id,total_amt FROM transactions T 
INNER JOIN prod_category P
ON T.prod_cat_code = P.prod_cat_code
WHERE total_amt < 0 AND timestampdiff(month,'2014-09-91',new_date)=3)
SELECT ABS(SUM(total_amt)) AS return_amount_cat FROM ABC;

![Screenshot (543)](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/f8bb7497-b28d-4617-9bf2-567c928d84c2)


This result is showing that the Home and kitchen has the maximum value of returns in the last 3 months of transactions. In this query Inner Join is used for join the two table Transactons and Prod_category. Where is used for filtering the data. Group by used for sorting the data,ABS is used for finding the absolute amount from the total_amount.







### Question NO.13 Which store type sells the maximum products : by the value of sales amount and quantity sold

SELECT store_type,COUNT(QTY) AS no_of_quantity, SUM(total_amt) AS total_sales FROM transactions
WHERE total_amt > 0
GROUP BY store_type
ORDER BY no_of_quantity DESC
LIMIT 1;


![Screenshot (544)](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/fb2c70f1-3bb7-4897-8720-1f6e320a7fff)




This query shows the e-shop store type with the highest number of sold products and corresponding total sales amount from the 'Transactions' table. It filters transactions where the total amount is greater than zero to focus on successful sales. The results are grouped by store type and sorted in descending order based on the number of products sold



### Business Objective : What are the categories for which average revenue is above the overall average

SELECT prod_cat,ROUND(AVG(total_amt)) FROM transactions T
INNER JOIN prod_category P
ON T.prod_subcat_code = P.prod_sub_cat_code
GROUP BY prod_cat
HAVING AVG(total_amt)> (SELECT AVG(total_amt) FROM transactions);



![Screenshot (545)](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/04867efd-82b2-41e9-9b2d-8ee217eb8f5f)


#---- product subcategories having average revenues than the overall average revenue----#

SELECT P.prod_subcat,ROUND(AVG(total_amt),2) AS averages FROM transactions T 
INNER JOIN prod_category P 
ON T.prod_subcat_code = P.prod_sub_cat_code
GROUP BY prod_subcat
HAVING AVG(total_amt) > (SELECT AVG(total_amt) FROM transactions)
ORDER BY averages DESC;

![Screenshot (546)](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/934efe91-fd1f-4948-898a-d8fb6f29cdfe)












### Business Objective: Find the average revenue by each subcategory for the categories which among the top 5 categories in terms of quanity sold

SELECT prod_cat,COUNT(Qty) AS quantity_sold FROM transactions T 
INNER JOIN prod_category P 
ON T.prod_cat_code = P.prod_cat_code
WHERE total_amt > 0 
GROUP BY prod_cat
ORDER BY quantity_sold DESC;

![Screenshot (548)](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/e30f13f3-09d2-4d91-9018-fc3512a44e30)


SELECT prod_cat,prod_subcat,ROUND(SUM(total_amt),3) AS total_amount,ROUND(AVG(total_amt),3) AS avg_amount FROM transactions T
INNER JOIN prod_category P
ON T.prod_cat_code = P.prod_cat_code
WHERE total_amt > 0 AND prod_cat IN('Books','Electronics','Home and Kitchen','Footwear','Clothing')
GROUP BY prod_cat,prod_subcat
ORDER BY case WHEN prod_cat = 'Books' THEN 1
              WHEN prod_cat = 'Electronics' THEN 2
              WHEN prod_cat = 'Home and Kitchen' THEN 3
              WHEN prod_cat = 'Footwear' THEN 4
              ELSE 5
              END


![Screenshot (549)](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/a70167cf-63c5-4741-9ab2-9eaabd3f212d)
