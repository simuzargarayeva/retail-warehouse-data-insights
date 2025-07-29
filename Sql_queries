select * from rw_sales;
--Q1. Top 5 best-selling products by retail sales
 
select item_name,sum(retail_sales) as total_sales
from rw_sales 
group by item_name
having sum(retail_sales) > 0
order by total_sales desc
fetch first 5 rows only;

--Q2. Average monthly retail sales by supplier

select month,supplier,
avg(retail_sales) as avg_retail
from rw_sales
group by month,supplier;

--Q3. Monthly trend analysis of warehouse sales

select year,month,sum(warehouse_sales) as total_warehouse
from rw_sales
group by month,year
order by year desc;

--Q4. Products with the highest difference between retail and warehouse sales

select item_code,item_name,
sum(retail_sales) as total_retail,
sum(warehouse_sales) as total_warehouse,
abs(sum(retail_sales)-sum(warehouse_sales)) as sales_difference
from rw_sales
group by item_code,item_name
having sum(retail_sales)>0 and sum(warehouse_sales)>0
order by sales_difference desc
fetch first 10 rows only;

--Q5. For each product, identify whether retail sales or warehouse sales are higher overall.

select item_code,item_name,
sum(retail_sales) as total_retail,
sum(warehouse_sales) as total_warehouse,
case 
  when sum(retail_sales) > sum(warehouse_sales) then 'Retail_dominant'
    when sum(warehouse_sales) > sum(retail_sales) then 'Warehouse_dominant'
      else 'Equal_sales'
        end as dominant_channel
        from rw_sales
        group by item_code,item_name;

--Q6. Find Peak Retail Sales Month per Product

with ranked_sales as
(select item_code,item_name,month,
sum(retail_sales) as monthly_retail,
row_number() over 
(partition by item_code
order by sum(retail_sales) desc)
as rn
from rw_sales
group by item_code,item_name,month)

select item_code, item_name, month,
 monthly_retail
 from ranked_sales
  where rn=1;


--Q7. List all distinct products sold by the top 3 suppliers ranked by their overall retail sales.

with supplier_sales as
(select supplier, 
sum(retail_sales) as total_retail
from rw_sales
group by supplier),

top_suppliers as
(select supplier from supplier_sales
order by total_retail desc 
fetch first 3 rows only)

select distinct(r.supplier),r.item_code,r.item_name
from rw_sales r
join top_suppliers t on r.supplier=t.supplier
where retail_sales > 0
order by r.supplier, r.item_code;

--Q8. Pareto analysis: products contributing 80% of total sales

with product_sales as
(select item_name,
sum(retail_sales) as total_retail
from rw_sales
group by item_name),

ranked_product as 
(select item_name,total_retail,
sum(total_retail) over(order by total_retail desc) as cumulative_sales,
sum(total_retail) over() as overall_total
from product_sales),

final as (select 
item_name, total_retail,
cumulative_sales,overall_total,
round(cumulative_sales/overall_total*100,2) as
cumulative_percentage
from ranked_product)

select * from final
where cumulative_percentage <= 80;











