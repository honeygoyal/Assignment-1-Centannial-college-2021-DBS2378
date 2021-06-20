Assignment 1 Centannial college 2021 DBS2378 Database
This assignment will test your knowledge and skills acquired during the first three weeks
of this course including Database Designing and Implementation course review.
Assignment Requirements
For this assignment you must use SI schema
1. Print the total amount, the average dollar value of service visits (parts and labour
costs) and the number of those visits for Acura, Mercedes and Jaguar car
makes that are sold between September 2015 and December 2018 inclusive (5
marks).
2. SI schema contains customers that have bought one or more vehicles. They can
be classified using the following criteria:
a. Customers that have bought only one car (one-time buyer)
b. Customer that have bought two cars (two-time buyer)
c. Customers that have bought more than two cars (frequent buyer)
Using a SINGLE SELECT statement, display a list of customers with their names
and what type of buyer they are for all those customers that have bought
Mercedes car makes. (5 marks)
3. Using SET operations, display a list of customers that are interested in a car
(prospect table) of the same make and model which they already own. (5 marks)
4. Show a list of total amount of money spend on the labour cost of servicing
Toyota cars. Show the subtotals for each model (5 marks)
5. Write a query using analytic functions that will show the serial number, the price
of each JAGUAR car as well as the cumulative sale price totals. (5 marks)
Other Requirements
• This assignment is to be done individually; you are not allowed to work on this
assignment with anyone. See also the Academic Honesty Policy for more details.
Copied assignments will be subject to regulations against academic integrity.
• All assignments are subject to a late penalty of 10% per day (including
weekends).
• Late assignments will only be accepted up to 3 days after the due date (including
weekends).
Submission
Follow these instructions carefully or you risk penalties up to 100% of your grade.
• Your submission must follow all the submission requirements outlined in
the Coding Conventions.
• All assignments must be submitted in plain text format (.txt). No other types of
files will be accepted for submission. Make sure that when pasting the code onto
your text editor, the indentations and spaces are preserved.
• The scripts must be named YourNameDBAS32100Assignment1-n.sql
• Once you have finished your assignment and you are ready for submission, copy
and paste the entire code in a text file. Make sure that the indentations and
spaces are preserved, name the text
file YourNameDBAS32100Assignment1.txt
• Paste after each query the query results. If the query produces more than 8
rows paste only the first three and the last three rows and a row of dots (.....) in
between them.
• Upload the text file (.txt) in the Assignments drop box on SLATE.
Evaluation
Your submission will be evaluated based on the following criteria:
• Coding Conventions - (5 marks)
o Make sure that coding conventions for this course are followed
• SQL Code (25 marks)
o Individual marks for each statement are shown in the requirements part of
this assignment
• Other Penalties
o Submission instructions not followed (not in text format) (-100%)
o Submission requirements are not followed (-10%)
o The results of each query are not pasted after each query (-10%)
o Late submission (-10% per day up to 3 days, after that -100%)
Assignment 1 Total: 30
Assignment weight 5% of the final mark



# SqlQueries

Query-1:

SELECT CAST(SUM(PARTSCOST + LABORCOST+TAXRATE) AS DECIMAL(16,2)) AS TOTAL, 
CAST(AVG(PARTSCOST) AS DECIMAL(16, 2)) AS AVERAGE_PARTSCOST, 
CAST(AVG(LABORCOST) AS DECIMAL(16, 2)) AS AVERAGE_LABORCOST,
COUNT(*) AS NUM_VISITS, T.CARMAKE 
FROM SI.SERVINV S, 
(SELECT CARSERIAL, CARMAKE FROM SI.CAR WHERE CARMAKE = 'MERCEDES' OR CARMAKE = 'JAQUAR' OR CARMAKE = 'ACURA' ORDER BY CARMAKE) T,
(SELECT CARSERIAL FROM SI.SALEINV WHERE SALEDATE BETWEEN TO_DATE('01-09-2015', 'dd-mm-yyyy') AND TO_DATE ('31-12-2018', 'dd-mm-yyyy')) P
WHERE S.CARSERIAL = P.CARSERIAL
AND S.CARSERIAL = T.CARSERIAL
GROUP BY CARMAKE

-----------------------------------------------------------------------
TOTAL     AVERAGE_PARTSCOST	 AVERAGE_LABORCOST  NUM_VISITS	CARMAKE
-----------------------------------------------------------------------
28101.28	482.08				244.43				38		MERCEDES
30321.01	404.59				154.51				53		ACURA
49283		920.64				1037.68				25		JAGUAR
------------------------------------------------------------------------

Query-2:

SELECT CUST.CUSTNAME, 
         CASE WHEN COUNT(CUST.CUSTNAME) > 2 THEN 'FREQUENT BUYER' 
         WHEN COUNT(CUST.CUSTNAME) > 1 THEN 'TWO-TIME BUYER'
         ELSE 'ONE-TIME-BUYER' END AS TYPE_OF_BUYER
         FROM SI.CUSTOMER CUST, SI.CAR CAR WHERE CUST.CUSTNAME = CAR.CUSTNAME AND CAR.PURCHINVNO IS NOT NULL 
         AND CARMAKE = 'MERCEDES'
         GROUP BY CUST.CUSTNAME;

---------------------------------
CUSTNAME         TYPE_OF_BUYER
---------------------------------
DANIEL ROSALES	 ONE-TIME-BUYER
OMAR ALI	     ONE-TIME-BUYER
JAMES CUMMING	 ONE-TIME-BUYER
.					.
.					.
.					.
JACK BROWN	    ONE-TIME-BUYER
ROBERTFE	    ONE-TIME-BUYER
PHILIP PEDERSEN	ONE-TIME-BUYER
---------------------------------

Query-3:
SELECT T.CUSTNAME FROM 
(SELECT C.CUSTNAME, C.CARMAKE, C.CARMODEL FROM SI.CAR C INTERSECT
SELECT P.CUSTNAME, P.CARMAKE, P.CARMODEL FROM SI.PROSPECT P) T

-------------------
CUSTNAME
-------------------
AMANDEEP
BHAVAY GROVER
MATTHEW VILJAKAINEN
PATRICK SIMMONS
SMARTH ARORA
VALERIYA
-------------------

Query-4:

SELECT T.CARMODEL , SUM(LABORCOST) TOTAL_LABOR_COST
FROM SI.SERVINV S, (SELECT CARSERIAL, CARMODEL FROM SI.CAR WHERE CARMAKE = 'TOYOTA') T 
WHERE S.CARSERIAL = T.CARSERIAL 
GROUP BY T.CARMODEL

-------------------------------------
CARMODEL TOTAL_LABOR_COST
-------------------------------------
PRIUS		370
YARIS		250
CAMRY	    1760
.		 	.
.		 	.
.			.
COROLLA		1711.98
SEQUIOA		200
4RUNNER		94
--------------------------------------


Query-5:

SELECT C.CARSERIAL, C.CARLISTPRICE, 
(SELECT SUM(S.CARSALEPRICE) FROM SI.SALEINV S WHERE C.CARLISTPRICE <= S.CARSALEPRICE) COMMULATIVE_SALES_PRICE
FROM SI.CAR C
WHERE C.CARMAKE = 'JAGUAR'
ORDER BY C.CARSERIAL
 
---------------------------------------------------
CARSERIAL   CARLISTPRICE  COMMULATIVE_SALES_PRICE
---------------------------------------------------
A17OXEV1	24000		14908756.14
J00RXJ81	1200		15251115.02
J01BXJR8	19999		15078511.23
.			 .			.
.			 .			.
.			 .			. 
J95BXJ1 	93283.23	8513933.07
J95BXJ2 	93283.23	8513933.07
J97BJL0 	77350		10144489.57
-------------------------------------------------
