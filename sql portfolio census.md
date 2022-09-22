
``` 
select * from dataset1
select * from dataset2
```

#####  Number Of Rows Into  dataset1 and dataset2 #####

``` sql
select count(*) from  dataset1
select count(*) from  dataset2
```

#####  Sum of population of India #####

``` sql
select sum(population) as Population from dataset2
```

##### select state name with letter 'a' #####

``` sql
select distinct(state) from dataset1
where state like 'a%' 
``` 

#####  avg growth  #####

``` sql
select state, avg(growth)*100 as avg_growth from dataset1
group by state;
``` 

#####  avg sex ratio #####

``` sql
select state,round(avg(sex_ratio),0) avg_sex_ratio from dataset1
group by state
order by avg_sex_ratio desc;
``` 

#####  top 3 state showing highest growth ratio #####

``` sql
select state,avg(growth)*100 avg_growth from dataset1
group by state 
order by avg_growth desc
limit 3;
``` 

#####  bottom 3 state showing lowest sex ratio #####

``` sql
select  state,round(avg(sex_ratio),0) avg_sex_ratio from dataset1
group by state
order by avg_sex_ratio asc
Limit 3;
``` 

#####  joining both table #####
##### total males and females #####
``` sql
select d.state,sum(d.males) total_males,sum(d.females) total_females from
(select c.district,c.state state,round(c.population/(c.sex_ratio+1),0) males, round((c.population*c.sex_ratio)/(c.sex_ratio+1),0) females from
(select a.district,a.state,a.sex_ratio/1000 sex_ratio,b.population from dataset1 a inner join dataset2 b on a.district=b.district ) c) d
group by d.state;
``` 

#####  Calculating state wise literacy rates by grouping all the states #####

** there is no related column in our data which states no. of children(age 0-6),so  results are slightly lesser than the actual literacy rates

``` sql
select h.state,sum(literate_people) total_literate_pop,sum(illiterate_people) total_lliterate_pop from 
(select d.district,d.state,round(d.literacy_ratio*d.population,0) literate_people,
round((1-d.literacy_ratio)* d.population,0) illiterate_people from
(select a.district,a.state,a.literacy/100 literacy_ratio,b.population from dataset1 a
inner join dataset2 b on a.district=b.district) d) H
group by h.state
``` 
##### population in previous census #####

``` sql
select sum(m.previous_census_population) previous_census_population,sum(m.current_census_population) current_census_population from
(select e.state,sum(e.previous_census_population) previous_census_population,sum(e.current_census_population) current_census_population from
(select d.district,d.state,round(d.population/(1+d.growth),0) previous_census_population,d.population current_census_population from
(select a.district,a.state,a.growth growth,b.population from  dataset1 a inner join  dataset2 b on a.district=b.district) d) e
group by e.state)m
``` 

#####  use of rank window function #####
 
** output top 3 districts from each state with highest literacy rate
``` sql
select a.* from
(select district,state,literacy,rank() over(partition by state order by literacy desc) rnk from dataset1) a
where a.rnk in (1,2,3) order by state
```
