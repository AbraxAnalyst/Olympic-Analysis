
# SQL OLYMPIC PROJECT 

This project was taken to apply my knowledge in SQL 
The dataset was gotten from Kaggle on Olympics games
The dataset(csv) was load into MySQL workbench using Table Import wizard 
DESCRIBE DATASET
1.	Name: The given name of each participant in the olympic game
2.	Sex: The gender of the participant
3.	Age: The age of the participant 
4.	Team: This is the team in which the participant belongs (Country)
5.	NOC: This is the Country Code of the participant 
6.	Games: The Olympics game name 
7.	Year: The year the game was played 
8.	Season: The season of the year the game was played 
9.	City: The city the game was played 
10.	Sport: The sport played 
11.	Event: The description of event of the game
12.	Medal: The medal class received by the participant
13.	Region: Country

This project is set to answer 15 questions in relation to Olympic Games
1.	Number of Olympic games held so far-
```bash
  SELECT COUNT (DISTINCT Games)
FROM athlete;
```
2.	All games held 
SELECT DISTINCT(Games), Year, City 
FROM athlete
ORDER BY Year;
3.	Number of nations participated in each Olympic game
```bash
  SELECT DISTINCT(Games) AS Game, COUNT (distinct NOC)
FROM athlete
GROUP BY Game
ORDER BY Game;
```

4.	What is the highest and lowest year of participating country

```bash
WITH country As 
	(SELECT a.Games, r.region
	 FROM athlete a
     JOIN region r 
     ON  a.NOC = r.NOC
     GROUP BY Games, r.region),
     total AS
     (SELECT Games, COUNT (*) AS total_country
     FROM country
     GROUP BY Games)
SELECT DISTINCT 
     CONCAT(first_value(Games) OVER (ORDER BY total_country)), "-", 
     first_value(total_country) OVER (ORDER BY total_country) AS LOW,
     CONCAT(first_value(Games) OVER (ORDER BY total_country DESC)), "-", 
     first_value(total_country) OVER (ORDER BY total_country DESC) AS HIGH
     FROM total
     ORDER BY 1;
```

5.	which country has participated in all Olympic games
```bash
WITH cm AS (SELECT r.region, COUNT(DISTINCT Games) AS count
     FROM athlete a
     jOIN region r ON r.NOC = a.NOC
     GROUP BY r.region),
     
     total_game AS 
      ( SELECT COUNT(DISTINCT Games) AS tot_games
        FROM athlete
      )
     SELECT region, count
     FROM  cm 
     JOIN total_game tg ON tg.tot_games = cm.count;
```

6.	 SPORT PLAYED IN ALL SUMMER 

```bash
SELECT DISTINCT(sport), season
FROM athlete
WHERE season = "Summer";
```

7.	 Sport played once in Olympic
```bash
WITH t1 as (
     SELECT DISTINCT Games, sport
     FROM athlete
     ),
     t2 as (
     SELECT sport, count(1) AS No_of_games
     FROM t1
     GROUP BY sport)
     
     SELECT t2.*, t1.Games
     from t2
     JOIN t1 on t1.sport = t2.sport
	where No_of_games = 1
     ORDER BY t1.sport;
```
  
8.	Total sport played in each Olympic games
```bash
WITH t1 AS (
    SELECT DISTINCT(Games), Sport
    FROM athlete)
    
    SELECT Games, COUNT(Sport)
    FROM t1
    GROUP BY Games
    ORDER BY COUNT(Sport) DESC;
```

9.	OLDEST athletes to win Gold medal
```bash
WITH details AS 
		( SELECT *
          FROM athlete
          WHERE Medal = "Gold"
          ),
          
		oldest AS 
          (SELECT Max(Age) AS Age
           FROM details
           )
		SELECT *
        FROM details
        JOIN oldest ON oldest.Age = details.Age;
```     

10.	 TOP 5 GOLD MEDAL HOLDER
```bash
WITH details AS 
		( SELECT Name, Team, COUNT(Medal) AS MD
          FROM athlete
          WHERE Medal = "Gold"
          GROUP BY Name, Team
          ORDER BY MD 
          )
		SELECT Name, Team,  MD
		  FROM details
          ORDER BY MD DESC
          LIMIT 5;
``` 
            
11.	 Athlete with most medal
```bash
WITH details AS 
		( SELECT Name, Team, COUNT(Medal) AS MD
          FROM athlete
          GROUP BY Name, Team
          ORDER BY MD 
          )
          SELECT Name, Team, MD 
          FROM details
          ORDER BY MD DESC
          LIMIT 5;
``` 
    

12.	Most successful country 
```bash
WITH details AS 
		( SELECT  distinct(Team), COUNT(Medal) AS MD
          FROM athlete
          GROUP BY Team
          ORDER BY MD 
          )
          SELECT Team, MD 
          FROM details
          ORDER BY MD DESC;
``` 
    
13.	 Most successful country BY MEDAL
```bash
SELECT  DISTINCT(Team),  Medal,
        COUNT(Medal) OVER(PARTITION BY Medal) AS MD
          FROM athlete
          GROUP BY Team, Medal
          ORDER BY Team, MD DESC;
``` 
		

