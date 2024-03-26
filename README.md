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
## 1 Total number of rows in each of the 3 tables in the database

SELECT COUNT(*) AS total_no_of_transactions FROM transactions;


SELECT COUNT(*) AS total_no_of_customers FROM customers;
SELECT COUNT(*) AS total_no_of_category FROM prod_category;

## 2 The total number of transactions that have a return

SELECT COUNT(transaction_id) AS return_s
FROM transactions
WHERE QTY < 0;

# 3 As you would have noticed, the dates provided across the datasets are not 
# in a correct format. As first steps, please convert the date variables into valid date formats
# before proceeding ahead

ALTER TABLE transactions
ADD new_date date;

SELECT (DATE_FORMAT(STR_TO_DATE(tran_date, '%d-%m-%Y0'), '%Y-%m-%d')) AS new_date FROM transactions;
UPDATE transactions set new_date = (DATE_FORMAT(STR_TO_DATE(tran_date, '%d-%m-%Y0'), '%Y-%m-%d'));

SELECT * FROM transactions;

ALTER TABLE transactions
drop tran_date;

SELECT * FROM transactions;


# 5 Which product category does the sub-category "DIY" belong to?

SELECT * FROM prod_category
WHERE prod_subcat = 'DIY';



# Data Interpretation

## 1 Which channel is most frequently used for transactions?

SELECT  COUNT(store_type) AS fre_trans,Store_type
FROM transactions
GROUP BY store_type
ORDER BY COUNT(store_type) DESC;



![Screenshot (525)](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/29a0dfe2-e2ce-4abb-a341-29758f11db80)



## 2 What is the count of male and female customers in the database?

SELECT Gender, COUNT(Gender) AS count_genders
FROM customers
Where Gender IS NOT NULL
GROUP BY Gender;


![Screenshot (523)](https://github.com/kajol105/Ecommerce-retail-analysis-/assets/55199887/25b842ec-1e52-4868-8249-ed9dd81f46b8)
































# 3  From which city do we have the maximun number of customers and how many?
select city_code, COUNT(customer_Id) AS max_no_customers
FROM customers
GROUP BY city_code
ORDER BY COUNT(customer_Id) DESC LIMIT 1;

# 4 How many sub_categories are there under the Books category?

SELECT prod_cat, COUNT(prod_subcat) AS count_subcat
FROM prod_category
WHERE prod_cat = 'Books'
GROUP BY prod_cat;

# 5 What is the maximum quantity of products ever ordered?

SELECT QTY AS 'maximum product sold', transaction_Id FROM transactions
WHERE QTY > 0
ORDER BY QTY DESC;

# 6 What is the total revenue generated in categories electronics and books?
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

# 7 How many customers have > 10 transactions with us, excluding returns?
SELECT * FROM transactions;
SELECT * FROM custtomers;

ALTER TABLE customers
DROP MyUnknownColumn;

SELECT * FROM custtomers A
INNER JOIN transactions B
ON A.customer_Id = B.cust_id;

SELECT customer_Id,COUNT( DISTINCT transaction_id) AS TOTAL_COUNT FROM custtomers A
INNER JOIN transactions B
ON A.customer_id = B.cust_id
GROUP BY customer_Id
HAVING COUNT( DISTINCT transaction_id)>10;

# 8 What is the combined revenue earned from the 'Electronics' and 'Clothing' categories, from 'flagship stores'?

SELECT * FROM prod_category;
SELECT * FROM transactions;

SELECT SUM(total_amt) AS amount FROM transactions T
INNER JOIN prod_category P
ON T.prod_cat_code = P.prod_cat_code
AND T.prod_subcat_code = P.prod_sub_cat_code
WHERE prod_cat IN('Clothing','Electronics') AND Store_type = 'Flagship store';

# Question no. 9 What is the total revenue generated from 'Male' customers in 'Electronics' category? Output should display total revenue by prod sub-cat

SELECT * FROM transactions T
JOIN prod_category P
ON T.prod_subcat_code = P.prod_sub_cat_code
RIGHT JOIN customers C 
ON C.customer_Id = T.cust_id;

SELECT Gender,prod_cat,SUM(total_amt) AS total_revenue FROM transactions T 
JOIN prod_category P
ON T.prod_subcat_code = P.prod_sub_cat_code
RIGHT JOIN customers C
ON C.customer_Id = T.cust_id
WHERE Gender = 'M'
GROUP BY prod_cat
HAVING prod_cat LIKE 'Electronics';

# 10  What is the percenateg of sales and returns by product sub category; display only top  5 sub categories in terms of sales?
 
 SELECT (prod_subcat),SUM(total_amt) AS sales_amount FROM transactions AS T 
 INNER JOIN prod_category AS P
 ON T.prod_cat_code = P.prod_cat_code
 WHERE T.total_amt > 0
 GROUP BY prod_subcat
 ORDER BY sales_amount DESC
 LIMIT 5;

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


# Question NO. 11 Finding the total revenue generated by customers aged 25 to 35 in the last 30 days of transactions

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

# 12 Which product category has seen the maximum value of returns int the last 3 months of transactions 

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


# Question NO.13 Which store type sells the maximum products : by the value of sales amount and quantity sold

SELECT store_type,COUNT(QTY) AS no_of_quantity, SUM(total_amt) AS total_sales FROM transactions
WHERE total_amt > 0
GROUP BY store_type
ORDER BY no_of_quantity DESC
LIMIT 1;



# Business Objective : What are the categories for which average revenue is above the overall average

SELECT prod_cat,ROUND(AVG(total_amt)) FROM transactions T
INNER JOIN prod_category P
ON T.prod_subcat_code = P.prod_sub_cat_code
GROUP BY prod_cat
HAVING AVG(total_amt)> (SELECT AVG(total_amt) FROM transactions);

#---- product subcategories having average revenues than the overall average revenue----#

SELECT P.prod_subcat,ROUND(AVG(total_amt),2) AS averages FROM transactions T 
INNER JOIN prod_category P 
ON T.prod_subcat_code = P.prod_sub_cat_code
GROUP BY prod_subcat
HAVING AVG(total_amt) > (SELECT AVG(total_amt) FROM transactions)
ORDER BY averages DESC;


# Business Objective: Find the average revenue by each subcategory for the categories which among the top 5 categories in terms of quanity sold

SELECT prod_cat,COUNT(Qty) AS quantity_sold FROM transactions T 
INNER JOIN prod_category P 
ON T.prod_cat_code = P.prod_cat_code
WHERE total_amt > 0 
GROUP BY prod_cat
ORDER BY quantity_sold DESC;

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


