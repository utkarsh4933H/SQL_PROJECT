```SQL
### CUSTOMER AND ORDERS

create table Public.customer(
 customer_id text,
 customer_unique_id text,
 customer_zip_code text,
 CUSTOMER_CITY text,
 customer_state text
);

select count(customer_state),customer_state from public.customer
group by customer_state;
```
