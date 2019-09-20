---
layout: post
title:  "Buddies! SQL and Python to Answer Business Questions"
date:   2019-03-15 20:40:42 +1000
categories: jekyll update
---

![buddies]({{ site.baseurl }}/images/buddies.jpg)

# Mining the Chinook Database with SQL and Python

In this project I'll be using SQL and Python together to mine a simple SQLite database to answer specific business questions.

### Helper Function
To assist with the interaction of these two languages, I'll create a helper function to run SQL queries in the Python environment. I'll use this to extract data from the database into Python dataframes, so it can then be further manipulated and visualised with Python as desired.


### Chinook Database
I'll be accessing the Chinook database, a sample data base representing a digital music store, with tables for artists, albums, media tracks, invoices and customers, created using real data from an iTunes Library.

More details about the Chinook database can be found [here](https://github.com/lerocha/chinook-database/tree/master/ChinookDatabase). A copy of the database schema can be found [here](https://github.com/lerocha/chinook-database/wiki/Chinook-Schema)

**Chinook Schema:**
![chinook_schema]({{ site.baseurl }}/images/chinook_schema_2.png)

Source: [Github](https://github.com/lerocha/chinook-database/wiki/Chinook-Schema)


## Create Helper Function to Quickly Execute SQL Queries
  
`run_query()`  
This will take an SQL query as an argument and return the results of that query in a pandas dataframe. It also makes use of a [context manager](https://jeffknupp.com/blog/2016/03/07/python-with-context-managers/) to open and close the database connection.

*Note:* As this function will be reading the results into a dataframe, this will be used exclusively for `SELECT` statements.

```python
import sqlite3
import pandas as pd
import matplotlib.pyplot as plt
%matplotlib inline

# Increase column character limit
pd.set_option('max_colwidth', 50)


# Create function to connect to DB, run query and return results in dataframe
def run_query(q):
    with sqlite3.connect('chinook.db') as conn:
        return pd.read_sql(q, conn)

    
# Test with query to return all tables and views in my database
test = '''
SELECT 
    name,
    tbl_name
FROM sqlite_master 
WHERE type IN ('table', 'view') 
ORDER BY type;
'''

run_query(test)
```

| name           | tbl_name       |
|:---------------|:--------------:|
| album          | album          |
| artist         | artist         |
| customer       | customer       |
| employee       | employee       |
| genre          | genre          |
| invoice        | invoice        |
| invoice_line   | invoice_line   |
| media_type     | media_type     |
| playlist       | playlist       |
| playlist_track | playlist_track |
| track          | track          |

## Task \#1: Album Recommendation

The store wants to add some new albums to sell to the US market. I've been given a list of 4 albums, all different genres, by different artists, and I need to decide which would be the best 3 to add to the store.

| Artist Name          | Genre   |
|:---------------------|:-------:|
| Regal                | Hip-Hop |
| Red Tone             | Punk    |
| Meteor and the Girls | Pop     |
| Slim Jim Bites       | Blues   |
  
None of the artists currently have any music in store, so I can't go off their past sales performance.  

Our target market is the USA, so decide to investigate what genres already perform well in the US market.

I extract data for total dollar value and percentage of the market held by the different genres of music in the US. 

```Python
q1 = '''
WITH dataset AS
(
    SELECT 
        il.track_id, 
        quantity, 
        g.name genre
    FROM 
        invoice_line il
    INNER JOIN invoice i ON il.invoice_id = i.invoice_id
    INNER JOIN tracK t ON il.track_id = t.track_id
    INNER JOIN genre g ON t.genre_id = g.genre_id
    WHERE i.billing_country = 'USA'
)

SELECT 
    genre, 
    COUNT(*) sales_USA, 
    ROUND(COUNT(*)/CAST((SELECT COUNT(*) FROM dataset) AS FLOAT)*100, 2) perc_sales_USA
FROM dataset
GROUP BY genre
ORDER BY 2 DESC
;
'''
run_query(q1)
```

| genre              |   sales_USA |   perc_sales_USA |
|:-------------------|------------:|:----------------:|
| Rock               |         561 |            53.38 |
| Alternative & Punk |         130 |            12.37 |
| Metal              |         124 |            11.8  |
| R&B/Soul           |          53 |             5.04 |
| Blues              |          36 |             3.43 |
| Alternative        |          35 |             3.33 |
| Latin              |          22 |             2.09 |
| Pop                |          22 |             2.09 |
| Hip Hop/Rap        |          20 |             1.9  |
| Jazz               |          14 |             1.33 |
| Easy Listening     |          13 |             1.24 |
| Reggae             |           6 |             0.57 |
| Electronica/Dance  |           5 |             0.48 |
| Classical          |           4 |             0.38 |
| Heavy Metal        |           3 |             0.29 |
| Soundtrack         |           2 |             0.19 |
| TV Shows           |           1 |             0.1  |

Once I have this data I plot it.

Based on the results below for sales data of music genres in the USA, the 3 artists we should purchase, in order of genre performance, are:

1. Red Tone - Punk
2. Slim Jim Bites - Blues
3. Meteor and the Girls - Pop

*Note:* these 3 genres combined make up only 18% of total sales, whereas `Rock` as a genre on its own makes up over 53% of the market. 

We don't have any `Rock` artists in out pool to add to our store, it would be good to keep an eye out for some going forward.

```python
# Assign dataframe to variable name and plot
sales_USA = run_query(q1)
sales_USA.loc[:9].plot.barh(x='genre', y='perc_sales_USA', figsize=(10,6),
                            legend=False, color='g')

# Set labels, title
plt.ylabel('')
plt.xlabel('Percentage of Sales - USA')
plt.title('% Top Selling Music Genres USA', fontsize=14, fontweight='bold')

# Remove tick perams
plt.tick_params(bottom=False, top=False, left=False, right=False)

# Remove spines
for key, value in plt.gca().spines.items():
    value.set_visible(False)

# Set xticks
plt.xticks([0,50])

# Set vlines
plt.gca().vlines([10, 20, 30, 40, 50], ymin=0, ymax=10, alpha=0.1)
plt.savefig('chinook_music_usa.png')
plt.show()
```

![chinook_music_usa]({{ site.baseurl }}/images/chinook_music_usa.png)

## Task \#2: Analyse Employee Sales Performance

Each customer for the Chinook store gets assigned to a sales support rep within the company when they first make a purchase. 

It's now my job to analyze the purchases of customers belonging to each employee to see how sales support reps are performing relative to each other.


#### Note:
A few points to note with the Chinook database.
- Invoices date up to December 2020
- The link between sales agent and how much they have sold is sales rep -> customer -> invoice
    - *However* I can see that there are invoices for customers of a particular rep existing from *before* that rep was hired. I assume that these customers were assigned to other reps before the current reps were hired, and made those purchases with these previous reps  
    
```python
q2a='''

WITH sales AS
    (
    SELECT
        (e.first_name || " " || e.last_name) employee_name, 
        e.title,
        e.hire_date,
        c.customer_id,
        i.total as sale, 
        i.invoice_date
    FROM invoice i
INNER JOIN customer c ON c.customer_id = i.customer_id
INNER JOIN employee e ON e.employee_id = c.support_rep_id

)


SELECT 
    employee_name as sales_rep, 
    strftime("%Y-%m", hire_date) hire_date,  
    strftime("%Y-%m", min(invoice_date)) customer_first_invoice, 
    strftime("%Y-%m", max(invoice_date)) customer_last_invoice,
    cast((julianday('2020-12-31') - julianday(hire_date)) / 30 AS INTEGER) as rep_tenure_months
FROM sales
GROUP BY 1, 2
;

'''

run_query(q2a)
```

| sales_rep     | hire_date   | customer_first_invoice   | customer_last_invoice   |   rep_tenure_months |
|:--------------|:------------|:-------------------------|:------------------------|:-------------------:|
| Jane Peacock  | 2017-04     | 2017-01                  | 2020-12                 |                  45 |
| Margaret Park | 2017-05     | 2017-01                  | 2020-12                 |                  44 |
| Steve Johnson | 2017-10     | 2017-01                  | 2020-12                 |                  39 |


In order to analyse performance I'll look at sales from a few different angles:

1. Total sales
2. Average monthly sales
3. Actual sales month to month 
4. Distribution of sales


#### 1. Total Sales to Date by Sales Rep

I group totals sales by rep, only taking into account sales made after the rep hire date.

I can see that Jane is the highest performing rep with her sales making my 37% of total sales. Her sales total is 10% greater than Margaret's, and almost 32% greater than Steve's.

However, she is the longest-serving sales rep, so it's not surprising that her running total of sales is greater than those of her colleagues.

```python
q2b='''

WITH sales AS
    (
    SELECT
        (e.first_name || " " || e.last_name) sales_rep, 
        e.title,
        e.hire_date,
        c.customer_id,
        i.total as sale, 
        i.invoice_date
    FROM invoice i
    INNER JOIN customer c ON c.customer_id = i.customer_id
    INNER JOIN employee e ON e.employee_id = c.support_rep_id
    WHERE hire_date < invoice_date

)

SELECT 
    sales_rep,
    SUM(sale) AS total_sales,
    CAST((julianday('2020-12-31') - julianday(hire_date)) / 30 AS INTEGER) AS rep_tenure_months,
    ROUND((sum(sale) / CAST((julianday('2020-12-31') - julianday(hire_date)) / 30 AS INTEGER)), 2) sales_per_month
FROM sales
WHERE hire_date < invoice_date
GROUP by 1
;

'''

print(run_query(q2b))

# Assign to variable name
sales_totals = run_query(q2)

# Plot total sales data 
plt.figure(figsize=(8,8))
sales_pie = sales_totals.set_index('sales_rep')
sales_pie = sales_pie["total_sales"].copy().rename('')
explode = (0, 0, 0.1) 
sales_pie.sort_values().plot.pie(
    startangle=90,
    counterclock=False,
    autopct='%1.1f%%',
    explode = explode
)
plt.title('$ Total Sales Performance', fontsize=12, fontweight='bold')
plt.savefig('chinook_total_perf.png')
plt.show()

```

![chinook_total_perf]({{ site.baseurl }}/images/chinook_total_perf.png)

#### 2. Average Monthly Sales by Sales Rep

I decide to look at average monthly sales to make a fairer comparison.

From the results it does appear that Jane is top of the leader board after all. I can see that **on average**, Jane sells 8% - 14% **per month** more than her colleagues.

```python
# Plot average monthy sales
sales_totals.plot.barh(x='sales_rep', y='sales_per_month', legend=False, width=0.3, figsize=(10,3))

# remove tick params and spines
plt.tick_params(bottom=False, top=False, left=False, right=False)
for key, value in plt.gca().spines.items():
    value.set_visible(False)
plt.gca().invert_yaxis()
plt.ylabel('')
plt.xlabel('$ Sales')
plt.title('$ Avg Sales per Month Performance', fontsize=12, fontweight='bold')
plt.savefig('chinook_month_perf.png')
plt.show()
```

![chinook_month_perf]({{ site.baseurl }}/images/chinook_month_perf.png)

#### 3. Actual Sales Month to Month

Let's see how the reps perform relative to each other for any given month. I get all the sales data for reps grouped by month and plot it on a line graph.

However, I can see **in this format** the results are not the easiest to interpret and draw conclusions from.

```python
# Get total sales grouped by rep and sales month

q2c='''

WITH sales AS
    (
    SELECT
        (e.first_name || " " || e.last_name) sales_rep, 
        e.title,
        e.hire_date,
        c.customer_id,
        i.total as sale, 
        strftime("%Y-%m", i.invoice_date) invoice_year_month
FROM invoice i
    INNER JOIN customer c ON c.customer_id = i.customer_id
    INNER JOIN employee e ON e.employee_id = c.support_rep_id
    WHERE hire_date < invoice_year_month

)

SELECT 
    sales_rep,
    sum(sale) as monthly_sales,
    invoice_year_month
FROM sales
GROUP BY 1, 3
ORDER BY 1, 3
;

'''

# Assign dataframe to variable name
monthly_sales = run_query(q2c)

# Format date column to datetime
xticklables = monthly_sales['invoice_year_month'].unique().sort()
monthly_sales['invoice_year_month']  = pd.to_datetime(monthly_sales['invoice_year_month'])

# List of reps
reps = monthly_sales['sales_rep'].unique()

# Plot sales
plt.figure(figsize=(26,14))
for rep in reps:
    plt.plot(monthly_sales.loc[monthly_sales['sales_rep'] == rep, 'invoice_year_month'], 
          monthly_sales.loc[monthly_sales['sales_rep'] == rep, 'monthly_sales'], label = rep)

# Remove tick params adnd spines
plt.tick_params(bottom=False, top=False, left=False, right=False)
for key, value in plt.gca().spines.items():
    value.set_visible(False)

plt.xticks(rotation=90, fontsize=14)
plt.yticks(fontsize=14)
plt.legend(prop={'size': 14})
plt.ylabel('$ Sales', fontsize=14)
plt.title('$ Monthly Sales Perfomance', fontsize=16, fontweight='bold')
plt.savefig('chinook_monthly.png')
plt.show()

```

<img src="https://raw.githubusercontent.com/ainephelan/ainephelan.github.io/master/images/chinook_monthly.jpg" alt="drawing" width="1000px"/>


#### 4. Distribution of Sales Values
Let's take a look at the distributions of sales made instead.

I'll display the median sales value as well. I'm choosing median as the most representative value as I can see all three distributions are a bit skewed, and the median is a robust statistic, resistant to the pull of outliers.

While we can see that the distributions are not incredibly dissimilar from each other, we have more of a granular understanding now of the types of sales that the reps are making. 

We can see that more of Jane's and Steve's sales fall below the average sale value, whereas more of Margaret's are above the average sale value. 

Jane however, does appear to outperform, with more higher value sales than her counterparts.

```python
# Plot figure with 2 axes
fig, axes = plt.subplots(nrows=1, ncols=2, figsize=(20, 6))
ax1, ax2 = axes.flatten()
fig.suptitle('$ Monthly Sales Performace', fontsize=16, fontweight='bold')

## ax1 = kde
for rep in reps:
    monthly_sales.loc[monthly_sales['sales_rep'] == rep, 'monthly_sales'].plot.kde(label=rep, ax=ax1)
ax1.set_xlabel('$ Monthly Sales')
ax1.set_xlim(monthly_sales['monthly_sales'].min(), monthly_sales['monthly_sales'].max())
ax1.axvline(monthly_sales['monthly_sales'].median(), color = 'k', alpha=0.2, label = 'Median')

# Annotate
ax1.text(21, .002, 'Median')
ax1.text(20, .023, 'Jane')
ax1.text(40, .022, 'Margaret')
ax1.text(38, .01, 'Steve')

# Remove tick params and spines
ax1.tick_params(top=False, right=False, left=False, bottom=False)
for key, value in ax1.spines.items():
    value.set_visible(False)

## ax2 = stripplot
import seaborn as sns
sns.stripplot(x='sales_rep', y='monthly_sales', data=monthly_sales, jitter=True, ax=ax2)
ax2.set_xlabel('')
ax2.set_ylabel('$ Monthly Sales')
ax2.axhline(monthly_sales['monthly_sales'].median(), color='k', alpha=0.2, label='Median Sales')

# Remove tick params and spines
ax2.tick_params(top=False, right=False, left=False, bottom=False)
for key, value in ax2.spines.items():
    value.set_visible(False)

plt.legend(loc='upper left')
plt.savefig('chinook_distros.png')
plt.show()

```

![chinook_distros]({{ site.baseurl }}/images/chinook_distros.png)



