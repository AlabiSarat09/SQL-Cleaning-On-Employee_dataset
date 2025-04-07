# SQL-Cleaning-On-Employee_dataset

SELECT TOP (1000) [EmployeeID]
      ,[FirstName]
      ,[LastName]
      ,[Age]
      ,[Gender]
  FROM [SQLTutorial].[dbo].[EmployeeDemographics]

  -- To calculate the average_age by gender 
  SELECT Gender,AVG(Age) AS 'Avg Gender'
  FROM EmployeeDemographics
  GROUP BY Gender
  ORDER BY 'Avg Gender'

  SELECT * FROM EmployeeDemographics
  SELECT * FROM EmployeeSalary

  -- Joining Employee demographics with the salary 
  SELECT d.FirstName, d.Age, s.JobTitle, s.Salary FROM EmployeeDemographics AS d
  FULL OUTER JOIN EmployeeSalary AS s ON d.EmployeeID = s.EmployeeID
  ORDER BY FirstName


  -- Categorizing the Employees using CASE statement
SELECT FirstName, LastName, Age,
CASE
	WHEN Age > 30 THEN 'Old'
	WHEN Age BETWEEN 27 AND 30 THEN 'Young'
	ELSE 'Child'
END
FROM EmployeeDemographics
WHERE Age IS NOT NULL
ORDER BY Age


 --Categorising JobTitle and giving salary raise 
SELECT d.FirstName, d.LastName, s.JobTitle, s.Salary,
CASE
	WHEN JobTitle = 'Salesman' THEN
	Salary + (Salary * .10)
	WHEN JobTitle = 'Accountant' THEN
	Salary + (Salary * .05)
	ELSE Salary + (Salary * .03)
END AS SalaryAfterRaise
FROM EmployeeDemographics AS d
JOIN EmployeeSalary AS s
ON d.EmployeeID = s.EmployeeID

SELECT s.JobTitle, AVG(s.Salary)
FROM EmployeeDemographics AS d
JOIN EmployeeSalary AS s
ON d.EmployeeID = s.EmployeeID
GROUP BY s.JobTitle
HAVING AVG(s.Salary) > 45000
ORDER BY AVG(s.Salary)

SELECT FirstName + ' ' + LastName AS FullName
FROM EmployeeDemographics

SELECT * FROM EmployeeDemographics

SELECT demo.FirstName, demo.LastName,
demo.FirstName + ' ' + demo.LastName AS FullName,
demo.Gender, 
sal.Salary,
COUNT(demo.Gender)
OVER (PARTITION BY demo.Gender) AS TotalGender
FROM EmployeeDemographics AS demo
JOIN EmployeeSalary AS sal
ON demo.EmployeeID = sal.EmployeeID


WITH CTE_Employee AS (SELECT demo.FirstName, demo.LastName,demo.Gender, 
sal.Salary,COUNT(demo.Gender) OVER (PARTITION BY demo.Gender) AS TotalGender,
AVG(sal.Salary) OVER (PARTITION BY sal.Salary) AS AvgSalary
FROM EmployeeDemographics AS demo
JOIN EmployeeSalary AS sal
ON demo.EmployeeID = sal.EmployeeID
WHERE Salary > 45000)
SELECT FirstName, AvgSalary FROM CTE_Employee

CREATE TABLE #Temp_Salary(
EmployeeID int,
JobTitle varchar(100),
Salary int)
 

INSERT INTO #Temp_Salary
SELECT * FROM EmployeeSalary

DELETE FROM #Temp_Salary
WHERE EmployeeID = 2

SELECT demo.FirstName, demo.Gender, temp.EmployeeID, temp.Salary
FROM #Temp_Salary AS temp 
JOIN EmployeeDemographics AS demo
ON temp.EmployeeID = demo.EmployeeID
WHERE temp.Salary > 45000

DROP TABLE IF EXISTS #Temp_Salary2
CREATE TABLE #Temp_Salary2 (
JobTitle varchar(50),
EmployeePerJob int,
AvgAge int,
AvgSalary int)


INSERT INTO #Temp_Salary2
SELECT s.JobTitle,COUNT(s.JobTitle), AVG(s.Salary), AVG(d.Age)
FROM EmployeeDemographics AS d
JOIN EmployeeSalary AS s
ON d.EmployeeID = s.EmployeeID
GROUP BY s.JobTitle

SELECT * FROM #Temp_Salary2

CREATE TABLE EmployeeErrors(
EmployeeID varchar(50),
FirstName varchar(50),
LastName varchar(50)
)

INSERT INTO EmployeeErrors VALUES
('1001  ', 'Jimbo', 'Halbert'),
('  1002', 'Pamela', 'Beasley'),
('1005', 'TOby', 'Flenderson - Fired')

SELECT * FROM EmployeeErrors

 Using Trim, Ltrim, Rtrim
SELECT EmployeeID, TRIM(EmployeeID) AS IDTRIM
FROM EmployeeErrors

SELECT EmployeeID, LTRIM(EmployeeID) AS IDTRIM
FROM EmployeeErrors

SELECT EmployeeID, RTRIM(EmployeeID) AS IDTRIM
FROM EmployeeErrors

 Using Replace
SELECT LastName, REPLACE(LastName, '- Fired','') LastNameFixed
FROM EmployeeErrors

 Using Substring
SELECT FirstName, SUBSTRING(FirstName,1,3)
FROM EmployeeErrors

 SELECT SUBSTRING(err.FirstName,1,3) , SUBSTRING(demo.FirstName,1,3)
 FROM EmployeeErrors err
 JOIN EmployeeDemographics demo
 ON SUBSTRING(err.FirstName,1,3) = SUBSTRING(demo.FirstName,1,3)

  Using Upper and Lower

 SELECT FirstName, UPPER(FirstName)
 FROM EmployeeErrors

 SELECT FirstName, LOWER(FirstName)
 FROM EmployeeErrors
