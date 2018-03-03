#PANDAS
#HEROES OF PYMOLI

```python
import pandas as pd
import numpy as np
import os
import json

filepath1 = os.path.join('resources', 'purchase_raw_data','purchase_data_1.json')
filepath2 = os.path.join('resources', 'purchase_raw_data', 'purchase_data_2.json')
pd1 = pd.read_json(filepath1)
pd2 = pd.read_json(filepath2)
pur_data = pd.merge(pd1,pd2, how='outer')


pur_data.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
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




```python
# PLAYER COUNT 
player_count = len(pur_data['SN'].unique())

players_df = pd.DataFrame([{'Total Players': player_count}])

players_df.set_index('Total Players', inplace = True)

players_df

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
    </tr>
    <tr>
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>612</th>
    </tr>
  </tbody>
</table>
</div>




```python
# PURCHASING ANALYSIS (TOTAL)

#code for inspecting data
#pur_data['Item ID'].value_counts()
#unique_items = pd.DataFrame(pur_data['Item ID'].unique())
#len(unique_items)

#creates a df but only keeping last occurance of Item ID
no_dup_items = pur_data.drop_duplicates(['Item ID'], keep = 'last')
#counts items by unique ID
total_unique = len(no_dup_items)
#finds the number of total purchases by counting occurances of price
total_pur = pur_data['Price'].count()
#calculates total revenue for table by summing occurance of price and below calc
total_rev = round(pur_data['Price'].sum(),2)
#calculates total_rev
avg_price = round(total_rev/total_pur, 2)

#creates Purchase Analysis DataFrame
pur_analysis = pd.DataFrame([{
    
    'Number of Unique Items': total_unique,
    'Average Purchase Price': avg_price,
    'Total Purchases': total_pur,
    'Total Revenue': total_rev
}])

pur_analysis.style.format({'Average Purchase Price': '$ {:.2f}', 'Total Revenue': '$ {:,.2f}'})

```




<style  type="text/css" >
</style>  
<table id="T_db5d7c74_1ed8_11e8_a0b9_78e3b55b6715" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Average Purchase Price</th> 
        <th class="col_heading level0 col1" >Number of Unique Items</th> 
        <th class="col_heading level0 col2" >Total Purchases</th> 
        <th class="col_heading level0 col3" >Total Revenue</th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_db5d7c74_1ed8_11e8_a0b9_78e3b55b6715level0_row0" class="row_heading level0 row0" >0</th> 
        <td id="T_db5d7c74_1ed8_11e8_a0b9_78e3b55b6715row0_col0" class="data row0 col0" >$ 2.93</td> 
        <td id="T_db5d7c74_1ed8_11e8_a0b9_78e3b55b6715row0_col1" class="data row0 col1" >184</td> 
        <td id="T_db5d7c74_1ed8_11e8_a0b9_78e3b55b6715row0_col2" class="data row0 col2" >858</td> 
        <td id="T_db5d7c74_1ed8_11e8_a0b9_78e3b55b6715row0_col3" class="data row0 col3" >$ 2,514.43</td> 
    </tr></tbody> 
</table> 




```python
# GENDER DEMOGRAPHICS

# Percentage and Count of Male Players
# Percentage and Count of Female Players
# Percentage and Count of Other / Non-Disclosed

#creates df of unique player names by only keeping the last occurance
no_dup_players = pur_data.drop_duplicates(['SN'], keep ='last')

#counts gender values from the df with no duplicate screen names
gender_counts = no_dup_players['Gender'].value_counts().reset_index()
#adds column for % of players using player count from first table and gender_count 
#column which is a count from line above
gender_counts['% of Players'] = gender_counts['Gender']/player_count * 100
#renames columns
gender_counts.rename(columns = {'index': 'Gender', 'Gender': '# of Players'}, inplace = True)
#sets index as Gender for aesthetics 
gender_counts.set_index(['Gender'], inplace = True)
#just checking percents sum to 100%
#gender_counts['% of Players'].sum()
#formats table
gender_counts.style.format({"% of Players": "{:.1f}%"})
```




<style  type="text/css" >
</style>  
<table id="T_db66cb66_1ed8_11e8_bf8c_78e3b55b6715" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" ># of Players</th> 
        <th class="col_heading level0 col1" >% of Players</th> 
    </tr>    <tr> 
        <th class="index_name level0" >Gender</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_db66cb66_1ed8_11e8_bf8c_78e3b55b6715level0_row0" class="row_heading level0 row0" >Male</th> 
        <td id="T_db66cb66_1ed8_11e8_bf8c_78e3b55b6715row0_col0" class="data row0 col0" >494</td> 
        <td id="T_db66cb66_1ed8_11e8_bf8c_78e3b55b6715row0_col1" class="data row0 col1" >80.7%</td> 
    </tr>    <tr> 
        <th id="T_db66cb66_1ed8_11e8_bf8c_78e3b55b6715level0_row1" class="row_heading level0 row1" >Female</th> 
        <td id="T_db66cb66_1ed8_11e8_bf8c_78e3b55b6715row1_col0" class="data row1 col0" >109</td> 
        <td id="T_db66cb66_1ed8_11e8_bf8c_78e3b55b6715row1_col1" class="data row1 col1" >17.8%</td> 
    </tr>    <tr> 
        <th id="T_db66cb66_1ed8_11e8_bf8c_78e3b55b6715level0_row2" class="row_heading level0 row2" >Other / Non-Disclosed</th> 
        <td id="T_db66cb66_1ed8_11e8_bf8c_78e3b55b6715row2_col0" class="data row2 col0" >9</td> 
        <td id="T_db66cb66_1ed8_11e8_bf8c_78e3b55b6715row2_col1" class="data row2 col1" >1.5%</td> 
    </tr></tbody> 
</table> 




```python
# PURCHASING ANALYSIS (GENDER)

# The below each broken by gender
# Purchase Count
# Average Purchase Price
# Total Purchase Value
# Normalized Totals

# counts purchases by gender
pur_count_by_gen = pd.DataFrame(pur_data.groupby('Gender')['Gender'].count())
# sums price by gender
total_pur_by_gen = pd.DataFrame(pur_data.groupby('Gender')['Price'].sum())
#merges the two data frames from above
pur_analysis_gen = pd.merge(pur_count_by_gen, total_pur_by_gen, left_index = True, right_index = True)
#renames columns
pur_analysis_gen.rename(columns = {'Gender': '# of Purchases', 'Price':'Total Purchase Value'}, inplace=True)
#adds column for average purchase price by gender by dividing total purcahse value by gender by # of purchases by gender
pur_analysis_gen['Average Purchase Price'] = pur_analysis_gen['Total Purchase Value']/pur_analysis_gen['# of Purchases']
#merges gender counts from above table (excluding dup SNs) into current df 
pur_analysis_gen = pur_analysis_gen.merge(gender_counts, left_index = True, right_index = True)
# calculates and adds normalized total column by dividing total purchase value by unique # of players by genger
pur_analysis_gen['Normalized Totals'] = pur_analysis_gen['Total Purchase Value']/pur_analysis_gen['# of Players']
pur_analysis_gen
#deletes columns not needed for table (# of Players was used for normalized totals while % of players came from gender count table)
del pur_analysis_gen['% of Players']
del pur_analysis_gen['# of Players']
# #resets index for aesthetics 
# # pur_analysis_gen.set_index('Gender', inplace=True)
# #formats table
pur_analysis_gen.style.format({'Total Purchase Value': '${:.2f}', 'Average Purchase Price': '${:.2f}', 'Normalized Totals': '${:.2f}'})
```




<style  type="text/css" >
</style>  
<table id="T_db7598ae_1ed8_11e8_936e_78e3b55b6715" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" ># of Purchases</th> 
        <th class="col_heading level0 col1" >Total Purchase Value</th> 
        <th class="col_heading level0 col2" >Average Purchase Price</th> 
        <th class="col_heading level0 col3" >Normalized Totals</th> 
    </tr>    <tr> 
        <th class="index_name level0" >Gender</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_db7598ae_1ed8_11e8_936e_78e3b55b6715level0_row0" class="row_heading level0 row0" >Female</th> 
        <td id="T_db7598ae_1ed8_11e8_936e_78e3b55b6715row0_col0" class="data row0 col0" >149</td> 
        <td id="T_db7598ae_1ed8_11e8_936e_78e3b55b6715row0_col1" class="data row0 col1" >$424.29</td> 
        <td id="T_db7598ae_1ed8_11e8_936e_78e3b55b6715row0_col2" class="data row0 col2" >$2.85</td> 
        <td id="T_db7598ae_1ed8_11e8_936e_78e3b55b6715row0_col3" class="data row0 col3" >$3.89</td> 
    </tr>    <tr> 
        <th id="T_db7598ae_1ed8_11e8_936e_78e3b55b6715level0_row1" class="row_heading level0 row1" >Male</th> 
        <td id="T_db7598ae_1ed8_11e8_936e_78e3b55b6715row1_col0" class="data row1 col0" >697</td> 
        <td id="T_db7598ae_1ed8_11e8_936e_78e3b55b6715row1_col1" class="data row1 col1" >$2052.28</td> 
        <td id="T_db7598ae_1ed8_11e8_936e_78e3b55b6715row1_col2" class="data row1 col2" >$2.94</td> 
        <td id="T_db7598ae_1ed8_11e8_936e_78e3b55b6715row1_col3" class="data row1 col3" >$4.15</td> 
    </tr>    <tr> 
        <th id="T_db7598ae_1ed8_11e8_936e_78e3b55b6715level0_row2" class="row_heading level0 row2" >Other / Non-Disclosed</th> 
        <td id="T_db7598ae_1ed8_11e8_936e_78e3b55b6715row2_col0" class="data row2 col0" >12</td> 
        <td id="T_db7598ae_1ed8_11e8_936e_78e3b55b6715row2_col1" class="data row2 col1" >$37.86</td> 
        <td id="T_db7598ae_1ed8_11e8_936e_78e3b55b6715row2_col2" class="data row2 col2" >$3.15</td> 
        <td id="T_db7598ae_1ed8_11e8_936e_78e3b55b6715row2_col3" class="data row2 col3" >$4.21</td> 
    </tr></tbody> 
</table> 




```python
# AGE DEMOGRAPHICS

# The below each broken into bins of 4 years (i.e. <10, 10-14, 15-19, etc.)
# Purchase Count
# Average Purchase Price
# Total Purchase Value
# Normalized Totals

#creates a column 'age_bin' based on conditional of age range
pur_data.loc[(pur_data['Age'] < 10), 'age_bin'] = "< 10"
pur_data.loc[(pur_data['Age'] >= 10) & (pur_data['Age'] <= 14), 'age_bin'] = "10 - 14"
pur_data.loc[(pur_data['Age'] >= 15) & (pur_data['Age'] <= 19), 'age_bin'] = "15 - 19"
pur_data.loc[(pur_data['Age'] >= 20) & (pur_data['Age'] <= 24), 'age_bin'] = "20 - 24"
pur_data.loc[(pur_data['Age'] >= 25) & (pur_data['Age'] <= 29), 'age_bin'] = "25 - 29"
pur_data.loc[(pur_data['Age'] >= 30) & (pur_data['Age'] <= 34), 'age_bin'] = "30 - 34"
pur_data.loc[(pur_data['Age'] >= 35) & (pur_data['Age'] <= 39), 'age_bin'] = "35 - 39"
pur_data.loc[(pur_data['Age'] >= 40), 'age_bin'] = "> 40"
#double checked count
# pur_data[['age_bin', 'Age']].count()

# counts purchases by age bin by counting screen names (non-unique)
pur_count_age = pd.DataFrame(pur_data.groupby('age_bin')['SN'].count())
#finds avg price of purchases by age bin
avg_price_age = pd.DataFrame(pur_data.groupby('age_bin')['Price'].mean())
#finds total purchase value by age bin
tot_pur_age = pd.DataFrame(pur_data.groupby('age_bin')['Price'].sum())
#deletes multiple occurances of SN while only keeping last, then counts # of unique
#players by age bin
no_dup_age = pd.DataFrame(pur_data.drop_duplicates('SN', keep = 'last').groupby('age_bin')['SN'].count())
#merges all info from above into one df
merge_age = pd.merge(pur_count_age, avg_price_age, left_index = True, right_index = True).merge(tot_pur_age, left_index = True, right_index = True).merge(no_dup_age, left_index = True, right_index = True)
#renames columns
merge_age.rename(columns = {"SN_x": "# of Purchases", "Price_x": "Average Purchase Price", "Price_y": "Total Purchase Value", "SN_y": "# of Purchasers"}, inplace = True)
#calculates normalized totals
merge_age['Normalized Totals'] = merge_age['Total Purchase Value']/merge_age['# of Purchasers']
#rest index for aesthetics
merge_age.index.rename("Age", inplace = True)
# formats
merge_age.style.format({'Average Purchase Price': '${:.2f}', 'Total Purchase Value': '${:.2f}', 'Normalized Totals': '${:.2f}'})
```




<style  type="text/css" >
</style>  
<table id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" ># of Purchases</th> 
        <th class="col_heading level0 col1" >Average Purchase Price</th> 
        <th class="col_heading level0 col2" >Total Purchase Value</th> 
        <th class="col_heading level0 col3" ># of Purchasers</th> 
        <th class="col_heading level0 col4" >Normalized Totals</th> 
    </tr>    <tr> 
        <th class="index_name level0" >Age</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715level0_row0" class="row_heading level0 row0" >10 - 14</th> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row0_col0" class="data row0 col0" >38</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row0_col1" class="data row0 col1" >$2.79</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row0_col2" class="data row0 col2" >$105.91</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row0_col3" class="data row0 col3" >24</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row0_col4" class="data row0 col4" >$4.41</td> 
    </tr>    <tr> 
        <th id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715level0_row1" class="row_heading level0 row1" >15 - 19</th> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row1_col0" class="data row1 col0" >144</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row1_col1" class="data row1 col1" >$2.89</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row1_col2" class="data row1 col2" >$416.83</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row1_col3" class="data row1 col3" >104</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row1_col4" class="data row1 col4" >$4.01</td> 
    </tr>    <tr> 
        <th id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715level0_row2" class="row_heading level0 row2" >20 - 24</th> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row2_col0" class="data row2 col0" >372</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row2_col1" class="data row2 col1" >$2.92</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row2_col2" class="data row2 col2" >$1087.66</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row2_col3" class="data row2 col3" >280</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row2_col4" class="data row2 col4" >$3.88</td> 
    </tr>    <tr> 
        <th id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715level0_row3" class="row_heading level0 row3" >25 - 29</th> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row3_col0" class="data row3 col0" >134</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row3_col1" class="data row3 col1" >$2.96</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row3_col2" class="data row3 col2" >$396.44</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row3_col3" class="data row3 col3" >89</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row3_col4" class="data row3 col4" >$4.45</td> 
    </tr>    <tr> 
        <th id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715level0_row4" class="row_heading level0 row4" >30 - 34</th> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row4_col0" class="data row4 col0" >71</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row4_col1" class="data row4 col1" >$2.97</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row4_col2" class="data row4 col2" >$211.14</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row4_col3" class="data row4 col3" >51</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row4_col4" class="data row4 col4" >$4.14</td> 
    </tr>    <tr> 
        <th id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715level0_row5" class="row_heading level0 row5" >35 - 39</th> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row5_col0" class="data row5 col0" >48</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row5_col1" class="data row5 col1" >$2.93</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row5_col2" class="data row5 col2" >$140.77</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row5_col3" class="data row5 col3" >31</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row5_col4" class="data row5 col4" >$4.54</td> 
    </tr>    <tr> 
        <th id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715level0_row6" class="row_heading level0 row6" >< 10</th> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row6_col0" class="data row6 col0" >33</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row6_col1" class="data row6 col1" >$2.95</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row6_col2" class="data row6 col2" >$97.28</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row6_col3" class="data row6 col3" >22</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row6_col4" class="data row6 col4" >$4.42</td> 
    </tr>    <tr> 
        <th id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715level0_row7" class="row_heading level0 row7" >> 40</th> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row7_col0" class="data row7 col0" >18</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row7_col1" class="data row7 col1" >$3.24</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row7_col2" class="data row7 col2" >$58.40</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row7_col3" class="data row7 col3" >11</td> 
        <td id="T_db946bc2_1ed8_11e8_a7c7_78e3b55b6715row7_col4" class="data row7 col4" >$5.31</td> 
    </tr></tbody> 
</table> 




```python
# TOP SPENDERS

# Identify the the top 5 spenders in the game by total purchase value, then list (in a table):
# SN
# Purchase Count
# Average Purchase Price
# Total Purchase Value

#Group by screen name to find, total purchase per person, number of purchases per person, and average price price per person
purchase_amt_by_SN = pd.DataFrame(pur_data.groupby('SN')['Price'].sum())
num_purchase_by_SN = pd.DataFrame(pur_data.groupby('SN')['Price'].count())
avg_purchase_by_SN = pd.DataFrame(pur_data.groupby('SN')['Price'].mean())
# merge the above dfs
merged_top5 = pd.merge(purchase_amt_by_SN, num_purchase_by_SN, left_index = True, right_index = True).merge(avg_purchase_by_SN, left_index=True, right_index=True)
# rename columns
merged_top5.rename(columns = {'Price_x': 'Total Purchase Value', 'Price_y':'Purchase Count', 'Price':'Average Purchase Price'}, inplace = True)
# sort from highest purchase value to lowest
merged_top5.sort_values('Total Purchase Value', ascending = False, inplace=True)
# take top 5 only
merged_top5 = merged_top5.head()
# format
merged_top5.style.format({'Total Purchase Value': '${:.2f}', 'Average Purchase Price': '${:.2f}'})
```




<style  type="text/css" >
</style>  
<table id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Total Purchase Value</th> 
        <th class="col_heading level0 col1" >Purchase Count</th> 
        <th class="col_heading level0 col2" >Average Purchase Price</th> 
    </tr>    <tr> 
        <th class="index_name level0" >SN</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715level0_row0" class="row_heading level0 row0" >Undirrala66</th> 
        <td id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715row0_col0" class="data row0 col0" >$17.06</td> 
        <td id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715row0_col1" class="data row0 col1" >5</td> 
        <td id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715row0_col2" class="data row0 col2" >$3.41</td> 
    </tr>    <tr> 
        <th id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715level0_row1" class="row_heading level0 row1" >Aerithllora36</th> 
        <td id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715row1_col0" class="data row1 col0" >$15.10</td> 
        <td id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715row1_col1" class="data row1 col1" >4</td> 
        <td id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715row1_col2" class="data row1 col2" >$3.77</td> 
    </tr>    <tr> 
        <th id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715level0_row2" class="row_heading level0 row2" >Saedue76</th> 
        <td id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715row2_col0" class="data row2 col0" >$13.56</td> 
        <td id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715row2_col1" class="data row2 col1" >4</td> 
        <td id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715row2_col2" class="data row2 col2" >$3.39</td> 
    </tr>    <tr> 
        <th id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715level0_row3" class="row_heading level0 row3" >Sondim43</th> 
        <td id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715row3_col0" class="data row3 col0" >$13.02</td> 
        <td id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715row3_col1" class="data row3 col1" >4</td> 
        <td id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715row3_col2" class="data row3 col2" >$3.25</td> 
    </tr>    <tr> 
        <th id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715level0_row4" class="row_heading level0 row4" >Mindimnya67</th> 
        <td id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715row4_col0" class="data row4 col0" >$12.74</td> 
        <td id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715row4_col1" class="data row4 col1" >4</td> 
        <td id="T_db9ea518_1ed8_11e8_a4e4_78e3b55b6715row4_col2" class="data row4 col2" >$3.18</td> 
    </tr></tbody> 
</table> 




```python
# MOST POPULAR ITEMS

# Identify the 5 most popular items by purchase count, then list (in a table):
# Item ID
# Item Name
# Purchase Count
# Item Price
# Total Purchase Value

# gets a count of each item by grouping by Item ID and counting the number of each IDs occurances
top5_items_ID = pd.DataFrame(pur_data.groupby('Item ID')['Item ID'].count())
#sort from high to low total purchase count
top5_items_ID.sort_values('Item ID', ascending = False, inplace = True)
#keep the first 6 rows because there is a tie
top5_items_ID = top5_items_ID.iloc[0:6][:]
#find the total purchase value of each item
top5_items_total = pd.DataFrame(pur_data.groupby('Item ID')['Price'].sum())
#merge purcahse count and total purcahse value 
top5_items = pd.merge(top5_items_ID, top5_items_total, left_index = True, right_index = True)
#drop duplicate items from original Df
no_dup_items = pur_data.drop_duplicates(['Item ID'], keep = 'last')
# merge to get all other info from the top 6 using the no dup df
top5_merge_ID = pd.merge(top5_items, no_dup_items, left_index = True, right_on = 'Item ID')
#keep only neede columns
top5_merge_ID = top5_merge_ID[['Item ID', 'Item Name', 'Item ID_x', 'Price_y', 'Price_x']]
#reset index as item ID for aesthetics
top5_merge_ID.set_index(['Item ID'], inplace = True)
# rename columns
top5_merge_ID.rename(columns =  {'Item ID_x': 'Purchase Count', 'Price_y': 'Item Price', 'Price_x': 'Total Purchase Value'}, inplace=True)
#format
top5_merge_ID.style.format({'Item Price': '${:.2f}', 'Total Purchase Value': '${:.2f}'})
```




<style  type="text/css" >
</style>  
<table id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Item Name</th> 
        <th class="col_heading level0 col1" >Purchase Count</th> 
        <th class="col_heading level0 col2" >Item Price</th> 
        <th class="col_heading level0 col3" >Total Purchase Value</th> 
    </tr>    <tr> 
        <th class="index_name level0" >Item ID</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715level0_row0" class="row_heading level0 row0" >84</th> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row0_col0" class="data row0 col0" >Arcane Gem</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row0_col1" class="data row0 col1" >12</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row0_col2" class="data row0 col2" >$4.81</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row0_col3" class="data row0 col3" >$29.34</td> 
    </tr>    <tr> 
        <th id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715level0_row1" class="row_heading level0 row1" >39</th> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row1_col0" class="data row1 col0" >Betrayal, Whisper of Grieving Widows</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row1_col1" class="data row1 col1" >11</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row1_col2" class="data row1 col2" >$2.35</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row1_col3" class="data row1 col3" >$25.85</td> 
    </tr>    <tr> 
        <th id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715level0_row2" class="row_heading level0 row2" >31</th> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row2_col0" class="data row2 col0" >Trickster</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row2_col1" class="data row2 col1" >10</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row2_col2" class="data row2 col2" >$4.59</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row2_col3" class="data row2 col3" >$23.22</td> 
    </tr>    <tr> 
        <th id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715level0_row3" class="row_heading level0 row3" >44</th> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row3_col0" class="data row3 col0" >Bonecarvin Battle Axe</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row3_col1" class="data row3 col1" >9</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row3_col2" class="data row3 col2" >$4.36</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row3_col3" class="data row3 col3" >$24.04</td> 
    </tr>    <tr> 
        <th id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715level0_row4" class="row_heading level0 row4" >154</th> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row4_col0" class="data row4 col0" >Feral Katana</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row4_col1" class="data row4 col1" >9</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row4_col2" class="data row4 col2" >$4.11</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row4_col3" class="data row4 col3" >$23.55</td> 
    </tr>    <tr> 
        <th id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715level0_row5" class="row_heading level0 row5" >107</th> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row5_col0" class="data row5 col0" >Splitter, Foe Of Subtlety</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row5_col1" class="data row5 col1" >9</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row5_col2" class="data row5 col2" >$4.15</td> 
        <td id="T_dbb2c9a4_1ed8_11e8_ab53_78e3b55b6715row5_col3" class="data row5 col3" >$33.03</td> 
    </tr></tbody> 
</table> 




```python
# MOST PROFITABLE

# Identify the 5 most profitable items by total purchase value, then list (in a table):
# Item ID
# Item Name
# Purchase Count
# Item Price
# Total Purchase Value

# find total purcahse value and sort by high to low
top5_profit = pd.DataFrame(pur_data.groupby('Item ID')['Price'].sum())
top5_profit.sort_values('Price', ascending = False, inplace = True)
# only keep top 5
top5_profit = top5_profit.iloc[0:5][:]
#get item purchase count
pur_count_profit = pd.DataFrame(pur_data.groupby('Item ID')['Item ID'].count())

top5_profit = pd.merge(top5_profit, pur_count_profit, left_index = True, right_index = True, how = 'left')
top5_merge_profit = pd.merge(top5_profit, no_dup_items, left_index = True, right_on = 'Item ID', how = 'left')
top5_merge_profit = top5_merge_profit[['Item ID', 'Item Name', 'Item ID_x', 'Price_y','Price_x']]
top5_merge_profit.set_index(['Item ID'], inplace=True)
top5_merge_profit.rename(columns = {'Item ID_x': 'Purchase Count', 'Price_y': 'Item Price', 'Price_x': 'Total Purchase Value'}, inplace = True)
top5_merge_profit.style.format({'Item Price': '${:.2f}', 'Total Purchase Value': '${:.2f}'})
```




<style  type="text/css" >
</style>  
<table id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Item Name</th> 
        <th class="col_heading level0 col1" >Purchase Count</th> 
        <th class="col_heading level0 col2" >Item Price</th> 
        <th class="col_heading level0 col3" >Total Purchase Value</th> 
    </tr>    <tr> 
        <th class="index_name level0" >Item ID</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715level0_row0" class="row_heading level0 row0" >34</th> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row0_col0" class="data row0 col0" >Retribution Axe</td> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row0_col1" class="data row0 col1" >9</td> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row0_col2" class="data row0 col2" >$4.14</td> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row0_col3" class="data row0 col3" >$37.26</td> 
    </tr>    <tr> 
        <th id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715level0_row1" class="row_heading level0 row1" >107</th> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row1_col0" class="data row1 col0" >Splitter, Foe Of Subtlety</td> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row1_col1" class="data row1 col1" >9</td> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row1_col2" class="data row1 col2" >$4.15</td> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row1_col3" class="data row1 col3" >$33.03</td> 
    </tr>    <tr> 
        <th id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715level0_row2" class="row_heading level0 row2" >115</th> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row2_col0" class="data row2 col0" >Spectral Diamond Doomblade</td> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row2_col1" class="data row2 col1" >7</td> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row2_col2" class="data row2 col2" >$4.25</td> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row2_col3" class="data row2 col3" >$29.75</td> 
    </tr>    <tr> 
        <th id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715level0_row3" class="row_heading level0 row3" >32</th> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row3_col0" class="data row3 col0" >Orenmir</td> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row3_col1" class="data row3 col1" >6</td> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row3_col2" class="data row3 col2" >$4.95</td> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row3_col3" class="data row3 col3" >$29.70</td> 
    </tr>    <tr> 
        <th id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715level0_row4" class="row_heading level0 row4" >84</th> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row4_col0" class="data row4 col0" >Arcane Gem</td> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row4_col1" class="data row4 col1" >12</td> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row4_col2" class="data row4 col2" >$4.81</td> 
        <td id="T_dbc31d92_1ed8_11e8_b3ce_78e3b55b6715row4_col3" class="data row4 col3" >$29.34</td> 
    </tr></tbody> 
</table> 


