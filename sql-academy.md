


MySQL

# 1. Вывести имена всех людей, которые есть в базе данных авиакомпаний

``` 
SELECT Passenger.name
from Passenger; 
```

# 2. Вывести названия всеx авиакомпаний

```
select Company.name
from Company;
```

# 3. Вывести все рейсы, совершенные из Москвы

```
SELECT *
from trip
where town_from = 'Moscow';
```

# 4. Вывести имена людей, которые заканчиваются на "man"

```
select name
FROM passenger
where name LIKE '%man';
```

# 5. Вывести количество рейсов, совершенных на TU-134

```
SELECT count(trip.plane) as count
from trip
WHERE plane like 'TU-134';
```

# 6. Какие компании совершали перелеты на Boeing

```
select DISTINCT name
FROM Company
  INNER JOIN Trip on Company.id = Trip.company
where Trip.plane like 'Boeing';
```

# 7. Вывести все названия самолётов, на которых можно улететь в Москву (Moscow)

```
select DISTINCT trip.plane
from trip
where trip.town_from LIKE 'Moscow';
```

# 8. В какие города можно улететь из Парижа (Paris) и сколько времени это займёт?

```
select town_to,
  TIMEDIFF(time_in, time_out) as flight_time
FROM Trip
where town_from LIKE 'Paris';
```

# 9. Какие компании организуют перелеты с Владивостока (Vladivostok)?

```
select name
from Company
  inner join trip on Company.id = Trip.company
where Trip.town_to LIKE 'Vladivostok';
```

# 10. Вывести вылеты, совершенные с 10 ч. по 14 ч. 1 января 1900 г.

```
SELECT *
FROM Trip
where time_out BETWEEN '19000101100000' and '19000101140000';
```

# 11. Вывести пассажиров с самым длинным именем

```
SELECT name
FROM passenger
where LENGTH(name) = (
    SELECT MAX(LENGTH(name))
    from passenger
  );
  ```

# 12. Вывести id и количество пассажиров для всех прошедших полётов

```
SELECT trip,
  COUNT(Pass_in_trip.passenger) as count
from Pass_in_trip
group by trip;
```

# 13. Вывести имена людей, у которых есть полный тёзка среди пассажиров

```
SELECT passenger.name
FROM passenger
group by passenger.name
HAVING COUNT (NAME) > 1
```

# 14. В какие города летал Bruce Willis

```
select town_to
from trip
  inner join Pass_in_trip on trip.id = Pass_in_trip.trip
  inner JOIN Passenger ON Passenger.id = Pass_in_trip.passenger
where Passenger.name LIKE 'Bruce Willis';
```

# 15. Выведите дату и время прилёта пассажира Стив Мартин (Steve Martin) в Лондон (London)

```
SELECT trip.time_in
from trip
  inner join Pass_in_trip on trip.id = Pass_in_trip.trip
  inner join passenger on Pass_in_trip.passenger = Passenger.id
where passenger.name like 'Steve Martin'
  and town_to like 'London';
  ```
  
# 16. Вывести отсортированный по количеству перелетов (по убыванию) и имени (по возрастанию) список пассажиров, совершивших хотя бы 1 полет.

```
SELECT name,
  COUNT(*) as count
from Pass_in_trip
  inner join passenger on Pass_in_trip.passenger = passenger.id
group by name
having COUNT(trip) > 0
order by COUNT(trip) DESC,
  name
  ```
  
# 17. Определить, сколько потратил в 2005 году каждый из членов семьи

```
select member_name,
  status,
  sum(amount * unit_price) as costs
from FamilyMembers
  INNER JOIN Payments on FamilyMembers.member_id = Payments.family_member
WHERE YEAR(date) = 2005
GROUP by member_name,
  status;
  ```
  
# 18. Узнать, кто старше всех в семьe

```
select member_name
from FamilyMembers
where birthday = (
    SELECT MIN(birthday)
    FROM FamilyMembers
  )
  ```
  
# 19.  Определить, кто из членов семьи покупал картошку (potato)

1 version 

```
select DISTINCT status
from FamilyMembers
  inner join Payments on FamilyMembers.member_id = Payments.family_member
  inner join Goods on Payments.good = Goods.good_id
where Goods.good_name = 'potato';
```

2 version 

```
SELECT distinct FamilyMembers.status as status
from FamilyMembers
  inner JOIN Payments ON FamilyMembers.member_id = Payments.family_member
  INNER JOIN Goods on Payments.good = Goods.good_id
where good_name LIKE 'Potato';
```

# 20. Сколько и кто из семьи потратил на развлечения (entertainment). Вывести статус в семье, имя, сумму

```
select FamilyMembers.status,
  FamilyMembers.member_name,
  sum(Payments.amount * Payments.unit_price) as costs
from FamilyMembers
  INNER JOIN Payments ON FamilyMembers.member_id = Payments.family_member
  INNER JOIN Goods ON Payments.good = Goods.good_id
  INNER JOIN GoodTypes ON Goods.type = GoodTypes.good_type_id
where good_type_name = 'entertainment'
group by FamilyMembers.status,
  FamilyMembers.member_name;
  ```
  
# 21. Определить товары, которые покупали более 1 раза

```
SELECT good_name
from Goods
  INNER JOIN Payments on Goods.good_id = Payments.good
GROUP BY good
HAVING COUNT(good) > 1;
```

# 22. Найти имена всех матерей (mother)

```
SELECT FamilyMembers.member_name
from FamilyMembers
WHERE FamilyMembers.status LIKE 'mother';
```

# 23. Найдите самый дорогой деликатес (delicacies) и выведите его стоимость

```
SELECT g.good_name,
  p.unit_price
from Goods as g
  inner join Payments as p ON g.good_id = p.good
  inner join GoodTypes as gt ON g.type = gt.good_type_id
where p.unit_price = (
    SELECT MAX(p.unit_price)
    from Payments as p
      INNER JOIN Goods as g on g.good_id = p.good
      inner join GoodTypes as gt ON g.type = gt.good_type_id
    where gt.good_type_name = 'delicacies'
  )
  ```
  
# 24. Определить кто и сколько потратил в июне 2005

```
SELECT member_name,
  sum(amount * unit_price) as costs
FROM FamilyMembers
  INNER JOIN Payments on FamilyMembers.member_id = Payments.family_member
where MONTH(date) = 06
  AND YEAR(date) = 2005
group by member_name;
```

# 25. Определить, какие товары не покупались в 2005 году 

```
SELECT good_name
from Goods
where good_id not in (
    select good
    from Payments
    where YEAR(date) = 2005
  )
  ```
  
# 26.  Определить группы товаров, которые не приобретались в 2005 году

```SELECT good_type_name
from GoodTypes
WHERE good_type_id not in (
    select good_type_id
    from GoodTypes
      INNER JOIN Goods ON GoodTypes.good_type_id = Goods.type
      INNER JOIN Payments ON Goods.good_id = Payments.good
    WHERE YEAR(date) = 2005
  );
  ```
  
# 27. Узнать, сколько потрачено на каждую из групп товаров в 2005 году. Вывести название группы и сумму

```
SELECT good_type_name,
  SUM(amount * unit_price) as costs
from GoodTypes
  INNER join Goods on GoodTypes.good_type_id = Goods.type
  INNER JOIN Payments on Goods.good_id = Payments.good
where YEAR(date) = 2005
GROUP by good_type_name;
```

# 28. Сколько рейсов совершили авиакомпании с Ростова (Rostov) в Москву (Moscow) ?

```
select COUNT(*) AS count
FROM trip
where town_from = 'Rostov'
  and town_to = 'Moscow';
  ```
  
# 29. Выведите имена пассажиров улетевших в Москву (Moscow) на самолете TU-134 

```
SELECT DISTINCT name
from Passenger
  INNER JOIN Pass_in_trip on Passenger.id = Pass_in_trip.passenger
  INNER JOIN Trip on Pass_in_trip.trip = Trip.id
where town_to = 'Moscow'
  and plane = 'TU-134'
  ```
  
# 30. Выведите нагруженность (число пассажиров) каждого рейса (trip). Результат вывести в отсортированном виде по убыванию нагруженности.  

```
SELECT trip,
  COUNT(passenger) as count
from Trip
  INNER JOIN Pass_in_trip ON Trip.id = Pass_in_trip.trip
group by trip
ORDER BY count DESC
```

# 31. Вывести всех членов семьи с фамилией Quincey.

```
SELECT *
from FamilyMembers
where member_name like '% Quincey';
```

# 32. Вывести средний возраст людей (в годах), хранящихся в базе данных. Результат округлите до целого в меньшую сторону.

```
select FLOOR(AVG(YEAR(CURRENT_DATE) - YEAR(birthday))) as age
from FamilyMembers;
```

# 33. Найдите среднюю стоимость икры. В базе данных хранятся данные о покупках красной (red caviar) и черной икры (black caviar).

```
SELECT AVG(unit_price) AS cost
from Payments
WHERE good in (
    SELECT good_id
    from Goods
    where good_name LIKE '%caviar'
  );
  ```
  
# 34. Сколько всего 10-ых классов

```
select count(name) as count
from class
where name like '10%';
```

# 35. Сколько различных кабинетов школы использовались 2.09.2019 в образовательных целях ?

```
select count(classroom) as count
from Schedule
WHERE date = '2019-09-02'
```

# 36. Выведите информацию об обучающихся живущих на улице Пушкина (ul. Pushkina)?

```
select *
from Student
WHERE student.address LIKE 'ul. Pushkina%';
```

# 37. Сколько лет самому молодому обучающемуся ?

```
SELECT MIN(TIMESTAMPDIFF(YEAR, birthday, CURRENT_DATE)) as year
from Student;
```

# 38. Сколько Анн (Anna) учится в школе ?

```
SELECT COUNT(Student.first_name) as count
from Student
where student.first_name like 'Anna';
```

# 39. Сколько обучающихся в 10 B классе ?

```
SELECT count(Student_in_class.Student) as count
from Student_in_class
  INNER JOIN class on Student_in_class.class = Class.id
where Class.name like '10 B';
```

# 40. Выведите название предметов, которые преподает Ромашкин П.П. (Romashkin P.P.) ?

```
select name as subjects
from Subject
  inner join Schedule on Subject.id = Schedule.subject
  INNER JOIN Teacher on Schedule.teacher = Teacher.id
WHERE Teacher.last_name = 'Romashkin'
  and Teacher.middle_name LIKE 'Petrovich'
  AND Teacher.first_name LIKE 'Pavel'
  ```
  
# 41. Во сколько начинается 4-ый учебный предмет по расписанию ?

```
SELECT Timepair.start_pair
FROM Timepair
where id = 4;
```

# 42. Сколько времени обучающийся будет находиться в школе, учась со 2-го по 4-ый уч. предмет ?

```
SELECT DISTINCT TIMEDIFF(
    (
      SELECT end_pair
      FROM Timepair
      where id = 4
    ),
    (
      SELECT start_pair
      FROM Timepair
      WHERE id = 2
    )
  ) as time
from Timepair
```

# 43. Выведите фамилии преподавателей, которые ведут физическую культуру (Physical Culture). Отcортируйте преподавателей по фамилии.

```
SELECT last_name
from Teacher
  INNER JOIN Schedule on Teacher.id = Schedule.teacher
  INNER JOIN Subject on Schedule.subject = Subject.id
where Subject.name = 'Physical Culture'
order by last_name
```

# 44. Найдите максимальный возраст (колич. лет) среди обучающихся 10 классов ?

```
select MAX(TIMESTAMPDIFF(YEAR, birthday, CURRENT_DATE)) as max_year
from student
  join Student_in_class on Student.id = Student_in_class.student
  join Class on Student_in_class.class = Class.id
where Class.name like '10%'
```

# 45. Какой(ие) кабинет(ы) пользуются самым большим спросом?

```
SELECT classroom
from Schedule
group by classroom
HAVING count(classroom) = (
    SELECT COUNT(classroom)
    from Schedule
    GROUP by classroom
    order by classroom desc
    limit 1
  )
  ```
  
# 46. В каких классах введет занятия преподаватель "Krauze" ?

```
SELECT DISTINCT name
from Class
  INNER JOIN Schedule ON Class.id = Schedule.class
  INNER JOIN Teacher ON Schedule.teacher = Teacher.id
where Teacher.last_name = 'Krauze'
```

# 47. Сколько занятий провел Krauze 30 августа 2019 г.?

```
SELECT COUNT(teacher) as count
from Schedule
WHERE date = '2019-08-30'
  and teacher = (
    SELECT id
    FROM Teacher
    WHERE last_name = 'Krauze'
  )
  ```
  
#  48. Выведите заполненность классов в порядке убывания

```
select Class.name,
  COUNT(Student_in_class.student) as count
from Class
  INNER JOIN Student_in_class ON Class.id = Student_in_class.class
GROUP BY Class.id
ORDER BY count DESC
```

# 49. Какой процент обучающихся учится в 10 A классе ?

```
SELECT COUNT(student) * 100 / (
    SELECT COUNT(student)
    from Student_in_class
  ) as percent
from Student_in_class
  JOIN Class ON Student_in_class.class = Class.id
where name = '10 A';
```

# 50. Какой процент обучающихся родился в 2000 году? Результат округлить до целого в меньшую сторону.

```
SELECT FLOOR(
    COUNT(id) * 100 /(
      SELECT COUNT(id)
      from Student
    )
  ) as percent
from Student
where YEAR(birthday) = '2000'
```

# 59. Вывести пользователей,указавших Белорусский номер телефона ? Телефонный код Белоруссии +375.

```
select *
from Users
where phone_number like '+375%'
```

# 60. Выведите идентификаторы преподавателей, которые хотя бы один раз за всё время преподавали в каждом из одиннадцатых классов.

```
SELECT teacher
FROM Schedule
  join class on Schedule.class = Class.id
where Class.name like '11%'
group BY teacher
HAVING count (DISTINCT name) = 2
```

# 61. Выведите список комнат, которые были зарезервированы в течение 12 недели 2020 года.

```
select DISTINCT Rooms.*
from Rooms
  join Reservations on Rooms.id = Reservations.room_id
where week(start_date, 1) = 12
  and YEAR(start_date) = 2020
  ```
  
# 62. Вывести в порядке убывания популярности доменные имена 2-го уровня, используемые пользователями для электронной почты. Полученный результат необходимо дополнительно отсортировать по возрастанию названий доменных имён.

```
SELECT SUBSTRING_INDEX(email, '@', -1) as domain,
  COUNT(SUBSTRING_INDEX(email, '@', -1)) as count
from Users
group by domain
order by count desc,
  domain
  ```
  
# 63. Выведите отсортированный список (по возрастанию) имен студентов в виде Фамилия.И.О.

```
SELECT CONCAT(
    last_name,
    '.',
    LEFT(first_name, 1),
    '.',
    LEFT(middle_name, 1),
    '.'
  ) as name
from Student
order by last_name,
  first_name
  ```
  
# 64. Вывести количество бронирований по каждому месяцу каждого года, в которых было хотя бы 1 бронирование. Результат отсортируйте в порядке возрастания даты бронирования.

```
select YEAR(start_date) as year,
  MONTH(start_date) as month,
  COUNT(*) as amount
from Reservations
group by year,
  month
order by year ASC,
  month ASC
  ```
  
# 65. Необходимо вывести рейтинг для комнат, которые хоть раз арендовали, как среднее значение рейтинга отзывов округленное до целого вниз.
  
  ```
  SELECT room_id,
  FLOOR(AVG(rating)) as rating
from Reviews
  join Reservations on Reviews.reservation_id = Reservations.id
GROUP by room_id
```
