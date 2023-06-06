1. Посчитайте, сколько компаний закрылось.


```python
SELECT COUNT(id)
FROM company
WHERE status='closed'
```

2. Отобразите количество привлечённых средств для новостных компаний США. Используйте данные из таблицы company. Отсортируйте таблицу по убыванию значений в поле funding_total .


```python
SELECT funding_total
FROM company
WHERE category_code='news' AND country_code='USA'
ORDER BY funding_total DESC
```

3. Найдите общую сумму сделок по покупке одних компаний другими в долларах. Отберите сделки, которые осуществлялись только за наличные с 2011 по 2013 год включительно.


```python
SELECT SUM(price_amount)
FROM acquisition
WHERE term_code='cash' AND acquired_at BETWEEN '2011-01-01' AND '2013-12-31'
```

4. Отобразите имя, фамилию и названия аккаунтов людей в твиттере, у которых названия аккаунтов начинаются на 'Silver'.



```python
SELECT first_name, last_name, twitter_username
FROM people
WHERE twitter_username LIKE 'Silver%'
```

5. Выведите на экран всю информацию о людях, у которых названия аккаунтов в твиттере содержат подстроку 'money', а фамилия начинается на 'K'.


```python
SELECT *
FROM people
WHERE twitter_username LIKE '%money%' AND last_name LIKE 'K%'
```

6. Для каждой страны отобразите общую сумму привлечённых инвестиций, которые получили компании, зарегистрированные в этой стране. Страну, в которой зарегистрирована компания, можно определить по коду страны. Отсортируйте данные по убыванию суммы.


```python
SELECT country_code, SUM(funding_total)
FROM company
GROUP BY country_code
ORDER BY SUM(funding_total) DESC
```

7. Составьте таблицу, в которую войдёт дата проведения раунда, а также минимальное и максимальное значения суммы инвестиций, привлечённых в эту дату. Оставьте в итоговой таблице только те записи, в которых минимальное значение суммы инвестиций не равно нулю и не равно максимальному значению.


```python
SELECT funded_at, MIN(raised_amount), MAX(raised_amount)
FROM funding_round
GROUP BY funded_at
HAVING MIN(raised_amount)<>0 AND MIN(raised_amount)<>MAX(raised_amount)
```

8.

Создайте поле с категориями:
- Для фондов, которые инвестируют в 100 и более компаний, назначьте категорию high_activity.
- Для фондов, которые инвестируют в 20 и более компаний до 100, назначьте категорию middle_activity.
- Если количество инвестируемых компаний фонда не достигает 20, назначьте категорию low_activity.

Отобразите все поля таблицы fund и новое поле с категориями.


```python
SELECT *,
        CASE
          WHEN invested_companies >=100  THEN 'high_activity'
          WHEN invested_companies >=20 AND invested_companies <100  THEN 'middle_activity'
          WHEN invested_companies <20  THEN 'low_activity'
        END
FROM fund
```

9. Для каждой из категорий, назначенных в предыдущем задании, посчитайте округлённое до ближайшего целого числа среднее количество инвестиционных раундов, в которых фонд принимал участие. Выведите на экран категории и среднее число инвестиционных раундов. Отсортируйте таблицу по возрастанию среднего.


```python
SELECT 
       CASE
           WHEN invested_companies>=100 THEN 'high_activity'
           WHEN invested_companies>=20 THEN 'middle_activity'
           ELSE 'low_activity'
       END AS activity,
       ROUND(AVG(investment_rounds),0)
FROM fund
GROUP BY activity
ORDER BY ROUND(AVG(investment_rounds),0)
```

10.

Проанализируйте, в каких странах находятся фонды, которые чаще всего инвестируют в стартапы. 

Для каждой страны посчитайте минимальное, максимальное и среднее число компаний, в которые инвестировали фонды этой страны, основанные с 2010 по 2012 год включительно. Исключите страны с фондами, у которых минимальное число компаний, получивших инвестиции, равно нулю. 

Выгрузите десять самых активных стран-инвесторов: отсортируйте таблицу по среднему количеству компаний от большего к меньшему. 

Затем добавьте сортировку по коду страны в лексикографическом порядке.


```python
SELECT country_code,
        MIN(invested_companies) AS min,
        MAX(invested_companies) AS max,
        AVG(invested_companies) AS avg
FROM fund
WHERE founded_at BETWEEN '2010-01-01' AND '2012-12-31' 
GROUP BY country_code
HAVING MIN(invested_companies)>0
ORDER BY avg DESC,country_code
LIMIT 10
```

11. Отобразите имя и фамилию всех сотрудников стартапов. Добавьте поле с названием учебного заведения, которое окончил сотрудник, если эта информация известна.


```python
SELECT first_name, last_name, instituition
FROM people AS p
LEFT JOIN education AS e ON e.person_id=p.id
```

12.
Для каждой компании найдите количество учебных заведений, которые окончили её сотрудники. Выведите название компании и число уникальных названий учебных заведений. Составьте топ-5 компаний по количеству университетов.


```python
SELECT c.name, COUNT(DISTINCT e.instituition)
FROM company AS c
JOIN people AS p ON p.company_id=c.id
JOIN education AS e ON e.person_id=p.id
GROUP BY c.name
ORDER BY COUNT(DISTINCT e.id) DESC
LIMIT 5
```

13. 
Составьте список с уникальными названиями закрытых компаний, для которых первый раунд финансирования оказался последним.


```python
SELECT DISTINCT co.name
FROM company AS co
INNER JOIN funding_round AS fr ON fr.company_id=co.id
WHERE co.status ='closed'
AND fr.is_first_round=1
AND fr.is_last_round=1
```

14.
Составьте список уникальных номеров сотрудников, которые работают в компаниях, отобранных в предыдущем задании.


```python
SELECT p.id
FROM people AS p 
LEFT JOIN company AS c ON p.company_id=c.id
WHERE c.name IN (SELECT DISTINCT c.name FROM company AS c INNER JOIN funding_round AS fr ON fr.company_id=c.id WHERE c.status ='closed' AND fr.is_first_round=1 AND fr.is_last_round=1)
```

15.
Составьте таблицу, куда войдут уникальные пары с номерами сотрудников из предыдущей задачи и учебным заведением, которое окончил сотрудник.


```python
select distinct e.person_id, e.instituition
from education as e
where e.person_id in 
    (select distinct p.id
    from people as p
    where p.company_id in 
        (select distinct c.id from 
        company as c left join funding_round as f on c.id = f.company_id
        where c.status like 'closed'
        and is_first_round=1 and is_last_round=1))
```

16. Посчитайте количество учебных заведений для каждого сотрудника из предыдущего задания. При подсчёте учитывайте, что некоторые сотрудники могли окончить одно и то же заведение дважды.


```python
WITH
a AS (
SELECT e.person_id, 
                e.instituition
FROM education AS e
WHERE e.person_id in 
                   (SELECT DISTINCT p.id
                   FROM people AS p
                   WHERE p.company_id IN 
                                       (SELECT DISTINCT co.id
                                        FROM company AS co
                                        INNER JOIN funding_round AS fr ON fr.company_id = co.id
                                        WHERE co.status = 'closed'
                                        AND fr.is_first_round = 1
                                        AND fr.is_last_round =1))
)

SELECT DISTINCT person_id, COUNT(instituition)
FROM a
GROUP BY person_id
```

17.
Дополните предыдущий запрос и выведите среднее число учебных заведений (всех, не только уникальных), которые окончили сотрудники разных компаний. Нужно вывести только одну запись, группировка здесь не понадобится.


```python
WITH closed AS (
        SELECT distinct c.name, c.id
        FROM company AS c 
        JOIN funding_round AS fr ON c.id=fr.company_id
        WHERE fr.is_first_round =1
        AND fr.is_last_round=1
        AND c.status='closed'),

    pid AS (
        SELECT distinct id 
        FROM people 
        WHERE company_id IN (SELECT id FROM closed )),

    degree_cnt AS (
        SELECT  count(instituition)  , person_id 
        FROM education
        WHERE person_id IN (SELECT id FROM pid)
        
        GROUP BY person_id  )

SELECT AVG(avg_cnt.count)
FROM (SELECT count 
      FROM degree_cnt) AS avg_cnt

```

18.
Напишите похожий запрос: выведите среднее число учебных заведений (всех, не только уникальных), которые окончили сотрудники Facebook*.
*(сервис, запрещённый на территории РФ)


```python
WITH facebook AS (
        SELECT * 
       FROM company  
        WHERE name LIKE 'Facebook'),
        

    pid AS (
        SELECT distinct id 
        FROM people 
        WHERE company_id IN (SELECT id FROM facebook )),

    degree_cnt AS (
        SELECT count(instituition), person_id 
        FROM education
        WHERE person_id IN (SELECT id FROM pid)
        GROUP BY person_id)

SELECT AVG(avg_cnt.count)
FROM (SELECT count 
      FROM degree_cnt) AS avg_cnt 
```

19.

Составьте таблицу из полей:

- name_of_fund — название фонда;
- name_of_company — название компании;
- amount — сумма инвестиций, которую привлекла компания в раунде.

В таблицу войдут данные о компаниях, в истории которых было больше шести важных этапов, а раунды финансирования проходили с 2012 по 2013 год включительно.


```python
SELECT f.name AS name_of_fund,
        c.name AS name_of_company,
        fr.raised_amount AS amount
FROM investment AS i
JOIN company AS c ON c.id=i.company_id
JOIN fund AS f ON f.id=i.fund_id
JOIN funding_round AS fr ON fr.id=i.funding_round_id
WHERE c.milestones>6 AND EXTRACT (YEAR FROM fr.funded_at) BETWEEN 2012 AND 2013

```

20.

Выгрузите таблицу, в которой будут такие поля:

- название компании-покупателя;
- сумма сделки;
- название компании, которую купили;
- сумма инвестиций, вложенных в купленную компанию;
- доля, которая отображает, во сколько раз сумма покупки превысила сумму вложенных в компанию инвестиций, округлённая до ближайшего целого числа.

Не учитывайте те сделки, в которых сумма покупки равна нулю. Если сумма инвестиций в компанию равна нулю, исключите такую компанию из таблицы. 

Отсортируйте таблицу по сумме сделки от большей к меньшей, а затем по названию купленной компании в лексикографическом порядке. 
Ограничьте таблицу первыми десятью записями.


```python
SELECT DISTINCT c.name AS buyer,
        a.price_amount,
        com.name AS product,
        com.funding_total,
        ROUND(a.price_amount/com.funding_total)
FROM acquisition AS a
LEFT OUTER JOIN company AS c ON a.acquiring_company_id=c.id
LEFT OUTER JOIN company AS com ON a.acquired_company_id=com.id
LEFT OUTER JOIN funding_round AS fr ON com.id=fr.company_id
WHERE a.price_amount !=0 AND com.funding_total !=0
ORDER BY a.price_amount DESC, product
LIMIT 10
```

21.
Выгрузите таблицу, в которую войдут названия компаний из категории social, получившие финансирование с 2010 по 2013 год включительно. Проверьте, что сумма инвестиций не равна нулю. Выведите также номер месяца, в котором проходил раунд финансирования.


```python
with
tab as (SELECT *
FROM company
WHERE category_code='social')

SELECT tab.name as name,
       extract(month from fr.funded_at) as month 
FROM tab
LEFT JOIN funding_round as fr on tab.id=fr.company_id
WHERE extract(year from fr.funded_at) between 2010 and 2013 AND NOT fr.raised_amount =0
```

22.
Отберите данные по месяцам с 2010 по 2013 год, когда проходили инвестиционные раунды. Сгруппируйте данные по номеру месяца и получите таблицу, в которой будут поля:
- номер месяца, в котором проходили раунды;
- количество уникальных названий фондов из США, которые инвестировали в этом месяце;
- количество компаний, купленных за этот месяц;
- общая сумма сделок по покупкам в этом месяце.


```python
WITH a 
     AS (SELECT EXTRACT(MONTH FROM funded_at) AS mth, 
                COUNT(DISTINCT f.id) AS usa_funds_count 
         FROM   funding_round AS fr 
                JOIN investment i ON i.funding_round_id = fr.id 
                JOIN fund AS f ON f.id = i.fund_id 
         WHERE  f.country_code = 'USA' 
                AND EXTRACT(YEAR FROM funded_at) BETWEEN 2010 AND 2013 
         GROUP  BY mth), 
     b 
     AS (SELECT EXTRACT(MONTH FROM acquired_at) AS mth, 
                COUNT(acquired_company_id) AS acq_company_count, 
                SUM(price_amount) AS total 
         FROM   acquisition                 
         WHERE  EXTRACT(YEAR FROM acquired_at) BETWEEN 2010 AND 2013
         GROUP  BY mth) 
SELECT a.mth, 
       a.usa_funds_count, 
       b.acq_company_count, 
       b.total 
FROM   a JOIN b ON a.mth = b.mth;
```

23.
Составьте сводную таблицу и выведите среднюю сумму инвестиций для стран, в которых есть стартапы, зарегистрированные в 2011, 2012 и 2013 годах. Данные за каждый год должны быть в отдельном поле. Отсортируйте таблицу по среднему значению инвестиций за 2011 год от большего к меньшему.


```python
WITH
     inv_2011 AS (SELECT country_code,
				         AVG(funding_total) AS year_2011
				  FROM company
				  WHERE EXTRACT(YEAR FROM founded_at) = 2011
				  GROUP BY country_code),
	 inv_2012 AS (SELECT country_code,
				         AVG(funding_total) AS year_2012
				  FROM company
				  WHERE EXTRACT(YEAR FROM founded_at) = 2012
				  GROUP BY country_code),
	 inv_2013 AS (SELECT country_code,
				         AVG(funding_total) AS year_2013
				  FROM company
				  WHERE EXTRACT(YEAR FROM founded_at) = 2013
				  GROUP BY country_code)	
SELECT inv_2011.country_code,
       inv_2011.year_2011,
	   inv_2012.year_2012,
	   inv_2013.year_2013
FROM inv_2011 
INNER JOIN inv_2012 ON inv_2011.country_code = inv_2012.country_code
INNER JOIN inv_2013 ON inv_2012.country_code = inv_2013.country_code
ORDER BY inv_2011.year_2011 DESC;

```
