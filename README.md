
# Heroes Of  Pymoli Data Analysis

-  Trend 1: Males play and purchase the most. 
-  Trend 2: Ages 15-29 play and purchase the most. 
-  Trend 3: Most profitable item doesn't always mean it's the most popular. 


```python
# Dependencies
import pandas as pd
import os

# Import the JSON file
file_path = os.path.join("raw_data", "purchase_data.json")

# Read it into pandas dataframe
purchase_data_df = pd.read_json(file_path)
#purchase_data_df.head()
```

## Player Count


```python
# Find the total number of players
# Use .nunique() over len() since some players may have purchased more than once
players_count = purchase_data_df["SN"].nunique()
#players_count

# Create a DataFrame to organize the data
players_count_df = pd.DataFrame({
    "Total Players": [players_count]
})
players_count_df
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Total)


```python
# Find the number of unique items
unique_items = purchase_data_df["Item ID"].nunique()
#unique_items

# Find the average purchase price
average_purchase_price = purchase_data_df["Price"].mean()
#average_purchase_price

# Find the total number of purchases
purchases_count = len(purchase_data_df)
#purchases_count

# Find the total revenue
total_revenue = purchase_data_df["Price"].sum()
#total_revenue

# Create a DataFrame to organize the data
purchasing_analysis_df = pd.DataFrame({
    "Number of Unique Items": [unique_items], 
    "Average Price": [average_purchase_price], 
    "Number of Purchases": [purchases_count], 
    "Total Revenue": [total_revenue]
}, columns=["Number of Unique Items", "Average Price", "Number of Purchases", "Total Revenue"])
#purchasing_analysis_df

# Use mapping to clean the $ columns 
purchasing_analysis_df["Average Price"] = purchasing_analysis_df["Average Price"].map("${:.2f}".format)
purchasing_analysis_df["Total Revenue"] = purchasing_analysis_df["Total Revenue"].map("${:.2f}".format)
purchasing_analysis_df
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Unique Items</th>
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>183</td>
      <td>$2.93</td>
      <td>780</td>
      <td>$2286.33</td>
    </tr>
  </tbody>
</table>
</div>



## Gender Demographics


```python
# Need to single out the players since the same player can purchase multiple times
organized_purchase_data = purchase_data_df[["Age", "Gender", "SN"]]
purchase_data_no_duplicates = organized_purchase_data.drop_duplicates()
#purchase_data_no_duplicates.count()

# Can groupby by Gender to calculate just that column 
# Find the count for each gender (male, female, other/non-disclosed)
gender_groupby = purchase_data_no_duplicates.groupby("Gender")
gender_count = gender_groupby["Gender"].count()
#gender_count

# Fid the percentage count for each gender
gender_percentage = gender_groupby["Gender"].count() / players_count * 100
#gender_percentage

# Create a DataFrame to organize the data
# Sort index so male is up top
gender_demographics = pd.DataFrame({
    "Total Count": gender_count, 
    "Percentage of Players": gender_percentage
}).sort_values(["Total Count"], ascending=False)
#gender_demographics

# Use mapping to clean the % columns 
gender_demographics["Percentage of Players"] = gender_demographics["Percentage of Players"].map("{:.2f}%".format)
gender_demographics
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>81.15%</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>17.45%</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.40%</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Gender)


```python
# Use the original data since we're looking at all purchases
# Can groupby by Gender to calculate just that column 
gender_purchase_groupby = purchase_data_df.groupby("Gender")

# Find the count for each gender (male, female, other/non-disclosed)
gender_purchase_count = gender_purchase_groupby["Gender"].count()
#gender_purchase_count

# Find the average purchase price for each gender
average_purchase_gender = gender_purchase_groupby["Price"].mean()
#average_purchase_gender

# Find the total purchase amount for each gender
total_purchase_gender = gender_purchase_groupby["Price"].sum()
#total_purchase_gender

# Find the normalized totals for each gender
normalized_total_gender = (total_purchase_gender / gender_purchase_count) 
normalized_total_gender

# Create a DataFrame to organize the data
# Sort index so male is up top
purchasing_gender_df = pd.DataFrame({ 
    "Purchase Count": gender_purchase_count, 
    "Average Purchase Price": average_purchase_gender, 
    "Total Purchase Value": total_purchase_gender, 
    "Normalized Totals": normalized_total_gender
},
columns=["Purchase Count", "Average Purchase Price", "Total Purchase Value", "Normalized Totals"]).sort_values(["Purchase Count"], ascending=False)
#purchasing_gender_df

# Use mapping to clean the $ columns 
purchasing_gender_df["Average Purchase Price"] = purchasing_gender_df["Average Purchase Price"].map("${:.2f}".format)
purchasing_gender_df["Total Purchase Value"] = purchasing_gender_df["Total Purchase Value"].map("${:.2f}".format)
purchasing_gender_df["Normalized Totals"] = purchasing_gender_df["Normalized Totals"].map("${:.2f}".format)
purchasing_gender_df
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>$2.95</td>
      <td>$1867.68</td>
      <td>$2.95</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>$2.82</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$3.25</td>
    </tr>
  </tbody>
</table>
</div>



## Age Demographics


```python
# Need to use the data with no duplicates so players aren't counted more than once
#purchase_data_no_duplicates.count()

# Need to create bins for the age groups first
bins = [0, 10, 15, 20, 25, 30, 35, 40, 100]
group_names = ["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"]
age_bins = pd.cut(purchase_data_no_duplicates["Age"], bins, labels=group_names, right=False)
#age_bins

# Find the count for each age group
age_count = age_bins.value_counts()
#age_count

# Find the percentage for each age group 
age_percent = age_count / players_count * 100
#age_percent

# Create DataFrames to organize the data
# Need to sort by the indexes or the #s will be greatest to least based on count
age_demographics = pd.DataFrame({
    "Total Counts": age_count, 
    "Percentage of Players": age_percent
}).sort_index()
#age_demographics

# Use mapping to clean the % columns 
age_demographics["Percentage of Players"] = age_demographics["Percentage of Players"].map("{:.2f}%".format)
age_demographics
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Counts</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>3.32%</td>
      <td>19</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>4.01%</td>
      <td>23</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>17.45%</td>
      <td>100</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>45.20%</td>
      <td>259</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>15.18%</td>
      <td>87</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>8.20%</td>
      <td>47</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>4.71%</td>
      <td>27</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>1.92%</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Age)


```python
# Use from original data since we're looking at all purchases 
# Need to create bins for the age groups first (original data)
bins2 = [0, 10, 15, 20, 25, 30, 35, 40, 100]
group_names2 = ["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"]
age_bins2 = pd.cut(purchase_data_df["Age"], bins, labels=group_names, right=False)
#age_bins2

# Make a copy of the original data first to not override the original
purchase_data_copy = purchase_data_df.copy()

# Replace the age column by the bins made to filter and calculate
purchase_data_copy["Age"] = age_bins2
#purchase_data_copy.head()

# Can groupby by Age to calculate just that column 
# Find the count for each age group 
age_purchase_groupby = purchase_data_copy.groupby("Age")
age_purchase_count = age_purchase_groupby["Gender"].count()
#age_purchase_count

# Find the average purchase price for each age group
average_purchase_age = age_purchase_groupby["Price"].mean()
#average_purchase_age

# Find the total purchase amount for each age group
total_purchase_age = age_purchase_groupby["Price"].sum()
#total_purchase_age

# Find the normalized totals for each age group
normalized_total_age = (total_purchase_age / age_purchase_count) 
#normalized_total_age

# Create a DataFrame to organize the data
purchasing_age_df = pd.DataFrame({ 
    "Purchase Count": age_purchase_count, 
    "Average Purchase Price": average_purchase_age, 
    "Total Purchase Value": total_purchase_age, 
    "Normalized Totals": normalized_total_age
},
columns=["Purchase Count", "Average Purchase Price", "Total Purchase Value", "Normalized Totals"])
#purchasing_age_df

# Use mapping to clean the $ columns 
purchasing_age_df["Average Purchase Price"] = purchasing_age_df["Average Purchase Price"].map("${:.2f}".format)
purchasing_age_df["Total Purchase Value"] = purchasing_age_df["Total Purchase Value"].map("${:.2f}".format)
purchasing_age_df["Normalized Totals"] = purchasing_age_df["Normalized Totals"].map("${:.2f}".format)
purchasing_age_df
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Age</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>28</td>
      <td>$2.98</td>
      <td>$83.46</td>
      <td>$2.98</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>35</td>
      <td>$2.77</td>
      <td>$96.95</td>
      <td>$2.77</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>133</td>
      <td>$2.91</td>
      <td>$386.42</td>
      <td>$2.91</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>336</td>
      <td>$2.91</td>
      <td>$978.77</td>
      <td>$2.91</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>125</td>
      <td>$2.96</td>
      <td>$370.33</td>
      <td>$2.96</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>64</td>
      <td>$3.08</td>
      <td>$197.25</td>
      <td>$3.08</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>42</td>
      <td>$2.84</td>
      <td>$119.40</td>
      <td>$2.84</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>17</td>
      <td>$3.16</td>
      <td>$53.75</td>
      <td>$3.16</td>
    </tr>
  </tbody>
</table>
</div>



## Top Spenders


```python
# Use groupby to separate the data according to "SN" values
groupby_sn = purchase_data_df.groupby(["SN"])

# Need to use a data function to view the groupby object
#groupby_sn.count().head()

# Get the purchase count, average purchase price, and total purchase value 
sn_price_count = groupby_sn["Price"].count()
#print(sn_price_count.head())
sn_price_mean = groupby_sn["Price"].mean()
#print(sn_price_mean.head())
sn_price_sum = groupby_sn["Price"].sum()
#print(sn_price_sum.head())

top_spenders = pd.DataFrame({
    "Purchase Count": sn_price_count, 
    "Average Purchase Price":sn_price_mean, 
    "Total Purchase Value": sn_price_sum
},
columns=["Purchase Count", "Average Purchase Price", "Total Purchase Value"])

top_spenders = top_spenders.sort_values(["Total Purchase Value"], ascending=False).head().round(2)

top_spenders["Average Purchase Price"] = top_spenders["Average Purchase Price"].map("${:.2f}".format)
top_spenders["Total Purchase Value"] = top_spenders["Total Purchase Value"].map("${:.2f}".format)

top_spenders.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>5</td>
      <td>$3.41</td>
      <td>$17.06</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>$3.39</td>
      <td>$13.56</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>$3.18</td>
      <td>$12.74</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>$4.24</td>
      <td>$12.73</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>$3.86</td>
      <td>$11.58</td>
    </tr>
  </tbody>
</table>
</div>



## Most Popular Items


```python
# Use groupby to separate the data according to "Item ID" and "Item Name" values
groupby_item = purchase_data_df.groupby(["Item ID", "Item Name"])

# Need to use a data function to view the groupby object
#groupby_item.count().head()

# Get the purchase count, item price, and total purchase value 
item_price_count = groupby_item["Price"].count()
#print(item_price_count.head())
item_price = groupby_item["Price"].mean()
#print(item_price.head())
item_price_sum = groupby_item["Price"].sum()
#print(item_price_sum.head())

# Create a DataFrame to organize the data
most_popular = pd.DataFrame({
    "Purchase Count": item_price_count, 
    "Item Price":item_price, 
    "Total Purchase Value": item_price_sum
},
columns=["Purchase Count", "Item Price", "Total Purchase Value"])

# Sort by purchase count in descending order to see the top 5 items that were the most purchased
most_popular = most_popular.sort_values(["Purchase Count"], ascending=False)

# Using mapping to format columns in $ 
most_popular["Item Price"] = most_popular["Item Price"].map("${:.2f}".format)
most_popular["Total Purchase Value"] = most_popular["Total Purchase Value"].map("${:.2f}".format)

most_popular.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>11</td>
      <td>$2.35</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>11</td>
      <td>$2.23</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>9</td>
      <td>$2.07</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <th>Woeful Adamantite Claymore</th>
      <td>9</td>
      <td>$1.24</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <td>9</td>
      <td>$1.49</td>
      <td>$13.41</td>
    </tr>
  </tbody>
</table>
</div>



## Most Profitable Items


```python
# Create a DataFrame to organize the data
most_profitable = pd.DataFrame({
    "Purchase Count": item_price_count, 
    "Item Price":item_price, 
    "Total Purchase Value": item_price_sum
},
columns=["Purchase Count", "Item Price", "Total Purchase Value"])

# Sort by purchase count in descending order to see the top 5 items that were the most purchased
most_profitable = most_profitable.sort_values(["Total Purchase Value"], ascending=False)

# Using mapping to format columns in $ 
most_profitable["Item Price"] = most_profitable["Item Price"].map("${:.2f}".format)
most_profitable["Total Purchase Value"] = most_profitable["Total Purchase Value"].map("${:.2f}".format)

most_profitable.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>7</td>
      <td>$4.25</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>6</td>
      <td>$4.95</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>6</td>
      <td>$4.87</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>8</td>
      <td>$3.61</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>
