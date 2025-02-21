create table customer (
customer_id int4
,first_name varchar(50)
,last_name varchar(50)
,gender varchar(30)
,DOB varchar(50)
,job_title varchar(50)
,job_industry_category varchar(50)
,wealth_segment varchar(50)
,deceased_indicator varchar(50)
,owns_car varchar(30)
,address varchar(50)
,postcode varchar(30)
,state varchar(30)
,country varchar(30)
,property_valuation int4
);

create table transaction (
transaction_id int4
,product_id int4
,customer_id int4
,transaction_date varchar(30)
,online_order varchar(30)
,order_status varchar(30)
,brand varchar(30)
,product_line varchar(30)
,product_class varchar(30)
,product_size varchar(30)
,list_price float4
,standard_cost float4
);

-- Вывести все уникальные бренды, у которых стандартная стоимость выше 1500 долларов.

select distinct brand
from "transaction" t 
where t.standard_cost >= 1500;

-- Вывести все подтвержденные транзакции за период '2017-04-01' по '2017-04-09' включительно.

select *
from "transaction" t
where t.order_status = 'Approved' and t.transaction_date::date between '2017-04-01'::date  and '2017-04-09'::date;

-- Вывести все профессии у клиентов из сферы IT или Financial Services, которые начинаются с фразы 'Senior'.

select distinct job_title
from customer c 
where (c.job_industry_category = 'IT' or c.job_industry_category = 'Financial Services') and c.job_title like 'Senior%';

-- Вывести все бренды, которые закупают клиенты, работающие в сфере Financial Services

select distinct brand
from "transaction" t 
where t.customer_id in (select customer_id
						from customer c
						where c.job_industry_category = 'Financial Services');

-- Вывести 10 клиентов, которые оформили онлайн-заказ продукции из брендов 'Giant Bicycles', 'Norco Bicycles', 'Trek Bicycles'.

select distinct c.customer_id, c.first_name, c.last_name 
from customer c
where c.customer_id in (select customer_id
						from "transaction" t 
						where t.online_order = 'True' and t.brand in ('Giant Bicycles', 'Norco Bicycles', 'Trek Bicycles'))
limit 10;

-- Вывести всех клиентов, у которых нет транзакций.

select c.customer_id, c.first_name, c.last_name, t.transaction_id 
from customer c
left join "transaction" t on c.customer_id = t.customer_id 
where t.customer_id is null;

-- Вывести всех клиентов из IT, у которых транзакции с максимальной стандартной стоимостью.

select c.customer_id, c.first_name, c.last_name, c.job_industry_category, t.standard_cost 
from customer c
left join "transaction" t on c.customer_id = t.customer_id 
where t.standard_cost = (select max(standard_cost) from transaction) and c.job_industry_category = 'IT';

-- Вывести всех клиентов из сферы IT и Health, у которых есть подтвержденные транзакции за период '2017-07-07' по '2017-07-17'.
select *
from customer c 
where c.job_industry_category in ('IT', 'Health') and c.customer_id in (select t.customer_id
																		from  "transaction" t
																		where t.order_status = 'Approved' and t.transaction_date::date between '2017-07-07'::date and '2017-07-17'::date)
