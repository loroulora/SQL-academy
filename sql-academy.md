


MySQL

# 1. Вывести имена всех людей, которые есть в базе данных авиакомпаний

``` 
SELECT Passenger.name
from Passenger; 
```

# 2. Вывести названия всеx авиакомпаний

select Company.name
from Company;

# 3. Вывести все рейсы, совершенные из Москвы

SELECT *
from trip
where town_from = 'Moscow';

# 4. Вывести имена людей, которые заканчиваются на "man"

select name
FROM passenger
where name LIKE '%man';

# 5. Вывести количество рейсов, совершенных на TU-134

SELECT count(trip.plane) as count
from trip
WHERE plane like 'TU-134';

# 6. Какие компании совершали перелеты на Boeing

select DISTINCT name
FROM Company
  INNER JOIN Trip on Company.id = Trip.company
where Trip.plane like 'Boeing';

# 7. Вывести все названия самолётов, на которых можно улететь в Москву (Moscow)

select DISTINCT trip.plane
from trip
where trip.town_from LIKE 'Moscow';

# 8. В какие города можно улететь из Парижа (Paris) и сколько времени это займёт?

select town_to,
  TIMEDIFF(time_in, time_out) as flight_time
FROM Trip
where town_from LIKE 'Paris';

# 9. Какие компании организуют перелеты с Владивостока (Vladivostok)?

select name
from Company
  inner join trip on Company.id = Trip.company
where Trip.town_to LIKE 'Vladivostok';

# 10. Вывести вылеты, совершенные с 10 ч. по 14 ч. 1 января 1900 г.

SELECT *
FROM Trip
where time_out BETWEEN '19000101100000' and '19000101140000';

# 11. Вывести пассажиров с самым длинным именем

SELECT name
FROM passenger
where LENGTH(name) = (
    SELECT MAX(LENGTH(name))
    from passenger
  );

# 12. Вывести id и количество пассажиров для всех прошедших полётов

SELECT trip,
  COUNT(Pass_in_trip.passenger) as count
from Pass_in_trip
group by trip;

# 13. Вывести имена людей, у которых есть полный тёзка среди пассажиров

SELECT passenger.name
FROM passenger
group by passenger.name
HAVING COUNT (NAME) > 1
