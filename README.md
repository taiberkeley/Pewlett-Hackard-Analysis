# Analysis of employees getting ready to age out of the program 
  The Company has a large number of employees getting ready to age out of the program. The Manager has determined that anyone born between 1952 and 1955 will begin to retire. The first query will return a list of those employees that are eligible to retire, amongs the complete dataset of the company's employess. 

First, I have created a entity relationship diagram (ERD), a flowchart that highlights different tables and their relationships to each other.
I have also identified primary and foreign keys and and established relationships between tables, as per below: 
![alt text](https://github.com/taiberkeley/Pewlett-Hackard-Analysis/blob/main/QuickDBD-export.png)

Second step was to create a query using SQL/PgAdmin in order to create tables for each csv file with employees data the company have provided .
  Some of the queries are:
    
    CREATE TABLE Departments (
	dept_no VARCHAR(4) NOT NULL,
	dept_name VARCHAR(40) NOT NULL,
     PRIMARY KEY (dept_no),
     UNIQUE (dept_name)
	);

	CREATE TABLE dept_manager (
	dept_no VARCHAR(4) NOT NULL,
    emp_no INT NOT NULL,
    from_date DATE NOT NULL,
    to_date DATE NOT NULL,
	FOREIGN KEY (emp_no) REFERENCES employees (emp_no),
	FOREIGN KEY (dept_no) REFERENCES departments (dept_no),
    	PRIMARY KEY (emp_no, dept_no)
	);

With all the tables created I was able to import the csv files and identify the number of employess that were retiring, according to the range of date that was specified by the company:

	SELECT COUNT(first_name)
	FROM employees
	WHERE (birth_date BETWEEN '1952-01-01' AND '1955-12-31')
	AND (hire_date BETWEEN '1985-01-01' AND '1988-12-31');

With that query I was able to conclude that 41,380 employees borned and hired within the ranges will be retiering. Using the query below I was able to create a table and export a csv file for the manager with a complete list of employees that will age out of the program:

	SELECT first_name, last_name
	INTO retirement_info
	FROM employees
	WHERE (birth_date BETWEEN '1952-01-01' AND '1955-12-31')
	AND (hire_date BETWEEN '1985-01-01' AND '1988-12-31');

create a separate list of employees for each department. so we'll need to start combining it in different ways using joins.
The list Bobby provided was big and difficult to absorb as one large file. More than 40,000 employees? Retiring around the same time? Yeah, that's a lot.

Bobby will need to break down that large list into smaller pieces, then present those to his boss instead
What we'll need to help Bobby do first is recreate the retirement_info table so it includes the emp_no column. With this column in place, we'll be able to join our new table full of future retirees to the Dept_Emp table, so we know which departments will have job openings (and how many).

	-- Create new table for retiring employees
	SELECT emp_no, first_name, last_name
	INTO retirement_info
	FROM employees
	WHERE (birth_date BETWEEN '1952-01-01' AND '1955-12-31')
	AND (hire_date BETWEEN '1985-01-01' AND '1988-12-31');
	-- Check the table
	SELECT * FROM retirement_info;
	
After executing this code, the retirement_info table that's generated now includes the emp_no column. We are ready to begin combining different tables using joins to help Bobby create the new list his boss has requested.
Use Left Join to Capture retirement-info Tabl
Now that we have a list of all retirement-eligible employees, it's important to make sure that they are actually still employed with PH. To do so, we're going to perform another join, this time between the retirement_info and dept_emp tables.

	-- Joining retirement_info and dept_emp tables
	SELECT ri.emp_no,
    	ri.first_name,
    	ri.last_name,
	de.to_date
	INTO current_emp
	FROM retirement_info as ri
	LEFT JOIN dept_emp as de
	ON ri.emp_no = de.emp_no
	WHERE de.to_date = ('9999-01-01');

But before he's able to hand that list over, he'll need to use COUNT and GROUP BY with joins to separate the employees into their departments. 

	
	
