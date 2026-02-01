```SQL
### CUSTOMER AND ORDERS
### Find total number of orders per customer state.
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
