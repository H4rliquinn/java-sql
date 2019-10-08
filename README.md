# java-sql

A student that completes this project shows that they can:

- Query data from a single table
- Query data from multiple tables
- Create a new datadaase using PostgreSQL

# Introduction

Working with SQL

# Instructions

ReImport the Northwind database into PostgreSQL using pgAdmin. This is the same data we used during the guided project.

clone https://github.com/pthom/northwind_psql.git

## pgAdmin

- Right Click Databases

  - Create
    - type in northwind2

- Tools -> Query Tool

  - Open file northwind.sql (from cloned repo)
  - Execute

- Look under

  - northwind2 -> Schemas -> public -> tables

- Clear query windows

Answer the following data queries. Keep track of the SQL you write by pasting it into this document under its appropriate header below. You will be submitting that through the regular fork, change, pull process.

### find all customers that live in London. Returns 6 records.

> This can be done with SELECT and WHERE clauses

SELECT \*
FROM customers
WHERE city='London'

### find all customers with postal code 1010. Returns 3 customers.

> This can be done with SELECT and WHERE clauses

SELECT \*
FROM customers
WHERE postal_code='1010'

### find the phone number for the supplier with the id 11. Should be (010) 9984510.

> This can be done with SELECT and WHERE clauses

SELECT phone
FROM suppliers
WHERE supplier_id='11'

### list orders descending by the order date. The order with date 1998-05-06 should be at the top.

> This can be done with SELECT, WHERE, and ORDER BY clauses

SELECT \*
FROM orders
ORDER BY order_date desc

### find all suppliers who have names longer than 20 characters. You can use `length(company_name)` to get the length of the name. Returns 11 records.

> This can be done with SELECT and WHERE clauses

SELECT \*
FROM suppliers
WHERE length(company_name)>20

### find all customers that include the word 'MARKET' in the contact title. Should return 19 records.

> This can be done with SELECT and a WHERE clause using the LIKE keyword

> Don't forget the wildcard '%' symbols at the beginning and end of your substring to denote it can appear anywhere in the string in question

> Remember to convert your contact title to all upper case for case insenstive comparing so upper(contact_title)

SELECT \*
FROM customers
WHERE contact_title like '%Market%'

### add a customer record for

- customer id is 'SHIRE'
- company name is 'The Shire'
- contact name is 'Bilbo Baggins'
- the address is '1 Hobbit-Hole'
- ths city is 'Bag End'
- the postal code is '111'
- the country is 'Middle Earth'
  > This can be done with the INSERT INTO clause

INSERT INTO customers (customer_id,company_name,contact_name,address,city,postal_code,country)
VALUES ('SHIRE','The Shire','Bilbo Baggins','1 Hobbit-Hole','Bag End','111','Middle Earth')

### update _Bilbo Baggins_ record so that the postal code changes to _"11122"_.

> This can be done with UPDATE and WHERE clauses

UPDATE customers
SET postal_code='11122'  
WHERE customer_id='SHIRE'

### list orders grouped by customer showing the number of orders per customer. _Rattlesnake Canyon Grocery_ should have 18 orders.

> This can be done with SELECT, COUNT, JOIN and GROUP BY clauses. Your count should focus on a field in the Orders table, not the Customer table

> There is more information about the COUNT clause on [W3 Schools](https://www.w3schools.com/sql/sql_count_avg_sum.asp)

SELECT c.customer_id, c.company_name, o.order_count
FROM customers c
JOIN (SELECT customer_id, count(customer_id) as order_count from orders GROUP BY customer_id) o
ON c.customer_id=o.customer_id
-- WHERE company_name like 'Rattlesnake%'

### list customers names and the number of orders per customer. Sort the list by number of orders in descending order. _Save-a-lot Markets should be at the top with 31 orders followed by \_Ernst Handle_ with 30 orders. Last should be _Centro comercial Moctezuma_ with 1 order.

> This can be done by adding an ORDER BY clause to the previous answer

SELECT c.customer_id, company_name, order_count
FROM customers c
JOIN (SELECT customer_id, count(customer_id) as order_count from orders GROUP BY customer_id) o
ON c.customer_id=o.customer_id
ORDER BY order_count desc

### list orders grouped by customer's city showing number of orders per city. Returns 69 Records with _Aachen_ showing 6 orders and _Albuquerque_ showing 18 orders.

> This is very similar to the previous two queries, however, it focuses on the City rather than the CustomerName

SELECT city, order_count
FROM customers c
JOIN (SELECT customer_id, count(customer_id) as order_count from orders GROUP BY customer_id) o
ON c.customer_id=o.customer_id
-- WHERE city='Aachen' OR
-- city like 'Albu%'
GROUP BY city,order_count
ORDER BY order_count desc

## Data Normalization

Note: This step does not use PostgreSQL!

Take the following data and normalize it into a 3NF database. You can use the website https://www.tablesgenerator.com/markdown_tables# to help generate Markdown Tables.

| Person Name | Pet Name | Pet Type | Pet Name 2 | Pet Type 2 | Pet Name 3 | Pet Type 3 | Fenced Yard | City Dweller |
| ----------- | -------- | -------- | ---------- | ---------- | ---------- | ---------- | ----------- | ------------ |
| Jane        | Ellie    | Dog      | Tiger      | Cat        | Toby       | Turtle     | No          | Yes          |
| Bob         | Joe      | Horse    |            |            |            |            | No          | No           |
| Sam         | Ginger   | Dog      | Miss Kitty | Cat        | Bubble     | Fish       | Yes         | No           |

## Normalized Tables

Person Table
| id | name | fenced_yard | city_dweller |
|----|------|-------------|--------------|
| 1 | Jane | No | Yes |
| 2 | Bob | No | No |
| 3 | Sam | Yes | No |

Pet Table

| pet_id | id  | pet_name   | pet_type_id |
| ------ | --- | ---------- | ----------- |
| 1      | 1   | Ellie      | 1           |
| 2      | 1   | Tiger      | 2           |
| 3      | 1   | Toby       | 3           |
| 4      | 2   | Joe        | 4           |
| 5      | 3   | Ginger     | 1           |
| 6      | 3   | Miss Kitty | 2           |
| 7      | 3   | Bubble     | 5           |

Pet Type Table

| pet_type_id | pet_type |
| ----------- | -------- |
| 1           | Dog      |
| 2           | Cat      |
| 3           | Turtle   |
| 4           | Horse    |
| 5           | Fish     |

---

## Stretch Goals

### delete all customers that have no orders. Should delete 17 (or 18 if you haven't deleted the record added) records.

> This is done with a DELETE query

> In the WHERE clause, you can provide another list with an IN keyword this list can be the result of another SELECT query. Write a query to return a list of CustomerIDs that meet the criteria above. Pass that to the IN keyword of the WHERE clause as the list of IDs to be deleted

> Use a LEFT JOIN to join the Orders table onto the Customers table and check for a NULL value in the OrderID column

> Answer per spec
> DELETE FROM customers
> WHERE customer_id in (SELECT c.customer_id
> FROM customers c
> LEFT JOIN orders o
> ON o.customer_id=c.customer_id
> WHERE order_id is null)

> Better Answer:
> DELETE FROM customers
> WHERE customer_id NOT IN (select distinct customer_id from orders)

## Create Database and Table

### After creating the database, tables, columns, and constraint, generate the script necessary to recreate the database. This script is what you will submit for review.

- use pgAdmin to create a database, naming it `budget`.
- add an `accounts` table with the following _schema_:

  - `id`, numeric value with no decimal places that should autoincrement.
  - `name`, string, add whatever is necessary to make searching by name faster.
  - `budget` numeric value.

- constraints
  - the `id` should be the primary key for the table.
  - account `name` should be unique.
  - account `budget` is required.

> ## Create Database

## -- PostgreSQL database dump

-- Dumped from database version 12.0
-- Dumped by pg_dump version 12.0

-- Started on 2019-10-08 18:41:52

SET statement_timeout = 0;
SET lock_timeout = 0;
SET idle_in_transaction_session_timeout = 0;
SET client_encoding = 'UTF8';
SET standard_conforming_strings = on;
SELECT pg_catalog.set_config('search_path', '', false);
SET check_function_bodies = false;
SET xmloption = content;
SET client_min_messages = warning;
SET row_security = off;

SET default_tablespace = '';

SET default_table_access_method = heap;

--
-- TOC entry 203 (class 1259 OID 16907)
-- Name: accounts; Type: TABLE; Schema: public; Owner: postgres
--

CREATE TABLE public.accounts (
id bigint NOT NULL,
name text,
budget numeric NOT NULL
);

ALTER TABLE public.accounts OWNER TO postgres;

--
-- TOC entry 202 (class 1259 OID 16905)
-- Name: accounts_id_seq; Type: SEQUENCE; Schema: public; Owner: postgres
--

CREATE SEQUENCE public.accounts_id_seq
START WITH 1
INCREMENT BY 1
NO MINVALUE
NO MAXVALUE
CACHE 1;

ALTER TABLE public.accounts_id_seq OWNER TO postgres;

--
-- TOC entry 2824 (class 0 OID 0)
-- Dependencies: 202
-- Name: accounts_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: postgres
--

ALTER SEQUENCE public.accounts_id_seq OWNED BY public.accounts.id;

--
-- TOC entry 2688 (class 2604 OID 16910)
-- Name: accounts id; Type: DEFAULT; Schema: public; Owner: postgres
--

ALTER TABLE ONLY public.accounts ALTER COLUMN id SET DEFAULT nextval('public.accounts_id_seq'::regclass);

--
-- TOC entry 2690 (class 2606 OID 16917)
-- Name: accounts accounts_name_key; Type: CONSTRAINT; Schema: public; Owner: postgres
--

ALTER TABLE ONLY public.accounts
ADD CONSTRAINT accounts_name_key UNIQUE (name);

--
-- TOC entry 2692 (class 2606 OID 16915)
-- Name: accounts accounts_pkey; Type: CONSTRAINT; Schema: public; Owner: postgres
--

ALTER TABLE ONLY public.accounts
ADD CONSTRAINT accounts_pkey PRIMARY KEY (id);

-- Completed on 2019-10-08 18:41:52

--
-- PostgreSQL database dump complete
--
