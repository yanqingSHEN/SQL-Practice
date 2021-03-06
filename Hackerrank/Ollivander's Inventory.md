### Question Link: [Hackerrank/Ollivander's Inventory](https://www.hackerrank.com/challenges/harry-potter-and-wands/problem)


##  Solution 1
```sql
SELECT W2.id,Sub.age,m,Sub.power
FROM (
SELECT W1.power,min(coins_needed) as m,P1.age
FROM   Wands AS W1
INNER JOIN Wands_Property AS P1
ON     W1.code = P1.code
WHERE  P1.is_evil = 0
GROUP BY W1.power,P1.age) AS Sub
INNER JOIN Wands AS W2
ON     Sub.power = W2.power And Sub.m = W2.coins_needed
INNER JOIN Wands_Property AS P2
ON     Sub.age = P2.age
WHERE  W2.code = P2.code
ORDER BY Sub.power DESC, Sub.age DESC
```

##  Solution 2 (CTE)
```sql
WITH T1 AS(
SELECT      W2.code,W1.power,MIN(coins_needed) AS Minc
FROM        Wands AS W1
LEFT JOIN   Wands_Property AS W2
ON          W1.code = W2.code
WHERE       is_evil = 0
GROUP BY    W2.code,W1.power)
SELECT      W3.id,W4.age,T1.Minc,T1.power
FROM        T1
INNER JOIN  Wands AS W3
ON          T1.code = W3.code AND T1.power = W3.power AND T1.Minc = W3.coins_needed
INNER JOIN  Wands_Property AS W4
ON          T1.code = W4.code
ORDER BY    T1.power DESC,W4.age DESC
```

##  Solution 3 (Window)
```sql
WITH T1 AS (
SELECT id,age,coins_needed,power,
       ROW_NUMBER() OVER (PARTITION BY age,power ORDER BY coins_needed) as cheap
FROM   Wands
INNER JOIN Wands_Property
ON     Wands.code = Wands_Property.code
WHERE  is_evil = 0)
SELECT id,age,coins_needed,power
FROM   T1
WHERE  cheap = 1
ORDER BY power DESC,age DESC
```

## Logic
First find the minimum coins needed for each combination of wand age and power. Notice that for later join, use code instead of age in CTE
as these two variables have one-on-one relationship. Then find the corresponding id and age using join.
