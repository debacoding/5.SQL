Q1) ## type of triangle
Write a query identifying the type of each record in the TRIANGLES table using its three side lengths. Output one of the following statements for each record in the table:
Equilateral: It's a triangle with 3 sides of equal length.
Isosceles: It's a triangle with 2 sides of equal length.
Scalene: It's a triangle with 3 sides of differing lengths.
Not A Triangle: The given values of A, B, and C don't form a triangle.
```sql
SELECT IF(A + B <= C OR B + C <= A OR C + A <= B,"Not A Triangle",
         IF(A = B AND B = C, "Equilateral",
         IF(A = B OR B = C OR C = A,"Isosceles","Scalene")))
FROM TRIANGLES AS T;       
```
Q2) ## the PADS 
(a) Query an alphabetically ordered list of all names in OCCUPATIONS, immediately followed by the first letter of each profession as a parenthetical (i.e.: enclosed in parentheses). For example: AnActorName(A), ADoctorName(D), AProfessorName(P), and ASingerName(S).
(b) Query the number of ocurrences of each occupation in OCCUPATIONS. Sort the occurrences in ascending order, and output them in the following format:
There are a total of [occupation_count] [occupation]s. where [occupation_count] is the number of occurrences of an occupation in OCCUPATIONS and [occupation] is the lowercase occupation name. If more than one Occupation has the same [occupation_count], they should be ordered alphabetically.
```sql
select concat(name,'(',substring(Occupation,1,1),')') as Name 
from occupations 
order by Name;

Select concat ('There are a total of ', count(occupation),' ', lower(occupation),'s.') as totals
from occupations
group by occupation
order by totals;
```
Q3) ## occupations 
Pivot the Occupation column in OCCUPATIONS so that each Name is sorted alphabetically and displayed underneath its corresponding Occupation. The output column headers should be Doctor, Professor, Singer, and Actor, respectively.
Note: Print NULL when there are no more names corresponding to an occupation.
```sql
SELECT 
    D.Name, 
    P.Name, 
    S.Name, 
    A.Name 
FROM (
    SELECT 
        @rownum:=@rownum+1 AS rownum, 
        Name 
    FROM (SELECT @rownum:=0) r, 
        Occupations 
    WHERE Occupation = 'Doctor' 
    ORDER BY Name
) AS D 
RIGHT JOIN (
    SELECT 
        @rownumP:=@rownumP+1 AS rownumP, 
        Name 
    FROM (SELECT @rownumP:=0) r, Occupations 
    WHERE Occupation = 'Professor' ORDER BY Name
) AS P ON D.rownum = P.rownumP LEFT JOIN (SELECT @rownumS:=@rownumS+1 AS rownumS, Name FROM (SELECT @rownumS:=0) r, Occupations WHERE Occupation = 'Singer' ORDER BY Name) AS S ON P.rownumP = S.rownumS LEFT JOIN (SELECT @rownumA:=@rownumA+1 AS rownumA, Name FROM (SELECT @rownumA:=0) r, Occupations WHERE Occupation = 'Actor' ORDER BY Name) AS A ON P.rownumP = A.rownumA;
```
Q4) ## new companies
Given the table schemas below, write a query to print the company_code, founder name, total number of lead managers, total number of senior managers, total number of managers, and total number of employees. Order your output by ascending company_code.
The tables may contain duplicate records.
The company_code is string, so the sorting should not be numeric. For example, if the company_codes are C_1, C_2, and C_10, then the ascending company_codes will be C_1, C_10, and C_2.
```sql
select
    C.company_code, 
    group_concat( distinct C.founder),
    count(distinct LM.lead_manager_code),
    count(distinct SM.senior_manager_code),
    count(distinct M.manager_code),
    count(distinct E.employee_code)
from Company C
left join Lead_Manager LM on LM.company_code = C.company_code
left join Senior_Manager SM on SM.lead_manager_code = LM.lead_manager_code
left join Manager M on M.senior_manager_code = SM.senior_manager_code
left join Employee E on E.manager_code = M.manager_code
group by C.company_code
order by C.company_code;
```
Q5) ## binary tree nodes
Write a query to find the node type of Binary Tree ordered by the value of the node. Output one of the following for each node:
Root: If node is root node.
Leaf: If node is leaf node.
Inner: If node is neither root nor leaf node.
```sql
SELECT N, CASE
            WHEN P IS NULL THEN 'Root'
            WHEN (SELECT COUNT(*) FROM BST child WHERE child.P = father.N) > 0 THEN 'Inner'
            ELSE 'Leaf'
          END
FROM BST father
ORDER BY N;
```
