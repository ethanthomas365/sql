# DC Assignment 2: Design a Logical Model and Advanced SQL

🚨 **Please review our [Assignment Submission Guide](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md)** 🚨 for detailed instructions on how to format, branch, and submit your work. Following these guidelines is crucial for your submissions to be evaluated correctly.

#### Submission Parameters:
* Submission Due Date: `April 07, 2026`
* Weight: 70% of total grade
* The branch name for your repo should be: `assignment-two`
* What to submit for this assignment:
    * This markdown (Assignment2.md) with written responses in Section 1 and 4
    * Two Entity-Relationship Diagrams (preferably in a pdf, jpeg, png format).
    * One .sql file 
* What the pull request link should look like for this assignment: `https://github.com/<your_github_username>/sql/pulls/<pr_id>`
    * Open a private window in your browser. Copy and paste the link to your pull request into the address bar. Make sure you can see your pull request properly. This helps the technical facilitator and learning support staff review your submission easily.

Checklist:
- [ ] Create a branch called `assignment-two`.
- [ ] Ensure that the repository is public.
- [ ] Review [the PR description guidelines](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md#guidelines-for-pull-request-descriptions) and adhere to them.
- [ ] Verify that the link is accessible in a private browser window.

If you encounter any difficulties or have questions, please don't hesitate to reach out to our team via our Slack. Our Technical Facilitators and Learning Support staff are here to help you navigate any challenges.

***

## Section 1:
You can start this section following *session 1*, but you may want to wait until you feel comfortable wtih basic SQL query writing. 

Steps to complete this part of the assignment:
- Design a logical data model
- Duplicate the logical data model and add another table to it following the instructions
- Write, within this markdown file, an answer to Prompt 3


###  Design a Logical Model

#### Prompt 1
Design a logical model for a small bookstore. 📚

At the minimum it should have employee, order, sales, customer, and book entities (tables). Determine sensible column and table design based on what you know about these concepts. Keep it simple, but work out sensible relationships to keep tables reasonably sized. 

Additionally, include a date table. 
A date table (also called a calendar table) is a permanent table containing a list of dates and various components of those dates. Some theory, tips, and commentary can be found [here](https://www.sqlshack.com/designing-a-calendar-table/), [here](https://www.mssqltips.com/sqlservertip/4054/creating-a-date-dimension-or-calendar-table-in-sql-server/) and [here](https://sqlgeekspro.com/creating-calendar-table-sql-server/). 
Remember, you don't actually need to run any of the queries in these articles, but instead understand *why* date tables in SQL make sense, and how to situate them within your logical models.

There are several tools online you can use, I'd recommend [Draw.io](https://www.drawio.com/) or [LucidChart](https://www.lucidchart.com/pages/).

**HINT:** You do not need to create any data for this prompt. This is a conceptual model only. 

#### Prompt 2
We want to create employee shifts, splitting up the day into morning and evening. Add this to the ERD.

#### Prompt 3
The store wants to keep customer addresses. Propose two architectures for the CUSTOMER_ADDRESS table, one that will retain changes, and another that will overwrite. Which is type 1, which is type 2? 

**HINT:** search type 1 vs type 2 slowly changing dimensions. 

```
One possible architecture for customer_address would be a table that stores only the current address for each customer. The way I conceive of this means that this table would include
customer_id, street, city, province (or state I suppose), postal_code, and country. When a customer changes address, the old values are overwritten with the new ones (in this case new address
overwrites the old one). This would be a Type 1 slowly changing dimension since the historical address information would not be preserved. Only up to date information is saved. 

A second possible architecture would be a table that keeps the full history of address changes and keeps old and new addresses linked through another table column. In this design, 
customer_address could include something like customer_id (or customer_adress_id if that level of specificity is required) to keep track of the customers who move and so it is easy to 
see a historical list of addresses tied to a single id. customer_address would also include street, city, province (or state), postal_code, country, effective_start_date, effective_end_date, and
is_current. In this architecture, each time a customer changes address, a new row is inserted for the new adress (it does not overwrite the old row), while the previous row is marked as no longer
current by updating its effective_end_date (date in which they moved out or the new address was added). This is a Type 2 slowly changing dimension, because it retains historical changes over
time. 

A link to the websites I used to read about Type 1 and type 2 slowly changing dimensions in case it is needed: 
https://coalesce.io/data-insights/type-1-vs-type-2-slowly-changing-dimensions/
https://www.thoughtspot.com/data-trends/data-modeling/slowly-changing-dimensions-in-data-warehouse
```

***

## Section 2:
You can start this section following *session 4*.

Steps to complete this part of the assignment:
- Open the assignment2.sql file in DB Browser for SQLite:
	- from [Github](./02_activities/assignments/assignment2.sql)
	- or, from your local forked repository  
- Complete each question, by writing responses between the QUERY # and END QUERY blocks


### Write SQL

#### COALESCE
1. Our favourite manager wants a detailed long list of products, but is afraid of tables! We tell them, no problem! We can produce a list with all of the appropriate details. 

Using the following syntax you create our super cool and not at all needy manager a list:
```
SELECT 
product_name || ', ' || product_size|| ' (' || product_qty_type || ')'
FROM product
```

But wait! The product table has some bad data (a few NULL values). 
Find the NULLs and then using COALESCE, replace the NULL with a blank for the first column with nulls, and 'unit' for the second column with nulls. 

**HINT**: keep the syntax the same, but edited the correct components with the string. The `||` values concatenate the columns into strings. Edit the appropriate columns -- you're making two edits -- and the NULL rows will be fixed. All the other rows will remain the same.

<div align="center">-</div>

#### Windowed Functions
1. Write a query that selects from the customer_purchases table and numbers each customer’s visits to the farmer’s market (labeling each market date with a different number). Each customer’s first visit is labeled 1, second visit is labeled 2, etc. 

You can either display all rows in the customer_purchases table, with the counter changing on each new market date for each customer, or select only the unique market dates per customer (without purchase details) and number those visits. 

**HINT**: One of these approaches uses ROW_NUMBER() and one uses DENSE_RANK().

Filter the visits to dates before April 29, 2022.

2. Reverse the numbering of the query so each customer’s most recent visit is labeled 1, then write another query that uses this one as a subquery (or temp table) and filters the results to only the customer’s most recent visit. 
**HINT**: Do not use the previous visit dates filter.

3. Using a COUNT() window function, include a value along with each row of the customer_purchases table that indicates how many different times that customer has purchased that product_id.

You can make this a running count by including an ORDER BY within the PARTITION BY if desired.
Filter the visits to dates before April 29, 2022.

<div align="center">-</div>

#### String manipulations
1. Some product names in the product table have descriptions like "Jar" or "Organic". These are separated from the product name with a hyphen. Create a column using SUBSTR (and a couple of other commands) that captures these, but is otherwise NULL. Remove any trailing or leading whitespaces. Don't just use a case statement for each product! 

| product_name               | description |
|----------------------------|-------------|
| Habanero Peppers - Organic | Organic     |

**HINT**: you might need to use INSTR(product_name,'-') to find the hyphens. INSTR will help split the column. 

2. Filter the query to show any product_size value that contain a number with REGEXP. 

<div align="center">-</div>

#### UNION
1. Using a UNION, write a query that displays the market dates with the highest and lowest total sales.

**HINT**: There are a possibly a few ways to do this query, but if you're struggling, try the following: 1) Create a CTE/Temp Table to find sales values grouped dates; 2) Create another CTE/Temp table with a rank windowed function on the previous query to create "best day" and "worst day"; 3) Query the second temp table twice, once for the best day, once for the worst day, with a UNION binding them. 

***

## Section 3:
You can start this section following *session 5*.

Steps to complete this part of the assignment:
- Open the assignment2.sql file in DB Browser for SQLite:
	- from [Github](./02_activities/assignments/assignment2.sql)
	- or, from your local forked repository  
- Complete each question, by writing responses between the QUERY # and END QUERY blocks

### Write SQL

#### Cross Join
1. Suppose every vendor in the `vendor_inventory` table had 5 of each of their products to sell to **every** customer on record. How much money would each vendor make per product? Show this by vendor_name and product name, rather than using the IDs.

**HINT**: Be sure you select only relevant columns and rows. Remember, CROSS JOIN will explode your table rows, so CROSS JOIN should likely be a subquery. Think a bit about the row counts: how many distinct vendors, product names are there (x)? How many customers are there (y). Before your final group by you should have the product of those two queries (x\*y). 

<div align="center">-</div>

#### INSERT
1. Create a new table "product_units". This table will contain only products where the `product_qty_type = 'unit'`. It should use all of the columns from the product table, as well as a new column for the `CURRENT_TIMESTAMP`.  Name the timestamp column `snapshot_timestamp`.

2. Using `INSERT`, add a new row to the product_unit table (with an updated timestamp). This can be any product you desire (e.g. add another record for Apple Pie). 

<div align="center">-</div>

#### DELETE 
1. Delete the older record for the whatever product you added.

**HINT**: If you don't specify a WHERE clause, [you are going to have a bad time](https://imgflip.com/i/8iq872).

<div align="center">-</div>

#### UPDATE
1. We want to add the current_quantity to the product_units table. First, add a new column, `current_quantity` to the table using the following syntax.
```
ALTER TABLE product_units
ADD current_quantity INT;
```

Then, using `UPDATE`, change the current_quantity equal to the **last** `quantity` value from the vendor_inventory details. 

**HINT**: This one is pretty hard. First, determine how to get the "last" quantity per product. Second, coalesce null values to 0 (if you don't have null values, figure out how to rearrange your query so you do.) Third, `SET current_quantity = (...your select statement...)`, remembering that WHERE can only accommodate one column. Finally, make sure you have a WHERE statement to update the right row, you'll need to use `product_units.product_id` to refer to the correct row within the product_units table. When you have all of these components, you can run the update statement.
*** 

## Section 4:
You can start this section anytime.

Steps to complete this part of the assignment:
- Read the article
- Write, within this markdown file, between 250 and 1000 words. No additional citations/sources are required.

### Ethics

Read: Boykis, V. (2019, October 16). _Neural nets are just people all the way down._ Normcore Tech. <br>
    https://vicki.substack.com/p/neural-nets-are-just-people-all-the

**What are some of the ethical issues important to this story?**

Consider, for example, concepts of labour, bias, LLM proliferation, moderating content, intersection of technology and society, ect. 


```
Funnily enough, although I don't work with them just yet, other graduate students in my lab do a ton of work using neural networks, specifically for object recognition,
and how neural nets would go on to pick up objects (using heatmaps as their preferred grasp points). Though I won't get a chance to "interview" my lab mates on their opinions of all this
before it is due, it is an opinion I look forward to hearing on how they treat datasets and how they handle the ethics of training sets and testing neural networks. Focusing specifically
on the article at hand I think it is both funny and horifying how much we take the tools and products we have available to us for granted. Focusing specifically on LLMs (chatgpt), 
they're in a very peculiar spot in their life cycle. When the first version of Chatgpt launched in 2023 (might have been 2022 I am unsure), there was almost an unbridled optimism for the 
technology and what it meant. Fast forward 3 years later to today, and we're seeing a lot of pushback towards its proliferation in our day to day lives and that is because we are slowly becoming
more cognizant of the ethical boundaries it pushes. These massives LLMs need massive data centres which both continue to pollute the ecosystem and increase the cost of living of those living
nearby. The same jobs that were used to create the AIs are being replaced by the very same technology. Funily enough, about 5 years ago now I read a small book titled "The Tyranny of Algorithms"
which was published a couple years before chatgpt was public, and the doomscrolling epidemic was at an all time high. How algorithms were built, what they were built on, the terrible datasets that
exist are still out there and still being reused. There is a lot of unpaid labour that is stripped to make these large LLMs and datasets possible and therefore biases consistently slip through 
and make the lives of certain groups worse despite this supposed advancement. Returning to the article, learning that robots cannot do "simple" tasks like fold clothes is also why I think we 
are seeing pushback for "AI" and its related products. The dream (in my opinion) would have these tools accomplish the back breaking labour that is often done at the cost of human rights laws, and
offset to a machine or thing that can do it for us, leaving more people with time to pursue things like the Arts and Sciences (something I believe all humans psychologically need to pursue as
the highest form of self-fulfillment). Instead we see these tools take the jobs of artists, increase prices and make quality of life for those already struggling worse, and in a sense, it is 
a perversion of what could have been a public tool to make life better, along with making the Arts and Sciences more accessible to all. Without these tools being public, we are at the mercy of 
large billion dollar corporations moderating the content, allowing what is shown to us, all in a ploy to drive ramapant consumerism. Biases that work to split people a part all because it may sell
more of a product will be and likely are baked into all these products, whether intentional or not. Even open source things like the imagenet discussed in the article was built off almost 60 years
of human work. Human work with biases in a time where we were in the midsts of the civil rights movements. 

To give a quick conclusion, there are a lot of considerations that go into building databases for training neural nets, LLMs, especially when trying to avoid the biases of society getting baked in. 
The goal is never to reproduce the systematic biases of a society, yet it is almost impossible when training techniques are kept "hidden" or are undisclosed, or come from lexical libraries from the
early 1960s. The pushback on datacentres, the increasing ram costs, and the fact that AI hasn't actually contributed to the job market yet are all reasons why we see pushback for these products.
Although there is pushback, only time will tell how much lawmakers and their constituents will fight to have these products be public domain and not the sole product of private enterprise in the
hopes of stealing jobs or finding ways to skirt paying employees minumum wage.


If someone from the learning staff did read this half baked word vomit of a rant I went on, I appreciate it. I apologize my thoughts were all over the place but I treated this mainly as a writing
exercise for a topic I am deeply passionate about (in the ethics of all this). I would have loved for this to be a more coherent, research style text on the ethics of training and deploying 
LLMs, but I didn't have the time, and again I enjoyed using these spaces as a writing exercise for myself. Again, Thank you if you did read all this!

```
