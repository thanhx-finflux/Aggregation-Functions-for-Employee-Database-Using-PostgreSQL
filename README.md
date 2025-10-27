# SQL-Aggregation-with-PostgreSQL
This project uses PostgreSQL to aggregate data from an employee database. The codebase leverages SQL aggregate functions (COUNT, SUM, MIN, MAX, AVG, ROUND) and grouping operations (GROUP BY, HAVING, DISTINCT) to extract meaningful insights for relational data. The project simulates a real-world employee database, analyzing employee, department, and salary data to answer business questions.

### Project Purpose
- Using SQL aggregate functions for data analysis.
- Demonstrate the use of GROUP BY and HAVING clause for filtering group data.
- Highlight ability to write efficient SQL queries to drive insights.
- Serve as a portfolio piece to demonstrate my expertise in database querying and PostgreSQL.

```sql
#############################
-- Introduction to the Employees Database
#############################

-- Retrieve all records in the employees table
SELECT * FROM employees;

-- Retrieve all records in the departments table
SELECT * FROM departments;

-- Retrieve all records in the dept_emp table
SELECT * FROM dept_emp;

-- Retrieve all records in the salaries table
SELECT * FROM salaries;

#############################
-- COUNT()
#############################
-- How many employees are in the company?
SELECT COUNT(emp_no) AS total_employees FROM employees;

-- Is there any employee without a first name?
SELECT COUNT(*) AS employees_without_first_name
FROM employees
WHERE
    first_name IS NULL;

-- Alternative Solution
SELECT COUNT(first_name) AS employees_with_first_name FROM employees;

-- How many records are in the salaries table
SELECT COUNT(*) AS total_salaries FROM salaries;

-- How many annual contracts with a value higher than or equal to
-- $100,000 has been registered in the salaries table?
SELECT COUNT(*) AS high_salary_contracts
FROM salaries
WHERE
    salary >= 100000;

-- How many times have we paid salaries to employees?
SELECT COUNT(salary) AS total_salary_payments FROM salaries;

-- This should give the same result as above
SELECT COUNT(from_date) FROM salaries;

#############################
-- SELECT DISTINCT & GROUP BY
#############################
-- Select first name from the employees table
SELECT first_name FROM employees;

-- Select different names from the employees table
SELECT DISTINCT first_name FROM employees;

-- Select first name from the employees table and group by first name
SELECT first_name FROM employees GROUP BY first_name;
-- This should give the same result as above

-- How many different names can be found in the employees table?
SELECT first_name, COUNT(*) AS name_count
FROM employees
GROUP BY
    first_name;

-- How many different names can be found in the employees table?
SELECT COUNT(DISTINCT first_name) AS different_first_names
FROM employees;

-- How many different first names are in the employees table?
SELECT COUNT(*) AS different_first_names
FROM (
        SELECT first_name
        FROM employees
        GROUP BY
            first_name
    ) AS subquery;
-- This should give the same result as above

-- 3.4: How many different first names can be found in the employees table
-- and order by first name in ascending order?
SELECT first_name, COUNT(*) AS name_count
FROM employees
GROUP BY
    first_name
ORDER BY first_name ASC;

-- 3.5: How many different first names are in the employees table
-- and order by first name in descending order?
SELECT first_name, COUNT(*) AS name_count
FROM employees
GROUP BY
    first_name
ORDER BY first_name DESC;

-- How many different departments are there in the "employees" database?
SELECT COUNT(DISTINCT dept_no) AS different_departments
FROM dept_emp;

-- How many employees are in each department? Rename the column as dept_count
SELECT dept_no, COUNT(*) AS dept_count
FROM dept_emp
GROUP BY
    dept_no
ORDER BY dept_no ASC;

-- Retrieve a list of how many employees earn over $80,000 and
-- how much they earn. Rename the 2nd column as emps_with_same_salary?
SELECT salary, COUNT(emp_no) AS emps_with_same_salary
FROM salaries
WHERE
    salary > 80000
GROUP BY
    salary
ORDER BY salary ASC;

#############################
-- HAVING
#############################
-- Retrieve a list of all employees who were employed on and after 1st of January, 1995
SELECT * FROM employees WHERE hire_date >= '1995-01-01';

-- Will this produces the same result?
SELECT *
FROM employees
GROUP BY
    emp_no
HAVING
    hire_date >= '1995-01-01';

-- Extract a list of names of employees, where the number of employees is more than 15
-- Order by first name.

SELECT first_name, COUNT(first_name) as names_count
FROM employees
WHERE
    COUNT(first_name) > 15 -- This will give an error
GROUP BY
    first_name
ORDER BY first_name;

-- Correct Solution
SELECT first_name, COUNT(first_name) as names_count
FROM employees
GROUP BY
    first_name
HAVING
    COUNT(first_name) > 15
ORDER BY first_name;

-- Retrieve a list of employee numbers and the average salary where the average salary is more than $120,000.
SELECT emp_no, AVG(salary) AS average_salary
FROM salaries
WHERE
    AVG(salary) > 120000 -- This will give an error
GROUP BY
    emp_no;

-- Select all records from the salaries table
SELECT * FROM salaries;

-- Solution
SELECT emp_no, ROUND(AVG(salary), 2) AS average_salary
FROM salaries
GROUP BY
    emp_no
HAVING
    AVG(salary) > 120000;

-- Extract a list of all names that have been encountered more than 10 times. Let the data refer to people hired after 1st of January, 1990
SELECT first_name, COUNT(first_name) AS name_count
FROM employees
WHERE
    hire_date > '1990-01-01'
GROUP BY
    first_name
HAVING
    COUNT(first_name) > 10
ORDER BY first_name;

-- Select the employee numbers of all individuals who have signed more than 1 contract after the 1st of January, 2000
-- Retrieve all records from dept_emp
SELECT * FROM dept_emp;

-- Solution
SELECT emp_no, COUNT(emp_no) AS contract_count
FROM dept_emp
WHERE
    from_date > '2000-01-01'
GROUP BY
    emp_no
HAVING
    COUNT(emp_no) > 1;

#############################
-- SUM
#############################
-- Retrieve the total amount the company has paid in salary?
SELECT SUM(salary) AS total_salaries_paid FROM salaries;

-- Retrieve the total amount the company has paid in salary?
SELECT SUM(*) -- This will give an error
FROM salaries;

-- What is the total amount of money spent on salaries for all contracts starting after the 1st of January, 1997?
SELECT SUM(salary) AS total_salaries_post_1997
FROM salaries
WHERE
    from_date > '1997-01-01';

#############################
-- Task Six: MIN() and MAX()
#############################
-- What is the highest salary paid by the company?
SELECT emp_no, MAX(salary) AS highest_salary
FROM salaries
GROUP BY
    emp_no
ORDER BY highest_salary DESC
LIMIT 1;

-- What is the lowest salary paid by the company?
SELECT emp_no, MIN(salary) AS lowest_salary
FROM salaries
GROUP BY
    emp_no
ORDER BY lowest_salary ASC
LIMIT 1;

-- What is the lowest employee number (ID) in the database?
SELECT MIN(emp_no) AS lowest_employee_number FROM employees;

-- What is the highest employee number (ID) in the database?
SELECT MAX(emp_no) AS highest_employee_number FROM employees;

#############################
-- AVG()
#############################
-- How much has the company paid on average to employees?
SELECT ROUND(AVG(salary), 2) AS average_salary FROM salaries;

-- What is the average annual salary paid to employees who started after the 1st of January, 1997
SELECT ROUND(AVG(salary), 2) AS average_salary_post_1997
FROM salaries
WHERE
    from_date > '1997-01-01';

#############################
-- Task Eight: ROUND()
#############################
-- Round the average salary to the nearest whole number
SELECT ROUND(AVG(salary), 0) AS average_salary_rounded FROM salaries;

-- Round the average salary to a precision of cents.
SELECT ROUND(AVG(salary), 2) AS average_salary_cents FROM salaries;

-- Round the average amount of money spent on salaries for all contracts that started after the 1st of January, 1997, to a precision of cents
SELECT
    ROUND(AVG(salary), 2) AS average_salary_post_1997_cents
FROM salaries
WHERE
    from_date > '1997-01-01';

-- Arithmetic operations can also be performed in PostgreSQL
-- Finding the range for salary
SELECT MAX(salary) - MIN(salary) AS salary_range FROM salaries;
-- Finding the difference between the highest and lowest average salary
SELECT MAX(AVG(salary)) - MIN(AVG(salary)) AS average_salary_range -- This will give an error
FROM salaries
GROUP BY
    emp_no;
```
### Contact

Thanh Xuyen, Nguyen

LinkedIn: [xuyen-thanh-nguyen-0518](https://www.linkedin.com/in/xuyen-thanh-nguyen-0518/)

Email: thanhxuyen.nguyen@outlook.com
