---
layout: post
title: Understanding Alcohol Product Relationships Using Association Rule Learning
image: "/posts/association-rules-title-img.png"
tags: [Association Rule Learning, Python]
---

In this project, I used Association Rule Learning to analyse the transactional relationships and dependencies between products in the alcohol section of a grocery store.

# Table of contents

- [00. Project Overview](#overview-main)
    - [Context](#overview-context)
    - [Actions](#overview-actions)
    - [Results](#overview-results)
    - [Growth/Next Steps](#overview-growth)
- [01. Data Overview](#data-overview)
- [02. Apriori Overview](#apriori-overview)
- [03. Data Preparation](#apriori-data-prep)
- [04. Applying The Apriori Algorithm](#apriori-fit)
- [05. Interpreting The Results](#apriori-results)
- [06. Growth & Next Steps](#growth-next-steps)

___

# Project Overview  <a name="overview-main"></a>

### Context <a name="overview-context"></a>

My client was looking to re-jig the alcohol section within their store.  Customers often complained that they couldn't find the products they wanted, and many asked for recommendations about which other products to try.  On top of this, their marketing team wanted to start running "bundled" promotions as this had worked well in other areas of the store, but they needed guidance with selecting which products to put together.

They provided a sample of 3,500 alcohol transactions. My task was to see if I could find solutions or insights that might help the business address these problems.

<br>
<br>
### Actions <a name="overview-actions"></a>

I applied Association Rule Learning - specifically the *Apriori* algorithm - to examine and analyse the strength of relationship between different products within the transactional data.

First, I installed the apyori package, which contains all of the required functionality for this task.

Then I brought in the sample data, and got it into the right format for the Apriori algorithm to deal with.

From there I applied the Apriori algorithm to provide the following relationship metrics:

* Support
* Confidence
* Expected Confidence
* Lift

These metrics examine product relationships in different ways, so I utilised each to put forward ideas that address each of the tasks at hand.  You can read more about these metrics, and the Apriori algorithm, in the relevant section below.

<br>
<br>

### Results <a name="overview-results"></a>

Interestingly, the strongest relationship existed between two products labelled as "gifts" - this was useful information for the category managers, as it suggested that gifts should be available in the same section of the aisle, if that was not already the case.

There was also some strong relationships between French wines, which suggested that wines should be arranged by country, rather than by type, to make it easier for customers to find what they are after. Interestingly, while there appeared to be *some* relationship between different New Zealand wines, they seemed to be more associated with French and South American wines - where the climate is similar - than they were with Australian Wines. This suggested that they should not necessarily be placed alongside Australian wines simply because they are both antipodean!

Another interesting association was between products labelled "small".  It was possible that these were being bought by customers doing a light shop with a basket, rather than a trolley, so I suggested that this be looked at more closely by category managers, in case they felt it was worth placing these items nearer to the checkout so that they were easier to carry.

In addition to these specific insights, I proposed to build a "search engine" for category managers where they could look-up products by keyword in the product association table to investigate other relationships.

<br>
<br>
### Growth/Next Steps <a name="overview-growth"></a>

As this was first and foremost an exploratory project, the next step would be to discuss the results with Category Managers to share insights. From there I would recommend applying this same logic to all other categories, as well as potentially across the full-product range.

I would also propose the building of a "Keyword Search Engine" to help Category Managers extract and utilise the insights held within the data.

<br>
<br>

___

# Data Overview  <a name="data-overview"></a>

The initial dataset contaied 3,500 transactions, each of which showed the alcohol products that were present in that transaction.  

I used the code below to import Pandas and the apriori algorithm from the apyori library, and to bring the raw data into Python.
<br>
```python

# import required Python packages
import pandas as pd
from apyori import apriori

# import the sample data
alcohol_transactions = pd.read_csv("data/sample_data_apriori.csv")

```
<br>

A sample of this data (the first 10 transactions) can be seen below:
<br>
<br>

| **transaction_id** | **product1** | **product2** | **product3** | **product4** | **product5** | **…** |
|---|---|---|---|---|---|---|
| 1 | Premium Lager | Iberia | … |  |  | ... |
| 2 | Sparkling | Premium Lager | Premium Cider | Own Label | Italy White | … |
| 3 | Small Sizes White | Small Sizes Red | Sherry Spanish | No/Low Alc Cider | Cooking Wine | … |
| 4 | White Uk | Sherry Spanish | Port | Italian White | Italian Red | … |
| 5 | Premium Lager | Over-Ice Cider | French White South | French Rose | Cocktails/Liqueurs | … |
| 6 | Kosher Red | … |  |  |  | ... |
| 7 | Own Label | Italy White | Australian Red | … |  | ... |
| 8 | Brandy/Cognac | … |  |  |  | ... |
| 9 | Small Sizes White | Bottled Ale | … |  |  | ... |
| 10 | White Uk | Spirits Mixers | Sparkling | German | Australian Red | … |
| … | … | … | … | … | … | … |

<br>
To explain this data, *Transaction 1* (the first row) contained two products: Premium Lager and Iberia.  As there were only two products in this transaction, the remaining columns are blank.

Transaction 2 (the second row) contained nine products (not all shown in the snippet).  The first nine columns for this row are therefore populated, followed by blank values.

For our sample data, the maximum number of unique products was 45, meaning the table of data had a total of 46 columns (45 for products + transaction_id).

The *apyori* library that I used does not want the data in this format. Instead, it wants it passed in as a *list of lists* so I needed to modify it.  The code and logic for this can be found in the Data Preparation section below.

___
<br>
# Apriori Overview  <a name="apriori-overview"></a>

Association Rule Learning is an approach that discovers the strength of relationships between different data-points.  It is commonly utilised to understand which products are frequently (or infrequently) purchased together.

In a business sense, this can provide some really interesting and useful information that can help optimise the following:

* Product Arrangement/Placement (making the customer journey more efficient)
* Product Recommendations (customers who purchased product A also purchased product B)
* Bundled Discounts (which products should/should not be put together)

One powerful, intuitive, and commonly used algorithm for Association Rule Learning is **Apriori**.

In Apriori there are four key metrics:

* Support
* Confidence
* Expected Confidence
* Lift

Each of these metrics help us understand items and their relationship with other items in their own way.

<br>
##### Support

Support is extremely intuitive. It simply tells us the percentage of all transactions that contain *both* item A and item B.  To calculate this, we’d just count up the transactions that include both items, and divide this by the total number of transactions.

You can think of support as a baseline metric that helps us understand how common or popular this particular *pair* of items is.

<br>
##### Confidence

Confidence takes us a little bit further than support, and looks more explicitly at the *relationship* between the two items.

It asks "of all transactions that *included item A*, what proportion also included item B?"  

In other words, here we are counting up the number of transactions that contained *both items A and B* and then rather than dividing by *all transactions* like we did for support, we instead divide this by the *total number of transactions that contained item A*.

A high score for confidence can mean a strong product relationship - but not always!  When one of the items is very popular, we can get an inflated score.  To help us regulate this, we can look at two further metrics: expected confidence and lift.

<br>
##### Expected Confidence

Expected confidence is quite simple: it is the percentage of *all transactions* that *contained item B*.

This is important as it provides indication of what the confidence *would be* if there were no relationship between the items.  We can use expected confidence, along with confidence, to calculate our final (and most powerful) metric - lift!

<br>
##### Lift

Lift is the factor by which the confidence exceeds the expected confidence.  In other words, lift tells us how likely item B is purchased *when item A is purchased*, while *controlling* for how popular item B is.

We calculate lift by dividing confidence by expected confidence.

A lift score *greater than 1* indicates that items A and B appear together *more often* than expected, and conversely a lift score *less than 1* indicates that items A and B appear together *less often* than expected.

<br>
##### In Practice

While above we're just discussing two products (item A and item B), in reality this score would be calculated between *all* pairs of products, and we could then sort these by lift score (for example) and see exactly what the strongest or weakest relationships were. Then we could use this information to guide our decisions regarding product layout, recommendations for customers, or promotions.

<br>
##### An Important Consideration

Something to consider when assessing the results of Apriori is that Item/Product relationships that have a *high Lift score* but also have a *low Support score* should be interpreted with caution!

In other words, if we sorted all item relationships by descending lift score, the one that comes out on top might initially seem very impressive and it may appear that there is a very strong relationship between the two items.  Always take into account the support metric - it could be that this relationship is only taking place by chance due to the rarity of the item set.

___
<br>
# Data Preparation  <a name="apriori-data-prep"></a>

As mentioned in the Data Overview section above, the *apyori* library that I used does not want the data in table format. Instead, it wants it passed in as a *list of lists* so I needed to modify it here.  

I used the code below to perform the following tasks:

* Remove the ID column as it is not required
* Iterate over the DataFrame, appending each transaction to a list, and appending those to a master list
* Print out the first 10 lists from the master list

<br>
```python

# drop ID column
alcohol_transactions.drop("transaction_id", axis = 1, inplace = True)

# modify data for apriori algorithm
transactions_list = []
for index, row in alcohol_transactions.iterrows():
    transaction = list(row.dropna())
    transactions_list.append(transaction)
    
# print out first 10 lists from master list
print(transactions_list[:10])

[['Premium Lager', 'Iberia'],
 ['Sparkling', 'Premium Lager', 'Premium Cider', 'Own Label', 'Italy White', 'Italian White', 'Italian Red', 'French Red', 'Bottled Ale'],
 ['Small Sizes White', 'Small Sizes Red', 'Sherry Spanish', 'No/Low Alc Cider', 'Cooking Wine', 'Cocktails/Liqueurs', 'Bottled Ale'],
 ['White Uk', 'Sherry Spanish', 'Port', 'Italian White', 'Italian Red'],
 ['Premium Lager', 'Over-Ice Cider', 'French White South', 'French Rose', 'Cocktails/Liqueurs', 'Bottled Ale'],
 ['Kosher Red'],
 ['Own Label', 'Italy White', 'Australian Red'],
 ['Brandy/Cognac'],
 ['Small Sizes White', 'Bottled Ale'],
 ['White Uk', 'Spirits Mixers', 'Sparkling', 'German', 'Australian Red', 'American Red']]

```
<br>

As you can see from the print statement, each transaction (row) from the initial DataFrame is now contained within a list, all making up the master list.

___
<br>
# Applying The Apriori Algorithm <a name="apriori-fit"></a>

In the code below, I applied the apriori algorithm from the apyori library.

This algorithm allows us to specify the association rules that we want.  I set:

* A minimum *Support* of 0.003 to eliminate very rare product sets
* A minimum *Confidence* of 0.2
* A minimum *Lift* of 3 to ensure I was only focusing on product sets with strong relationships
* A minimum and maximum length of 2, meaning I was focusing on product *pairs* rather than larger sets

```python

# apply the apriori algorithm and specify required parameters
apriori_rules = apriori(transactions_list,
                        min_support = 0.003,
                        min_confidence = 0.2,
                        min_lift = 3,
                        min_length = 2,
                        max_length = 2)

# convert the output to a list
apriori_rules = list(apriori_rules)

# print out the first element
apriori_rules[0]

RelationRecord(items=frozenset({'America White', 'American Rose'}), support=0.020745724698626296, ordered_statistics=[OrderedStatistic(items_base=frozenset({'American Rose'}), items_add=frozenset({'America White'}), confidence=0.5323741007194245, lift=3.997849299507762)])

```
<br>
The output from the algorithm is in the form of a generator. I converted this to a list as this is easier to manipulate and analyse.  

Based upon the parameters I set when applying the algorithm, I was returned 132 product pairs.  I printed out the first element from the list to see what the output looked like, and while this contained all the key information I needed, to make it easier to analyse (and more accessible & useable for stakeholders), in the next code snippet I extracted the key elements and used list comprehension to re-work this data to exist as a Pandas DataFrame.

```python

# extract each piece of information
product1 = [list(rule[2][0][0])[0] for rule in apriori_rules]
product2 = [list(rule[2][0][1])[0] for rule in apriori_rules]
support = [rule[1] for rule in apriori_rules]
confidence = [rule[2][0][2] for rule in apriori_rules]
lift = [rule[2][0][3] for rule in apriori_rules]

# compile into a single dataframe
apriori_rules_df = pd.DataFrame({"product1" : product1,
                                 "product2" : product2,
                                 "support" : support,
                                 "confidence": confidence,
                                 "lift" : lift})

```
<br>
A sample of this data (the first 5 product pairs - not in any order) can be seen below:
<br>
<br>

| **product1** | **product2** | **support** | **confidence** | **lift** |
|---|---|---|---|---|
| American Rose | America White | 0.021 | 0.532 | 3.998 |
| America White | American White | 0.054 | 0.408 | 3.597 |
| Australian Rose | America White | 0.005 | 0.486 | 3.653 |
| Low Alcohol A.C | America White | 0.003 | 0.462 | 3.466 |
| American Rose | American Red | 0.016 | 0.403 | 3.575 |
| … | … | … | … | … |

<br>
In the DataFrame, there are two products in the pair, and then the three key metrics: Support, Confidence, and Lift. 

___
<br>
# Interpreting The Results <a name="apriori-results"></a>

<br>
#### Associated Products

Now the data was in a useable format, I wanted to look at the product pairs with the *strongest* relationships. I therefore sorted the Lift column in descending order.

```python

# sort pairs by descending Lift
apriori_rules_df.sort_values(by = "lift", ascending = False, inplace = True)

```

<br>
In the table below, we can see the ten highest product relationships, based upon Lift.
<br>
<br>

| **product1** | **product2** | **support** | **confidence** | **lift** |
|---|---|---|---|---|
| Wine Gifts | Beer/Lager Gifts | 0.004 | 0.314 | 10.173 |
| Beer/Lager Gifts | Spirits & Fortified | 0.013 | 0.427 | 9.897 |
| Wine Gifts | Spirits & Fortified | 0.006 | 0.412 | 9.537 |
| Red Wine Bxes & 25Cl | White Boxes | 0.015 | 0.474 | 9.344 |
| French White Rhone | French Red | 0.003 | 0.480 | 8.691 |
| Small Sizeswhite Oth | Small Sizes White | 0.005 | 0.559 | 8.340 |
| Small Sizes Red | Small Sizes White | 0.025 | 0.486 | 7.258 |
| French White Loire | French White South | 0.004 | 0.349 | 6.763 |
| French White Rhone | French White 2 | 0.005 | 0.760 | 6.661 |
| Small Sizeswhite Oth | Small Sizes Red | 0.003 | 0.324 | 6.306 |
| Small Sizes Wht Othr | Small Sizes White | 0.003 | 0.414 | 6.176 |

<br>
Interestingly, the strongest relationship existed between two products labelled as "gifts" - this was useful information for the category managers as it suggested that gifts should be available in the same section of the aisle, if that was not already the case.

There was also some strong relationships between French wines, which suggested that wines should be arranged by country, rather than by type, to make it easier for customers to find what they are after. 

Another interesting association was between products labelled "small".  It was possible that these were being bought by customers doing a light shop with a basket, rather than a trolley, so I suggested that this be looked at more closely by category managers, in case they felt it was worth placing these items nearer to the checkout so that they were easier to carry.

<br>
#### Search Tool For Category Managers

With the data now stored as a DataFrame, we will also go back to the client with a proposal to build a simple "search" tool for Category Managers to use.

An example of how this might work would be to test a hypothesis around New Zealand wines.

The code below uses a string function to pull back all rows in the DataFrame where *product1* contains the words "New Zealand"

```python

# search based upon text
apriori_rules_df[apriori_rules_df["product1"].str.contains("New Zealand")]

```
<br>
The results of this search, in order of descending Lift, are as follows:
<br>
<br>

| **product1** | **product2** | **support** | **confidence** | **lift** |
|---|---|---|---|---|
| New Zealand Red | Malt Whisky | 0.005326605 | 0.271428571 | 5.628986711 |
| New Zealand Red | Iberia White | 0.007289038 | 0.371428571 | 4.616326531 |
| New Zealand Red | New Zealand White | 0.012615643 | 0.642857143 | 4.613825812 |
| New Zealand Red | French White South | 0.004485562 | 0.228571429 | 4.431055901 |
| New Zealand Red | French White 2 | 0.009531819 | 0.485714286 | 4.256862057 |
| New Zealand Red | French Red | 0.004205214 | 0.214285714 | 3.879985497 |
| New Zealand Red | French Red South | 0.006447996 | 0.328571429 | 3.868033946 |
| New Zealand Red | South America | 0.010933558 | 0.557142857 | 3.799863425 |
| New Zealand Red | Other Red | 0.004485562 | 0.228571429 | 3.591692889 |
| New Zealand Red | Iberia | 0.012054948 | 0.614285714 | 3.528433402 |
| New Zealand Red | Champagne | 0.008690777 | 0.442857143 | 3.526052296 |
| New Zealand White | South America White | 0.049341183 | 0.354124748 | 3.423205902 |
| New Zealand Red | French Red 2 | 0.010092515 | 0.514285714 | 3.359811617 |
| New Zealand Red | South America White | 0.006728343 | 0.342857143 | 3.314285714 |
| New Zealand Red | Australia White | 0.007289038 | 0.371428571 | 3.215742025 |

<br>
There appears to be *some* relationship between New Zealand wines and other New Zealand wines, but what is also interesting is that New Zealand wines seem to be more associated with French and South American wines than they are with Australian Wines.

New Zealand and Australia are often grouped together, but in terms of wine this wouldn't make sense - perhaps because of the different climates the wines are very different and thus it wouldn't make sense to group wines by geographical proximity, but by preference instead.  This is only a hypothesis for now - we will need to take this back to the client and get their category experts to help us interpret it!

___
<br>
# Growth & Next Steps <a name="growth-next-steps"></a>

As this was first and foremost an exploratory project, we will take back the results to the client Category Managers and discuss the results, our views on how these insights can be actioned best, and any considerations that need to be taken into account when interpreting.

From there, we will recommend applying this same logic to all other categories, as well as potentially across the full-product range.

We will also propose the build of the "Keyword Search Engine" which will help Category Managers extract and utilise the insights held within the data.
