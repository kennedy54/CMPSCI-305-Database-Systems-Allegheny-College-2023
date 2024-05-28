# Final Project

# Ryan Kennedy, Tugi Gantulga, David Gormley

## Twenty Research Queries

### Research Question 1

* Write the current research question here (you can copy and paste it from the `research_questions.md` document.)

How many parts are considered to be defective, and how many parts are considered to be not defective? How many defective parts were used for destructive testing, and how many not defective parts were used for destructive testing?

* Place all queries below for this particular question.

**Query 1:**
``` SQL
SELECT count(ColorCurrent) FROM NonConformance WHERE ColorCurrent="RED";
```
**Query 2:**
```SQL
SELECT count(ColorCurrent) FROM NonConformance WHERE ColorCurrent!="RED";
```
**Query 3:**
```SQL
SELECT distinct(count(JobOpID)) FROM NonConformance WHERE ColorCurrent="RED" AND DiscardedQtyToKeepDestructiveTest>0;
```
**Query 4:**
```SQL
SELECT distinct(count(JobOpID)) FROM NonConformance WHERE ColorCurrent="ORANGE" OR ColorCurrent="BLACK" OR ColorCurrent="YELLOW" AND DiscardedQtyToKeepDestructiveTest>0;
```

* In clear and meaningful language, explain what the queries are showing.

The queries first show how many parts are considered defective (red tags) (query 1), and how many are not (black, orange, or yellow tags) (query 2). They then show how many defective parts Acutec discarded, but kept for a destructive test (query 3), and how many not defective parts they used for a destructive test. 

* Please provide several lines of output from the queries.

**Query 1 Output:**
```SQL
15542
```
**Query 2 Output:**
```SQL
4878
```
**Query 3 Output:**
```SQL
2
```
**Query 4 Output:**
```SQL
1377
```

* Describe what we learn from this output.

We can learn that there were not many previously marked as "defective" parts that they still used for destructive testing. This can be explained because they already marked the rest of the defective parts to be defective, and not worth testing to begin with. However, there are also a high number of not previously marked as "defective" parts that were discarded and used for destructive testing. This appears to narrow down the parts that are most fit to be used without any error, as Acutec discarded these parts for a reason, as well as using those parts for a high number of tests. 

---

### Research Question 2

* Write the current research question here (you can copy and paste it from the `research_questions.md` document.)

How many JobIDs were the most and least efficient in the process of creating parts/products for aerospace vehicles? What particular JobID's were the most and least efficient?

* Place all queries below for this particular question.

**Query 1:**
```SQL
SELECT COUNT(DISTINCT JobID) AS NumericJobCount FROM Job WHERE COALESCE(CAST(Make_Quantity AS INTEGER), 0) > 0 AND COALESCE(CAST(Order_Quantity AS INTEGER), 0) > 0 AND COALESCE(CAST(Pick_Quantity AS INTEGER), 0) > 0 AND COALESCE(CAST(Shipped_Quantity AS INTEGER), 0) > 0 AND COALESCE(CAST(Returned_Quantity AS INTEGER), 0) > 0;
```
**Query 2:**
```SQL
SELECT COUNT(DISTINCT JobID) AS NumericZeroJobCount FROM Job WHERE COALESCE(CAST(Make_Quantity AS INTEGER), 0) = 0 AND COALESCE(CAST(Order_Quantity AS INTEGER), 0) = 0 AND COALESCE(CAST(Pick_Quantity AS INTEGER), 0) = 0 AND COALESCE(CAST(Shipped_Quantity AS INTEGER), 0) = 0 AND COALESCE(CAST(Returned_Quantity AS INTEGER), 0) = 0;
```
**Query 3:**
```SQL
SELECT JobID, MAX(COALESCE(CAST(Make_Quantity AS INTEGER), 0) + COALESCE(CAST(Order_Quantity AS INTEGER), 0) + COALESCE(CAST(Pick_Quantity AS INTEGER), 0) + COALESCE(CAST(Shipped_Quantity AS INTEGER), 0) + COALESCE(CAST(Returned_Quantity AS INTEGER), 0)) AS TotalQuantity FROM Job GROUP BY JobID ORDER BY TotalQuantity DESC LIMIT 5;
```
**Query 4:**
```SQL
SELECT JobID, MIN(COALESCE(CAST(Make_Quantity AS INTEGER), 0) + COALESCE(CAST(Order_Quantity AS INTEGER), 0) + COALESCE(CAST(Pick_Quantity AS INTEGER), 0) + COALESCE(CAST(Shipped_Quantity AS INTEGER), 0) + COALESCE(CAST(Returned_Quantity AS INTEGER), 0)) AS TotalQuantity FROM Job GROUP BY JobID ORDER BY TotalQuantity ASC LIMIT 5;
```

* In clear and meaningful language, explain what the queries are showing.

Query 1 shows which JobIDs contain numeric values above zero in all of the following attributes: Make_Quantity, Order_Quantity, Pick_Quantity, Shipped_Quantity, and Returned_Quantity. This shows how many JobIDs were efficient in the sense that they all contributed every one of these attributes in a positive manner. Query 2 displays which JobIDs contain all numeric values of zero in the Make_Quantity, Order_Quantity, Pick_Quantity, Shipped_Quantity, and Returned_Quantity attributes. This shows how many JobIDs were inefficient in the sense that they all did not contribute at all to any of these attributes. Query 3 displays which JobIDs contained the highest numeric value in any of the previously mentioned attributes, and it also displays the numeric values themselves. This shows how these JobID could be the most efficient in terms of highest numeric values, value rather how much they contributed to all the attributes together. Query 4 displays which JobIDs conatined the lowest numeric values in any of the previously mentioned attributes, and it also displays the numeric values themselves. This shows how these JobID could be least efficient in terms of having lowest numeric, value rather how much they contributed to all the attributes together.

* Please provide several lines of output from the queries.

**Results for Query 1:**
```SQL
821
```
**Results for Query 2:**
```SQL
216
```
**Results for Query 3:**
```SQL
a54fcd7a-272c-4a46-8c98-6f29b56931ec|1976828728
```
**Results for Query 4:**
```SQL
3010164d-1ee0-4795-9f28-25dee6ba79c1|-2097151924
```

* Describe what we learn from this output.

We can learn that there are significantly more JobIDs that contribute to all of the attributes: Make_Quantity, Order_Quantity, Pick_Quantity, Shipped_Quantity, and Returned_Quantity, than JobIDs that did not contribute to any of these attributes at all. This implies that there are more efficient JobID's than there are inefficient ones. We can also learn that some JobIDs specialize in different attributes more than others in terms of efficiency, and others perform poorly in some attributes more than others. A JobID can have a very high or very low numeric value in a particular attribute, indicating how efficient it is in that specific attribute. However, this does not indicate that this JobID is the most efficient it can possibly be on a global scale, as it may not have contributed to anything else.

---



### Research Question 3

* Write the current research question here (you can copy and paste it from the `research_questions.md` document.)

Are there any trends in the "Make_Quantity," "Order_Quantity," and "Shipped_Quantity" over time based on the "Sched_Start" and "Sched_End" dates in the "job" table?

* Place all queries below for this particular question.

``` SQL
SELECT 
  strftime('%Y', Sched_Start) AS Year, 
  SUM(Make_Quantity) AS Total_Make_Quantity, 
  SUM(Order_Quantity) AS Total_Order_Quantity, 
  SUM(Shipped_Quantity) AS Total_Shipped_Quantity 
FROM job 
GROUP BY Year;

```

``` SQL

SELECT JobID, Make_Quantity 
FROM job 
ORDER BY Make_Quantity DESC 
LIMIT 10;

```

``` SQL

SELECT JobID, ABS(Order_Quantity - Shipped_Quantity) AS Discrepancy 
FROM job 
ORDER BY Discrepancy DESC 
LIMIT 10;


```

``` SQL
SELECT AVG(julianday(Sched_End) - julianday(Sched_Start)) AS Avg_Duration 
FROM job 
WHERE Sched_End IS NOT NULL AND Sched_Start IS NOT NULL;

```

* In clear and meaningful language, explain what the queries are showing.

The data shows how production and order fulfillment at the company have changed over the years. There were ups and downs in the numbers of products made, orders received, and products shipped. In 2015 and 2016, something unusual happened, which might point to errors in the data or some special events during those years.

We also have a list that shows which jobs had the highest production. This tells us where most of our efforts were concentrated. Another list reveals jobs where there was a big difference between how many orders we got and how many products we actually sent out. This indicates some inconsistencies in how we operate.

Finally, on average, it takes about 197 days to complete a job. This gives us an idea of how long it takes to get a product from start to finish, which helps us understand the efficiency of our production process.

* Please provide several lines of output from the queries.

``` TEXT
YEAR    Quantity Order  Shipped
2007	1608515	2171379	1295561
2008	1650569	2384131	1165073
2009	1161831	1624972	749730
2010	1351696	1879831	931773
2011	1457475	2010870	994460
2012	1483551	2045226	1058239
2013	1554475	2266622	1067334
2014	1317131	2029623	983374
2015	1457167	2394596	22672967
2016	1410592	1975111	-47705983
2017	11558545	12155923	970418
2018	1752787	2483148	1106734
2019	1683031	2455604	1191989
2020	1490380	2116606	977105

```

``` TEXT
JobID	                                Make_Quantity
25175038-e434-410c-aa9b-4b53524e0f2f	10000000
ea69f9d2-84b7-4282-a39f-c6c9864a0c08	10000000
572520b4-0ffb-4bbd-8b11-f01174aa5b68	50000
c2034d81-f537-408f-a7ee-5e0d46a08c7d	30000
80e48595-b19b-47e7-b061-ae63eb1d0d65	28600
fea3e0be-0c36-4bf3-b3c1-0e5802b65eef	26000
36c08732-4f25-4579-a665-6630b3ea24a9	25000
72290614-a57c-4ab4-a863-fa26c8ce9f33	19537
3586a16a-a98a-46f3-9115-96aa76c683a5	17675
```


``` TEXT

3010164d-1ee0-4795-9f28-25dee6ba79c1	2097152000
a54fcd7a-272c-4a46-8c98-6f29b56931ec	1976827504
3b36c2ec-e440-431f-bb69-283ee0db288d	15138496
ba4b5476-47c6-4030-9f04-69c8dba2b3c8	14811126
4b06fdf5-d8d5-4bcd-b4d8-074e4fcdabed	14286848
8f46b0b2-f4cd-4b5a-941a-b192f1c4a4a0	14286828
7947db58-95c7-4a5d-8c7f-bda99d9b0b48	13238234
25175038-e434-410c-aa9b-4b53524e0f2f	10000000
ea69f9d2-84b7-4282-a39f-c6c9864a0c08	10000000
4fda3bc0-65ea-4032-8370-d05b4d7b5509	9699273
```


``` TEXT
avg_duration
197.119742685511
```

* Describe what we learn from this output.

There were notable irregularities or unexpected changes in the data from 2015 to 2016. This suggests that either there were errors in how the data was recorded, or the company experienced sudden spikes in orders or production challenges. The comparison of ordered and shipped quantities reveals inconsistencies. On average, 197 days to complete a job gives insight into the overall efficiency and speed of the company's production process.

---


### Research Question 4

* Write the current research question here (you can copy and paste it from the `research_questions.md` document.)

How can the data from these tables be used in a warehouse or for business intelligence purposes?

* Place all queries below for this particular question.




``` SQL
SELECT SUM(Make_Quantity) - SUM(Shipped_Quantity) AS Current_Inventory
FROM job;

```

```SQL
SELECT JobID, Order_Quantity
FROM job
ORDER BY Order_Quantity DESC
LIMIT 10;
```

``` SQL
SELECT strftime('%Y-%m', Sched_Start) AS Month, SUM(Shipped_Quantity) AS Monthly_Shipment
FROM job
GROUP BY Month;

```

``` SQL
SELECT JobID, Sched_End, (CASE WHEN Shipped_Quantity = Order_Quantity THEN 'On Time' ELSE 'Delayed' END) AS Delivery_Status
FROM job
WHERE Sched_End IS NOT NULL;

```

* In clear and meaningful language, explain what the queries are showing.

The first query can be used in a warehouse and for business intelligence by calcualting the current investory, assuming that the difference is all going to plan. By calcualtin the difference between total expected quantity and total shipped quantity.

The second query is used in a way to identify the top high-demand product. For example, if something has the most order quantity, then it is bound to be the product with the highest demand and businesses can store that product more in their warehouse.

The third query analyses the monthly shipment quantities. I think in my opinion, it is very useful for understanding seasonal trends and planning future shipments. It is showing monthly shipment past 2001 until the date they planned, which was a placeholder date around 2099.

The fourth query categorizes each job as 'On Time' or 'Delayed' based on whether the shipped quantity matches the order quantity, and then counts the total number of jobs in each category. Such a breakdown is useful for identifying areas needing improvement in the production and delivery process.


* Please provide several lines of output from the queries.

```TEXT
current_inventory
56446113.0
```

```TEXT
JobID	                               Order_Quantity
25175038-e434-410c-aa9b-4b53524e0f2f	10000000
ea69f9d2-84b7-4282-a39f-c6c9864a0c08	10000000
eb834d5f-3441-4be7-b5a5-a1f09a85cd88	55000
572520b4-0ffb-4bbd-8b11-f01174aa5b68	50000
7cf00ab6-60f8-412b-b16f-a9ab7e907de1	45000
16f228e5-3b44-48c9-85c2-05127fc766a8	44037
892b0239-3af9-4116-843d-7389d0f48009	35000
f3111545-65e8-460a-9155-ed09e046edaa	30004
c2034d81-f537-408f-a7ee-5e0d46a08c7d	30000
```

```TEXT
Month   Quantity_shipped
2000-11	21244
2000-12	119055
2001-01	100425
2001-02	97408
2001-03	86929
2001-04	79613
2001-05	70220
2001-06	66618
2001-07	51135
2001-08	71057
2001-09	47474
2001-10	54264
2001-11	47118
2001-12	56816
2002-01	59973
2002-02	60655
2002-03	48423
2002-04	47532
2002-05	74181
2002-06	53743
2002-07	52909
2002-08	62065
2002-09	46923
2002-10	58381
2002-11	43371
2002-12	37222
2003-01	67994
```

```TEXT
Delayed	167114
On Time	151311
```

* Describe what we learn from this output.

Using these four queries, we know how much stock is available in the warehouse, most ordered items, guiding inventory stocking and production prioritization. We also got to know monthly/seasonal patterns or trends in shipments, useful for planning and resource allocation. Lastly, the jobs into 'On Time' and 'Delayed' categories.

---


### Research Question 5

* Write the current research question here (you can copy and paste it from the `research_questions.md` document.)

What impact does query design have on response time? if any?

* Place all queries below for this particular question.

``` SQL
SELECT 
    job.JobID,
    (SUM(job.Returned_Quantity) * 100.0 / SUM(job.Shipped_Quantity))
FROM job;
```

~225ms

``` SQL
SELECT 
    job.JobID,
    (SUM(CASE WHEN job.Returned_Quantity IS NOT NULL THEN job.Returned_Quantity ELSE 0 END) * 100.0 /
    SUM(CASE WHEN job.Shipped_Quantity IS NOT NULL THEN job.Shipped_Quantity ELSE 0 END))
FROM job;
```

~270ms

``` SQL
SELECT 
    JobID,
    Make_Quantity + Shipped_Quantity
FROM job;
```

~35ms

``` SQL
SELECT 
    j1.JobID,
    j1.Make_Quantity + j2.Shipped_Quantity
FROM job j1
JOIN job j2 ON j1.JobID = j2.JobID
```

~11000ms

* In clear and meaningful language, explain what the queries are showing.

For the sake of collecting data on query design, the first and second queries both return the total percentage of returned items that have been shipped. The first one uses sum functions to accomplish this. The second query used CASE statements within the SUM functions to conditionally sum up the quantity assuming that they were not NULL. The third and fourth queries also do the same thing in that they add the shipped quanitity to the make quantity which on it's own is not very usefull but for information on response times, it is great. The third query uses a simple addition operator to accomplish this while the fourth query references the job table twice as j1 and j2 which needlesly makes it more complex.

* Please provide several lines of output from the queries.

``` TEXT
Execution finished without errors.
Result: 1 rows returned in 228ms
```

``` TEXT
Execution finished without errors.
Result: 1 rows returned in 278ms
```

``` TEXT
Execution finished without errors.
Result: 318425 rows returned in 31ms
```

``` TEXT
Execution finished without errors.
Result: 318425 rows returned in 11352ms
```

* Describe what we learn from this output.

From these outputs, we learn more about query executuion time in relation to query design. The first and second queries had fairly similar response times although the first was slightly faster on average. The first query had an average 225ms response time and was using only the SUM function. The second query used conditional CASE statements and had an average 270 ms response time. The third and fourth queries however is where the difference truly shows. The third query uses a simple addition operator and has a 35ms response time on average. The fouth query which was purposely made complex had an average 11000ms response time. This data shows that query design can greatly affect the execution time and efficiency of people using the database.


(Did you remember to place your name at the top of this document?)
