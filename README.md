
# Heroes Of Pymoli Data Analysis (Solved)
* Observed trend #1 - Of the 573 people in the dataset, the vast majority are male (81.15%). There also exists, a smaller, but notable proportion of female players (17.45%).

* Observed trend #2 - Our peak purchase over age demographics is in the age group of '20-24' which has 336 purchase count and $978.77 of total purchase value.

* Observed trend #3 - The total purchase value of Male (1,867.68 dollars) is more than female (382.91 dollars).  
-----


```python
import pandas as pd
json_file = "purchase_data.json"
df = pd.read_json(json_file)
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Male</td>
      <td>165</td>
      <td>Bone Crushing Silver Skewer</td>
      <td>3.37</td>
      <td>Aelalis34</td>
    </tr>
    <tr>
      <th>1</th>
      <td>21</td>
      <td>Male</td>
      <td>119</td>
      <td>Stormbringer, Dark Blade of Ending Misery</td>
      <td>2.32</td>
      <td>Eolo46</td>
    </tr>
    <tr>
      <th>2</th>
      <td>34</td>
      <td>Male</td>
      <td>174</td>
      <td>Primitive Blade</td>
      <td>2.46</td>
      <td>Assastnya25</td>
    </tr>
    <tr>
      <th>3</th>
      <td>21</td>
      <td>Male</td>
      <td>92</td>
      <td>Final Critic</td>
      <td>1.36</td>
      <td>Pheusrical25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>23</td>
      <td>Male</td>
      <td>63</td>
      <td>Stormfury Mace</td>
      <td>1.27</td>
      <td>Aela59</td>
    </tr>
  </tbody>
</table>
</div>



## Player Count


```python
total_players = df['SN'].unique()
total_players_count = total_players.size
player_count_df = pd.DataFrame([{'Total Players': total_players_count}])
# df w/ duplicates removed
removed_duplicates_df = df.drop_duplicates(['SN'], keep='first')
player_count_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
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
# format
money_fmt = "${:,.2f}".format
fmt = '{:.2f}'.format

unique_items = df['Item ID'].unique()
unique_items_count = unique_items.size
average_price = df['Price'].sum()/df['Price'].count()
purchases_count = df['Item ID'].count()
total_revenue = df['Price'].sum()

purchase_analysis_df = pd.DataFrame([[unique_items_count,money_fmt(average_price), purchases_count, money_fmt(total_revenue)]],
                                     columns=['Number of Unique Items','Average Price','Number of Purchases','Total Revenue'])
# display
purchase_analysis_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
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
      <td>$2,286.33</td>
    </tr>
  </tbody>
</table>
</div>



## Gender Demographics


```python
# use the built-in normalize in value_counts to get the percentage
percents = removed_duplicates_df['Gender'].value_counts(normalize=True)*100
# total count
total = removed_duplicates_df['Gender'].value_counts()

gender_demographics =total.to_frame()
gender_demographics= gender_demographics.rename(columns={'Gender':'Total Count'})
gender_demographics['Percentage of Player'] = percents.map(fmt)

# change the column order and display the df
gender_demographics = gender_demographics[['Percentage of Player','Total Count']]
gender_demographics
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Player</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>81.15</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.40</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>




## Purchasing Analysis (Gender)


```python
# group by gender 
group_by_gender = df.groupby(['Gender'])
purchase_analysis_df = pd.DataFrame()
# purchase count
purchase_analysis_df['Purchase Count']=group_by_gender['Item ID'].count()
# total purchase value
purchase_analysis_df['Total Purchase Value'] = group_by_gender['Price'].sum()
# average purchase price
purchase_analysis_df['Average Purchase Price'] = purchase_analysis_df['Total Purchase Value']/purchase_analysis_df['Purchase Count']
# normalization
purchase_analysis_df['Normalized Totals'] = purchase_analysis_df['Total Purchase Value']/gender_demographics['Total Count']

# formatting
purchase_analysis_df['Total Purchase Value'] = purchase_analysis_df['Total Purchase Value'].map(money_fmt)
purchase_analysis_df['Average Purchase Price'] = purchase_analysis_df['Average Purchase Price'].map(money_fmt)
purchase_analysis_df['Normalized Totals'] = purchase_analysis_df['Normalized Totals'].map(money_fmt)

# change the column order and display
purchase_analysis_df = purchase_analysis_df[['Purchase Count', 'Average Purchase Price', \
                                             'Total Purchase Value','Normalized Totals']]
purchase_analysis_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
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
      <th>Female</th>
      <td>136</td>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>$3.83</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>$2.95</td>
      <td>$1,867.68</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$4.47</td>
    </tr>
  </tbody>
</table>
</div>



## Age Demographics


```python
max_age = removed_duplicates_df['Age'].max()
# create bins and categories
bins = [0,9,14,19,24,29,34,39,max_age]
age_categories = ['<10','10-14','15-19','20-24','25-29','30-34','35-39','40+']
# cerate a df for age demographics
age_demographics_df = pd.DataFrame()
# categorize
categorized_by_age_df = pd.cut(removed_duplicates_df['Age'], bins, labels=age_categories, right=True)

# add total count that is sorted by the index to the df
age_demographics_df['Total Count'] = categorized_by_age_df.value_counts().sort_index()
# percent and formatting
age_demographics_df['Percentage of Players'] = (categorized_by_age_df.value_counts(normalize=True)*100).map(fmt)

# change the column order and display
age_demographics_df = age_demographics_df[['Percentage of Players', 'Total Count']]
age_demographics_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>3.32</td>
      <td>19</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>4.01</td>
      <td>23</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>45.20</td>
      <td>259</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>15.18</td>
      <td>87</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>8.20</td>
      <td>47</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>4.71</td>
      <td>27</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>1.92</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Age)


```python
# create a df for this portion
purchase_analysis_df = pd.DataFrame()

max_age_in_duplicates = df['Age'].max()
# create new bins for this part
bins_in_duplicates = [0,9,14,19,24,29,34,39,max_age_in_duplicates]
# reuse the age category
age_categories_in_duplicates = age_categories

# categorize 
categorized_by_age_in_duplicates_df = pd.cut(df['Age'],bins=bins_in_duplicates, labels=age_categories_in_duplicates, right=True)

# purchase count
purchase_analysis_df['Purchase Count'] = categorized_by_age_in_duplicates_df.value_counts().sort_index()

# total purchase value by age categories
purchase_analysis_df['Total Purchase Value'] = df.groupby(categorized_by_age_in_duplicates_df)['Price'].sum()

# average purchase price
purchase_analysis_df['Average Purchase Price'] = purchase_analysis_df['Total Purchase Value']/purchase_analysis_df['Purchase Count']

# normalized totals by age demographics
purchase_analysis_df['Normalized Totals'] = purchase_analysis_df['Total Purchase Value']/age_demographics_df['Total Count']

# formatting
purchase_analysis_df['Total Purchase Value'] = purchase_analysis_df['Total Purchase Value'].map(money_fmt)
purchase_analysis_df['Average Purchase Price'] = purchase_analysis_df['Average Purchase Price'].map(money_fmt)
purchase_analysis_df['Normalized Totals'] = purchase_analysis_df['Normalized Totals'].map(money_fmt)


# change the column order and display
purchase_analysis_df = purchase_analysis_df[['Purchase Count','Average Purchase Price', \
                                            'Total Purchase Value','Normalized Totals']]
purchase_analysis_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>28</td>
      <td>$2.98</td>
      <td>$83.46</td>
      <td>$4.39</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>35</td>
      <td>$2.77</td>
      <td>$96.95</td>
      <td>$4.22</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>133</td>
      <td>$2.91</td>
      <td>$386.42</td>
      <td>$3.86</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>336</td>
      <td>$2.91</td>
      <td>$978.77</td>
      <td>$3.78</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>125</td>
      <td>$2.96</td>
      <td>$370.33</td>
      <td>$4.26</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>64</td>
      <td>$3.08</td>
      <td>$197.25</td>
      <td>$4.20</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>42</td>
      <td>$2.84</td>
      <td>$119.40</td>
      <td>$4.42</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>17</td>
      <td>$3.16</td>
      <td>$53.75</td>
      <td>$4.89</td>
    </tr>
  </tbody>
</table>
</div>



## Top Spenders


```python
# create a new df
top_spenders_df = pd.DataFrame()
# group by SN
group_by_SN_df = df.groupby(['SN'])

# total purchase value
total_purchase_value = group_by_SN_df['Price'].sum().sort_values(ascending=False).head(5)
top_spenders_df['Total Purchase Value']  = total_purchase_value

# purchase count
top_total_purchase_sns =df[df['SN'].isin(total_purchase_value.index)]
group_by_sns =top_total_purchase_sns.groupby(['SN'])
purchase_count = group_by_sns['Item ID'].count()
top_spenders_df['Purchase Count'] = purchase_count

# average purchase price
top_spenders_df['Average Purchase Price'] = total_purchase_value/purchase_count

# formatting
top_spenders_df['Average Purchase Price'] = top_spenders_df['Average Purchase Price'].map(money_fmt)
top_spenders_df['Total Purchase Value']  = top_spenders_df['Total Purchase Value'] .map(money_fmt)

# change the column order and display
top_spenders_df = top_spenders_df[['Purchase Count','Average Purchase Price','Total Purchase Value']]
top_spenders_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
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
group_by_id_and_name = df.groupby(['Item ID','Item Name'])
# create a new df for this portion of code
most_popular_items = pd.DataFrame()
# purchase count
purchase_count = group_by_id_and_name.count().sort_values('Price',ascending=False)['SN']
most_popular_items['Purchase Count'] = purchase_count

# total purchase value
top_total_purchase_sns =df[df['Item ID'].isin(purchase_count.index.get_level_values(0))]
group_by_sns =top_total_purchase_sns.groupby(['Item ID', 'Item Name'])
total_purchase_value = group_by_sns['Price'].sum()

# item price
item_price = total_purchase_value/purchase_count

# formatting
most_popular_items['Item Price'] = item_price.map(money_fmt)
most_popular_items['Total Purchase Value'] = total_purchase_value.map(money_fmt)

# display top 5
most_popular_items.head(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
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
# create a new df
most_profitable_items = pd.DataFrame()

# purchase count
top_total_purchase_sns =df[df['Item ID'].isin(most_profitable_total_purchase_value.index.get_level_values(0))] # 0 --> Item ID
group_by_sns =top_total_purchase_sns.groupby(['Item ID', 'Item Name'])
purchase_count = group_by_sns['Item ID'].count()

# total purchase value
most_profitable_total_purchase_value = group_by_id_and_name['Price'].sum().sort_values(ascending=False)
most_profitable_items['Total Purchase Value'] = most_profitable_total_purchase_value

# item price
most_profitable_items['Item Price'] = most_profitable_total_purchase_value/purchase_count

# formatting
most_profitable_items['Purchase Count'] = purchase_count
most_profitable_items['Item Price'] = most_profitable_items['Item Price'].map(money_fmt)
most_profitable_items['Total Purchase Value'] = most_profitable_items['Total Purchase Value'].map(money_fmt)

# change the column order and display the top 5
most_profitable_items = most_profitable_items[['Purchase Count','Item Price','Total Purchase Value']]
most_profitable_items.head(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
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


