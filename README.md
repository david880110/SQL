use sakila;

### -- 1a. Display the first and last names of all actors from the table actor. 
select first_name, last_name from actor;
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/1a.png)

### -- 1b. Display the first and last name of each actor in a single column in upper case letters. Name the column Actor Name. 
select concat_ws(" ", first_name ,  last_name) as `Actor Name` from actor;
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/1b.png)

### -- 2a. You need to find the ID number, first name, and last name of an actor, of whom you know only the first name, "Joe." What is one query would you use to obtain this information?
select actor_id, first_name, last_name from actor 
where first_name = "joe";
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/2a.png)

### -- 2b. Find all actors whose last name contain the letters GEN:
select * from actor
where last_name like "%GEN%";
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/2b.png)

### -- 2c. Find all actors whose last names contain the letters LI. This time, order the rows by last name and first name, in that order:
select * from actor
where last_name like "%LI%"
order by last_name, first_name;
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/2c.png)

### -- 2d. Using IN, display the country_id and country columns of the following countries: Afghanistan, Bangladesh, and China:
select country_id, country from country
where country in ("Afghanistan", "Bangladesh", "China");
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/2d.png)

### -- 3a. Add a middle_name column to the table actor. Position it between first_name and last_name. Hint: you will need to specify the data type.
alter table actor
add column middle_name varchar(10) null after first_name;
select * from actor;

### -- 3b. You realize that some of these actors have tremendously long last names. Change the data type of the middle_name column to blobs.
alter table actor
change column middle_name middle_name blob;

### -- 3c. Now delete the middle_name column.
alter table actor
drop column middle_name;

### -- 4a. List the last names of actors, as well as how many actors have that last name.
select last_name, count(*) as last_name_count from actor
group by last_name;
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/4a.png)

### -- 4b. List last names of actors and the number of actors who have that last name, but only for names that are shared by at least two actors
select last_name, count(*) as last_name_count from actor
group by last_name
having last_name_count >= 2;
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/4b.png)

### -- 4c. Oh, no! The actor HARPO WILLIAMS was accidentally entered in the actor table as GROUCHO WILLIAMS, the name of Harpo's second cousin's husband's yoga teacher. Write a query to fix the record.
update actor
set first_name = "HARPO"
where last_name = "WILLIAMS" and first_name = "GROUCHO";
select last_name, first_name from actor
where first_name = "HARPO";

### -- 4d. Perhaps we were too hasty in changing GROUCHO to HARPO. It turns out that GROUCHO was the correct name after all! In a single query, if the first name of the actor is currently HARPO, change it to GROUCHO. Otherwise, change the first name to MUCHO GROUCHO, as that is exactly what the actor will be with the grievous error. BE CAREFUL NOT TO CHANGE THE FIRST NAME OF EVERY ACTOR TO MUCHO GROUCHO, HOWEVER! (Hint: update the record using a unique identifier.)
update actor
set first_name =
	if(first_name = "HARPO", "GROUCHO", "MUCHO GROUCHO")
where last_name = "WILLIAMS";
select * from actor
where last_name = "WILLIAMS";

### -- 5a. You cannot locate the schema of the address table. Which query would you use to re-create it? 
-- Hint: https://dev.mysql.com/doc/refman/5.7/en/show-create-table.html
show create table address;
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/5a.png)

### -- 6a. Use JOIN to display the first and last names, as well as the address, of each staff member. Use the tables staff and address:
select * from staff;
select * from address;

select s.first_name, s.last_name, a.address 
from staff s 
left join address a
on s.address_id = a.address_id;
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/6a.png)

### -- 6b. Use JOIN to display the total amount rung up by each staff member in August of 2005. Use tables staff and payment. 
select * from staff;
select * from payment;

select s.staff_id, s.first_name, s.last_name, p.amount, p.payment_date 
from staff s 
left join payment p 
on s.staff_id = p.staff_id
where payment_date between "2005-08-01" and "2005-08-31"
group by staff_id;
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/6b.png)

### -- 6c. List each film and the number of actors who are listed for that film. Use tables film_actor and film. Use inner join.
select * from film_actor;
select * from film;

select f.film_id, f.title, count(fa.actor_id) as actors_count
from film f
join film_actor fa 
on f.film_id = fa.film_id
group by film_id;
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/6c.png)

### -- 6d. How many copies of the film Hunchback Impossible exist in the inventory system?
select f.title, count(*) 
from film f
join inventory i
on f.film_id = i.film_id
where title = "HUNCHBACK IMPOSSIBLE";
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/6d.png)

### -- 6e. Using the tables payment and customer and the JOIN command, list the total paid by each customer. List the customers alphabetically by last name:
select c.customer_id, c.first_name, c.last_name, sum(p.amount) as total_paid
from customer c
join payment p
on c.customer_id = p.customer_id
group by c.customer_id
order by c.last_name;
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/6e.png)

### -- 7a. The music of Queen and Kris Kristofferson have seen an unlikely resurgence. As an unintended consequence, films starting with the letters K and Q have also soared in popularity. Use subqueries to display the titles of movies starting with the letters K and Q whose language is English. 
select f.title, l.name 
from film f
join language l
on f.language_id = l.language_id
where name = "English" and (title like "q&" or title like "k%");
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/7a.png)

### -- 7b. Use subqueries to display all actors who appear in the film Alone Trip.
select a.first_name, a.last_name
from actor a
join film_actor fa
on a.actor_id = fa.actor_id
join film f
on fa.film_id = f.film_id
where title = "Alone Trip";
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/7b.png)

### -- 7c. You want to run an email marketing campaign in Canada, for which you will need the names and email addresses of all Canadian customers. Use joins to retrieve this information.
select c.first_name, c.last_name, c.email, country.country
from customer c
join address a
on c.address_id = a.address_id
join city
on a.city_id = city.city_id
join country
on city.country_id = country.country_id
where country = "canada";
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/7c.png)

### -- 7d. Sales have been lagging among young families, and you wish to target all family movies for a promotion. Identify all movies categorized as famiy films.
select f.title, f.description, f.rating, c.name from film f
join film_category fc
on f.film_id = fc.film_id
join category  c
on fc.category_id = c.category_id
where fc.category_id = 8;
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/7d.png)

### -- 7e. Display the most frequently rented movies in descending order.
select f.title, count(r.inventory_id) as rental_count from film f
join inventory i
on f.film_id = i.film_id
join rental r
on i.inventory_id = r.inventory_id
group by f.film_id
having rental_count > 30
order by rental_count desc;
<br /><br />![alt text](https://github.com/david880110/SQL/blob/master/Result%20Screenshot/7e.png)

-- 7f. Write a query to display how much business, in dollars, each store brought in.
select * from sales_by_store;

-- 7g. Write a query to display for each store its store ID, city, and country.
select store_id, city, country
from store s
join address a
on s.address_id = a.address_id
join city 
on a.city_id = city.city_id
join country
on city.country_id = country.country_id;

-- 7h. List the top five genres in gross revenue in descending order. (Hint: you may need to use the following tables: category, film_category, inventory, payment, and rental.)
select * from sales_by_film_category
order by total_sales desc
limit 5;

-- 8a. In your new role as an executive, you would like to have an easy way of viewing the Top five genres by gross revenue. Use the solution from the problem above to create a view. If you haven't solved 7h, you can substitute another query to create a view.
drop view if exists top_five_genres;
create view top_five_genres (category, total_sales)
as
select * from sales_by_film_category
order by total_sales desc
limit 5;

-- 8b. How would you display the view that you created in 8a?
select * from top_five_genres;

-- 8c. You find that you no longer need the view top_five_genres. Write a query to delete it.
drop view top_five_genres;