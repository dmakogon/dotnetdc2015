

### find actors that have also directed


match (m:Movie)<-[:ACTS_IN]-(a:Actor)-[:DIRECTED]->(d:Movie)
  return a.name as Actor, collect(distinct(m.title)) as ActedIn,collect(distinct(d.title)) as DirectedIn limit 2;

### same thing, returning relationships, for graphing
match (m:Movie)<-[act:ACTS_IN]-(a:Actor)-[dir:DIRECTED]->(d:Movie) 
  return a.name as Actor, act, collect(distinct(m.title)) as ActedIn,dir,collect(distinct(d.title)) as DirectedIn limit 2;
  
### same thing, for action movies
match (m:Movie {genre:"Action"})<-[:ACTS_IN]-(a:Actor)-[:DIRECTED]->(d:Movie)
  return a.name as Actor, collect(distinct(m.title)) as ActedIn,collect(distinct(d.title)) as DirectedIn limit 2;

### action movie actors, who have a biography
match (m:Movie {genre:"Action"})<-[:ACTS_IN]-(a:Actor)-[:DIRECTED]->(d:Movie) 
with distinct a
where length(a.biography) > 0
return a.name, a.biography  limit 20;

### top 5 movies with average rating above 4 stars
match (m:Movie)<-[r:RATED]-()
with m.title as title,  avg(r.stars) as averagestars
where averagestars > 4
return title,  averagestars
order by averagestars desc limit 5;

### same thing, but only with movies having multiple reviews
match (m:Movie)<-[r:RATED]-()
with m.title as title,  avg(r.stars) as averagestars, count(r) as reviewcount
where averagestars > 4 and reviewcount > 1
return title,  averagestars, reviewcount
order by averagestars desc limit 5;

### show how WITH works with ordering, especially with collections
match (u:User)
with u
ORDER by u.login DESC limit 3
return collect(u.login) 

### show directors for movie with most number of ratings
match (m:Movie)<-[r:RATED]-()
with m,count(r) as ratingcount
order by ratingcount desc limit 1
match (m)<-[:DIRECTED]-(d:Director)
return m.title, collect(d.name);

### show directors for lowest-rated movie
match (m:Movie)<-[r:RATED]-()
with m,avg(r.stars) as avgRating
order by avgRating  limit 1
match (m)<-[:DIRECTED]-(d:Director)
return m.title, avgRating, collect(d.name);

### shortest path
MATCH (martin:Actor { name:"Martin Sheen" }),(michael:Actor { name:"Michael Douglas" }),
p = shortestpath((martin)-[*..15]-(michael))
return p;