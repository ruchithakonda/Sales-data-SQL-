# Sales-data-SQL-
The sales data was extracted from Kaggle .there are 113,036 rows and 18 columns in this dataset.This dataset contains  transactional  sales records across  multiple years, countries, and product categories. It can be used for  data analysis, reporting, and business insights.Such as revenue, customer demographics, and product performance.
/* Import the data */
proc import datafile="/mnt/data/sales_data/sales_data.csv"
    out=salesdata
    dbms=csv
    replace;
    getnames=yes;
run;

/* Create a sample table   using JOIN */
data country_region;
    input Country $ Region $;
    datalines;
Canada NorthAmerica
Australia APAC
Germany Europe
France Europe
UnitedStates NorthAmerica
;
run;

/* Using PROC SQL*/
proc sql;

/*Using SELECT statement with WHERE, ORDER BY, GROUP BY clause */
create table sales_summary as
select Country, Product_Category,
       sum(Revenue) as Total_Revenue,
       avg(Profit) as Avg_Profit
from salesdata
where Year >= 2014
group by Country, Product_Category
order by Total_Revenue desc;

/* using  JOINS: INNER, LEFT, RIGHT */
create table join_inner as
select a.Country, a.Total_Revenue, b.Region
from sales_summary a
inner join country_region b
on a.Country = b.Country;

create table join_left as
select a.Country, a.Total_Revenue, b.Region
from sales_summary a
left join country_region b
on a.Country = b.Country;

create table join_right as
select a.Country, a.Total_Revenue, b.Region
from sales_summary a
right join country_region b
on a.Country = b.Country;

/* Using Subquery: To find countries above avg revenue */
create table high_revenue_countries as
select Country, Total_Revenue
from sales_summary
where Total_Revenue > (
      select avg(Total_Revenue)
      from sales_summary);

/*  Creating a VIEW for analysis */
create view top_products as
select Product, sum(Revenue) as Revenue
from salesdata
group by Product
having Revenue > 10000;

/*  Optimising queries with an index  function*/
create index idx_country on salesdata(Country);
create index idx_year on salesdata(Year);

quit;
