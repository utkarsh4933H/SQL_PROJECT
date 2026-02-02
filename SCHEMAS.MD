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
### MONTHLY order count( year_wise)


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
###percentage of cancel orders 

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
`` Sql
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



























