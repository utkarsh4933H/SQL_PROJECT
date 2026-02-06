```SQL
### CUSTOMER AND ORDERS
### 1 Find total number of orders per customer state.
create table customer(
 customer_id varchar(100) primary key,
 customer_unique_id varchar(100),
 customer_zip_code_prefix int,
 CUSTOMER_CITY varchar(50),
 customer_state varchar(10)
);
 select * from customer;

create table orders(
order_id varchar (100) primary key,
customer_id varchar (100),
order_status char (50),
order_purchase_timestamp timestamp,
order_approved_at timestamp,
order_delivered_carrier_date timestamp,
order_delivered_customer_date timestamp,
order_estimated_delivery_date timestamp
);
select * from orders;

select customer_state,count(order_id) from customer c
join orders o on o.customer_id = c.customer_id
group by customer_state
```

```SQL
###2 TOP 10  cities with hightest number of customer 

select customer_city, count(customer_id) as total_customer from customer 
group by customer_city
order by total_customer desc
limit 10
```
```SQL
###3 customer who place more than 3 orders 

select c.customer_id, count(order_id) from customer c
join orders o on c.customer_id = o.customer_id
group by c.customer_id
having count(order_id) > 3;

```
```SQL
###4 AVERAGE ORDER VALUE PER CUSTOMER

create table payment(
order_id varchar(100),
payment_sequential int,
payment_type char(50),
payment_installments int,
payment_value numeric (10,2)
);

select customer_id, avg(payment_value) as order_avg_value from orders o
join payment p on o.order_id = p.order_id
group by customer_id
```
```SQL
### 5. CUSTOMER who never gave a review 


create table review(
review_id varchar(100),
order_id varchar(100),
review_score int ,
review_comment_title  varchar(255),
review_comment_message varchar(1000),
review_creation_date timestamp,
review_answer_timestamp timestamp
);
select * from review



select  o.customer_id from orders o 
join review r on o.order_id= r.order_id
where r.review_id is null;
```
```SQL
### 6. MONTHLY order count( year_wise)


select extract (year from order_purchase_timestamp) as year,
       extract (month from order_purchase_timestamp) as month ,
count(order_id) from orders
group by month, year
order by month, year;
```
```SQL
###7. average delivery time (day) per state

select c.customer_state, avg(o.order_delivered_customer_date - o.order_purchase_timestamp) as average_delivery_date from orders o 
join customer c on c.customer_id = o.customer_id
where o.order_delivered_customer_date is not null
group by c.customer_state;
```
```SQL
### 8. ORDERS DELIVER LATE 

select order_id , order_delivered_customer_date, order_estimated_delivery_date
from orders
where  order_delivered_customer_date > order_estimated_delivery_date
```
```SQL
### 9. percentage of cancel orders 

select count(case when order_status = 'canceled' then 1 end) * 100.0
/count(order_status) as canceled_percentage
from orders;
```
```SQL
### 10. PEAK ORDER HOURS

select extract (hour from order_purchase_timestamp) as hour,count(order_id) as total_order from orders
group by hour
order by  total_order desc
limit 1 ;
```
```SQL
### 11. Total revenue per payment type

select sum(payment_value) as total_revenue,payment_type from payment
group by payment_type
order by total_revenue;
```
``` Sql
### 12. Average payment value per order

select avg(payment_value) as average_payment,order_id from payment
group by order_id;
```

```SQL
### 13, orders paid in multiple order

select  payment_installments,order_id from payment
where payment_installments > 1
group by order_id,payment_installments;
```
```SQL
### 14. TOP 5 REVENUE - GENERATING STATES

SELECT c.customer_state,sum(payment_value) as total_revenue from customer c
join orders o on c.customer_id = o.customer_id
join payment p on o.order_id = p.order_id
group by customer_state
order by total_revenue desc
limit 5;
```
```SQL
### 15. MONTHLY REVENUE TREND 

SELECT DATE_TRUNC('MONTH', o.order_purchase_timestamp) as month ,sum(p.payment_value) 
as revenue from orders o join payment p on o.order_id = p.order_id
group by month 
order by month desc;
```
```SQL
### 16. top 10 best selling product categories

select product_category_name , count(order_id) as max_order from order_item oi
join product po on oi.product_id = po.product_id
group by po.product_category_name 
order by  max_order desc
limit 10
```
```SQL
### 17. average product price per category

select product_category_name,avg(price) from order_item oi
join product po on oi.product_id = po.product_id
group by product_category_name
```
```SQL
### 18. product sold but never reviewed 

select oi.product_id from order_item oi
join review r on r.order_id = oi.order_id
where r.review_id is null
```

```SQL
### 19. category with highest delivery delay 
select avg(o.order_delivered_customer_date - o.order_estimated_delivery_date) as delay_delivery ,po.product_category_name from customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
where order_delivered_customer_date > order_estimated_delivery_date 
group by product_category_name,o.order_estimated_delivery_date,o.order_delivered_customer_date
order by delay_delivery
```
```SQL 
###20. most expensive product sold in each category

select distinct o.customer_id from orders o
join payment p on o.order_id = p.order_id
group by o.customer_id
having avg(p.payment_value) > (select avg(payment_value) from payment);
``` 
```SQL
### 21. FIND TOTAL REVENUE PER SELLER
select oi.seller_id,sum(price) from order_item oi
group by oi.seller_id
```
```SQL
### 22. SELLER WHO DELIVERED ORDERS MORE THEN 5 STATES

select oi.seller_id,count (distinct c.customer_state) from customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
group by oi.seller_id 
having count (distinct c.customer_state )> 5;
```
```SQL
### 23. top 5 seller by numbers of orders 
select oi.seller_id,count(oi.order_id) as total_orders from customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
group by oi.seller_id
order by total_orders desc
limit 5
```
```SQL
### 24. average shipping cost per seller 
select oi.seller_id,avg(oi.freight_value) as total_orders from customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
group by oi.seller_id 
```
```SQL
### 25. SELLER WITH AVERAGE REVIEW SCORE < 3 
select oi.seller_id,avg(r.review_score) as total_orders from customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
group by oi.seller_id 
having avg(r.review_score)> 3;
```
```SQL 
### 26. AVERAGE REVIEW SCORE PER PRODUCT 
select po.product_category_name,avg(r.review_score) as total_orders from customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
group by po.product_category_name
```
```SQL 
### 27. PRODUCT WITH HIGHEST NUMBER OF 1 - STAR REVIEW 
select OI.PRODUCT_ID,R.REVIEW_SCORE from customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
WHERE R.REVIEW_SCORE = 1
group by OI.PRODUCT_ID,R.REVIEW_SCORE
```
```SQL 
### 28. CUSTOMERS WHO GAVE ONLY 5 STARS RATING 
select C.CUSTOMER_ID,R.REVIEW_SCORE from customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
WHERE R.REVIEW_SCORE = 5
group by C.CUSTOMER_ID,R.REVIEW_SCORE
```
```SQL 
### 29. PERCENTAGE OF POSITIVE REVIEWS(4 AND 5)
select (SUM(CASE WHEN REVIEW_SCORE IN (4,5) THEN 1 ELSE 0 END ) * 100.0 / COUNT(*)) AS POSITIVE_REVIEW_PERCENTAGE  from customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
```
```SQL
### 30. STATE WITH LOWEST AVERAGE REVIEW SCORE 
select C.CUSTOMER_STATE,AVG(R.REVIEW_SCORE) AS AVG_SCORE from customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
GROUP BY C.CUSTOMER_STATE 
ORDER BY AVG_SCORE 
```
```SQL
### 31. CUSTOMERS WHOSE ORDER VALUE > OVERALL AVERAGE ORDER VALUE 
with order_value as (
select o.customer_id, sum(oi.price + oi.freight_value) AS order_value from customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
group by o.customer_id
)
select * from order_value
where order_value > (select avg(order_value) from order_value);
```
```SQL
### 32. SELLER WHOSE REVENUE > AVERAGE SELLER REVENUE
WITH seller_revenue AS (
SELECT oi.seller_id,sum(oi.price + oi.freight_value) as seller_revenue from customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
group by oi.seller_id
)
select * from seller_revenue
where seller_revenue > (select avg(seller_revenue) from seller_revenue);
```
```SQL
### 33. TOP PRODUCT PER CATEGORY BASED ON SALES VALUE 
WITH category_sale as (
select po.product_category_name,oi.product_id,sum(oi.price) as total_value from customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
group by po.product_category_name,oi.product_id
),
ranked as (
select * , rank() over (partition by product_category_name
order by total_value desc )as rnk from category_sale
)
select * from ranked
where rnk = 1;
```
```SQL
### 34. CUSTOMERS WHO ORDERED FROM MORE THAN 3 SELLERS
select c.customer_id,count(distinct oi.seller_id) from customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
group by c.customer_id
having count(distinct oi.seller_id) > 3;
```
```SQL
### 35. state where average delivery time > national average
with delivery_time  as(
select c.customer_state,(o.order_delivered_customer_date - o.order_purchase_timestamp) 
as days from customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
where o.order_delivered_customer_date is not null
)
select customer_state from delivery_time
group by customer_state
having avg(days) > (select avg(days) from delivery_time);
```
```SQL
### 36. RANK PRODUCT CATEGORIES BY TOTAL REVENUE 
SELECT po.product_category_name,sum(oi.price),rank() over (partition by product_category_name
order by sum(oi.price)  desc )as rnk from  customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
group by po.product_category_name
```
``` SQL 
### 37. FIRST AND LAST ORDER DATE FOR EACH CUSTOMER
SELECT C.CUSTOMER_ID,MIN(ORDER_PURCHASE_TIMESTAMP),MAX(ORDER_PURCHASE_TIMESTAMP) from customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
GROUP BY C.CUSTOMER_ID
```
```SQL
### 38. RUNNING TOTAL OF REVENUE BY MONTH 
with total_revenue as (
SELECT DATe_TRUNC('month', o.order_purchase_timestamp) as month , sum(oi.price) as revenue 
from customer c
join orders o  on c.customer_id=o.customer_id
join review r on r.order_id=o.order_id
JOIN payment p on p.order_id = r.order_id
join order_item oi on r.order_id = oi.order_id
join product po on oi.product_id = po.product_id
group by month
)
select month,revenue,sum(revenue) over(order by month) as running_total from total_revenue
```















