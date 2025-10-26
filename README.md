# SQL-Questions
14 different sql questions, Easy to hard 


/*1. Task: Create a list of all the different (distinct) replacement costs of the films.
Question: What's the lowest replacement cost?*/

SELECT DISTINCT(replacement_cost) FROM FILM
order by replacement_cost limit 1;

/*2. Task: Write a query that gives an overview of how many films have replacements costs in the following cost ranges
low: 9.99 - 19.99
medium: 20.00 - 24.99
high: 25.00 - 29.99
Question: How many films have a replacement cost in the "low" group?*/

select 
case 
when replacement_cost between 9.99 AND 19.99
THEN 'LOW'
when replacement_cost between 20.00 AND 24.99
THEN 'MEDIUM'
when replacement_cost between 25.00 AND 29.99
THEN 'HIGH'
END AS cost_range, count(*)
from film
group by cost_range;

/* 3. Task: Create a list of the film titles including their title, length, and category name ordered descendingly by length. 
Filter the results to only the movies in the category 'Drama' or 'Sports'.
Question: In which category is the longest film and how long is it?*/

select title, length, name 
from film f
left join film_category fc
on fc.film_id = f.film_id
left join category c
on c.category_id = fc.category_id
where name = 'Sports' OR name = 'Drama'
order by length desc;

/* 4. Task: Create an overview of how many movies (titles) there are in each category (name).
Question: Which category (name) is the most common among the films?*/

select count(*), name from film f
left join film_category fc
on fc.film_id = f.film_id
left join category c
on c.category_id = fc.category_id
group by name
order by count(*) desc;

/* 5. Task: Create an overview of the actors' first and last names and in how many movies they appear in.
Question: Which actor is part of most movies??*/

select first_name, last_name, count(*), a.actor_id from film f
left join film_actor fa
on f.film_id = fa.film_id
left join actor a
on a.actor_id = fa.actor_id
group by first_name, last_name, a.actor_id
order by count(*) desc;

/* 6.Task: Create an overview of the addresses that are not associated to any customer.
Question: How many addresses are that?*/

select count(*) from address add 
left join customer c
on add.address_id = c.address_id
where c.customer_id is null ;

/* 7. Task: Create the overview of the sales  to determine the from which city 
(we are interested in the city in which the customer lives, not where the store is) most sales occur.
Question: What city is that and how much is the amount?*/

SELECT city,SUM(amount) FROM payment p
LEFT JOIN customer c
ON p.customer_id=c.customer_id
LEFT JOIN address a
ON a.address_id=c.address_id
LEFT JOIN city ci
ON ci.city_id=a.city_id
GROUP BY city
ORDER BY SUM(amount) DESC;

/*8. Task: Create an overview of the revenue (sum of amount) grouped by a column in the format "country, city".
Question: Which country, city has the least sales?*/

select country || ',' || city as Location , sum(amount) as sales from payment p
left join customer c
on p.customer_id = c.customer_id
left join address ad
on ad.address_id = c.address_id
left join city ci
on ci.city_id = ad.city_id
left join country co
on co.country_id = ci.country_id
group by Location
order by sales asc;

/*9. Task: Create a list with the average of the sales amount each staff_id has per customer.
Question: Which staff_id makes on average more revenue per customer?*/

select staff_id, round(avg(total),2)  as avg_amount 

from (select staff_id, customer_id, sum(amount) as total from payment
group by staff_id, customer_id
order by 2) sub

group by staff_id;

/* 10. Task: Create a query that shows average daily revenue of all Sundays.
Question: What is the daily average revenue of all Sundays?*/

select round(avg(avg_revenue),2) from
(select date(payment_date), 
extract(dow from payment_date), round(avg(amount),2) as avg_revenue from payment
where extract(dow from payment_date) = 0
group by date(payment_date), extract(dow from payment_date)) as total
