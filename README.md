## 6. JOIN

1. Show the matchid and player name for all goals scored by Germany (teamid = "GER").
```sql
SELECT matchid, player
FROM goal 
WHERE teamid = 'GER';
```
2. Show id, stadium, team1, team2 for game 1012.
```sql
SELECT id, stadium, team1, team2
FROM game
WHERE id = '1012';
```
3. Show the player, teamid, stadium and mdate for every German goal.
```sql
SELECT player, teamid, stadium, mdate
FROM game 
JOIN goal ON game.id = goal.matchid
WHERE teamid = 'GER';
```
4. Show the team1, team2 and player for every goal scored by player called Mario.
```sql
SELECT team1, team2, player
FROM game 
JOIN goal ON game.id = goal.matchid
WHERE player LIKE 'Mario%';
```
5. Show player, teamid, coach, gtime for all goals scored in the first 10 minutes (gtime <= 10).
```sql
SELECT player, teamid, coach, gtime
FROM goal 
JOIN eteam ON goal.teamid = eteam.id
WHERE gtime <= 10;
```
6. List the dates of the matches and the name of the team in which Fernando Santos was the team1 coach.
```sql
SELECT mdate, teamname
FROM game 
JOIN eteam ON game.team1 = eteam.id
WHERE coach = 'Fernando Santos';
```
7. List the player for every goal scored in a game where the stadium was "National Stadium, Warsaw".
```sql
SELECT player
FROM game 
JOIN goal ON game.id = goal.matchid
WHERE stadium = 'National Stadium, Warsaw';
```
8. Show the name of all players who scored a goal against Germany.
```sql
SELECT DISTINCT player
FROM goal
JOIN game ON goal.matchid = game.id
WHERE teamid <> 'GER' AND (team1 = 'GER' OR team2 = 'GER');
```
9. Show teamname and the total number of goals scored.
```sql
SELECT teamname, COUNT(*)
FROM goal
JOIN eteam ON goal.teamid = eteam.id
GROUP BY teamname;
```
10.  Show the stadium and the number of goals scored in each stadium.
```sql
SELECT stadium, COUNT(*)
FROM game
JOIN goal ON game.id = goal.matchid
GROUP BY stadium;
```
11.  For every match involving "POL", show the matchid, date and the number of goals scored.
```sql
SELECT matchid, mdate, COUNT(*)
FROM game
JOIN goal ON game.id = goal.matchid
WHERE team1 = 'POL' OR team2 = 'POL'
GROUP BY matchid, mdate;
```
12.  For every match where "GER" scored, show matchid, match date and the number of goals scored by "GER".
```sql
SELECT matchid, mdate, COUNT(*)
FROM game
JOIN goal ON game.id = goal.matchid
WHERE teamid = 'GER'
GROUP BY matchid, mdate;
```
13.  List every match with the goals scored by each team as shown.
```sql
SELECT mdate, team1, SUM(CASE WHEN teamid = team1 THEN 1 ELSE 0 END) AS score1, 
       team2, SUM(CASE WHEN teamid = team2 THEN 1 ELSE 0 END) AS score2
FROM game
LEFT JOIN goal ON game.id = goal.matchid
GROUP BY mdate, matchid, team1, team2;
```

## 7. More JOIN

1. List the films where the yr is 1962. Show id and title.
```sql
SELECT id, title
FROM movie
WHERE yr = '1962';
```
2. When was Citizen Kane released?
```sql
SELECT yr
FROM movie
WHERE title = 'Citizen Kane';
```
3. List all of the Star Trek movies, include the id, title and yr (all of these movies include the words Star Trek in the title). Order results by year.
```sql
SELECT id, title, yr
FROM movie
WHERE title LIKE '%Star Trek%'
ORDER BY yr;
```
4. What's the id for actor Glenn Close?
```sql
SELECT id
FROM actor
WHERE name = 'Glenn Close';
```
5. What's the id of the film Casablanca?
```sql
SELECT id
FROM movie
WHERE title = 'Casablanca';
```
6. Obtain the cast list for Casablanca.
```sql
SELECT name
FROM actor
JOIN casting ON actor.id = casting.actorid
WHERE movieid = (SELECT id 
                 FROM movie 
                 WHERE title = 'Casablanca');
```
7. Obtain the cast list for the film Alien.
```sql
SELECT name
FROM movie, actor, casting
WHERE title = 'Alien'
AND actor.id = casting.actorid
AND casting.movieid = movie.id;
```
8. List the films in which Harrison Ford has appeared.
```sql
SELECT title
FROM movie, actor, casting
WHERE name = 'Harrison Ford'
AND actor.id = casting.actorid
AND casting.movieid = movie.id;
```
9. List the films in which Harrison Ford has appeared but not in the starring role. (The ord field indicates the position of the actor. If ord = 1 then the actor is in the starring role.)
```sql
SELECT title
FROM movie, actor, casting
WHERE name = 'Harrison Ford'
AND actor.id = casting.actorid
AND casting.movieid = movie.id
AND ord <> '1';
```
10.  List the films together with the leading star for all 1962 films.
```sql
SELECT title, name
FROM movie, actor, casting
WHERE yr = '1962'
AND actor.id = casting.actorid
AND casting.movieid = movie.id
AND ord = 1;
```
11.  Which were the busiest years for Rock Hudson? Show the year and the number of movies he made each year for any year in which he made more than two movies.
```sql
SELECT yr, COUNT(title)
FROM movie
JOIN actor ON movie.id = actor.id
JOIN casting ON movie.id = casting.movieid
WHERE name = 'Rock Hudson'
GROUP BY yr
HAVING COUNT(title) > 2;
```
12.  List the film title and the leading actor for all of the films Julie Andrews played in.
```sql
SELECT title, name
FROM movie, actor, casting
WHERE movie.id IN (SELECT movie.id
                FROM movie, actor, casting 
                WHERE name = 'Julie Andrews'
                AND actor.id = casting.actorid
                AND casting.movieid = movie.id)
AND actor.id = casting.actorid
AND casting.movieid = movie.id
AND ord = 1;
```
13.  Obtain a list, in alphabetical order, of actors who've had at least 15 starring roles.
```sql
SELECT name
FROM actor
JOIN casting ON actor.id = casting.actorid
WHERE ord = 1
GROUP BY name
HAVING COUNT(name) >= 15;
```
14.  List the films released in the year 1978 ordered by the number of actors in the cast, then by title.
```sql
SELECT title, COUNT(actorid)
FROM movie, casting
WHERE yr = '1978'
AND casting.movieid = movie.id
GROUP BY title
ORDER BY 2 DESC, 1;
```
15.  List all the people who have worked with Art Garfunkel.
```sql
SELECT DISTINCT name
FROM actor, casting
WHERE actor.id = casting.actorid
AND name <> 'Art Garfunkel'
AND movieid IN (SELECT movieid
                FROM actor, casting
                WHERE name = 'Art Garfunkel'
                AND actor.id = casting.actorid);
```