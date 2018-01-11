

```python
import pandas as pd
import json
import os


```


```python
pwd
```




    'C:\\Users\\Jeff\\Desktop\\Data Bootcamp Files\\Homework 4 Pandas'




```python
data = pd.read_json("purchase_data.json",orient="records")
```


```python
data.head()
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
total_players = data["SN"].nunique()
total_players_df = pd.DataFrame({"Total Players": [total_players]})

total_players_df.head()
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




```python
num_of_items = data["Item ID"].nunique()
average_price = data["Price"].mean()
number_of_purch = len(data)
total_revenue = data["Price"].sum()
purch_analysis_df = pd.DataFrame({"Number of Unique Items": [num_of_items], "Average Price": [average_price], "Number of Purchases": [number_of_purch], "Total Revenue": [total_revenue]})

purch_analysis_df["Average Price"] = purch_analysis_df["Average Price"].map("${:.2f}".format)
purch_analysis_df["Total Revenue"] = purch_analysis_df["Total Revenue"].map("${:.2f}".format)

purch_analysis_df.head()
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
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Number of Unique Items</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>$2.93</td>
      <td>780</td>
      <td>183</td>
      <td>$2286.33</td>
    </tr>
  </tbody>
</table>
</div>




```python
male_total = len(data[data["Gender"] == 'Male'])
female_total = len(data[data["Gender"] == 'Female'])
other_total = len(data[data["Gender"] == 'Other / Non-Disclosed'])
all_total = male_total + female_total + other_total

gender_demo_df = pd.DataFrame({"":["Male", "Female", "Other / Non-Disclosed"], "Percentage of Players": [(male_total/all_total)*100, (female_total/all_total)*100, (other_total/all_total)*100], "Total Count": [male_total, female_total, other_total]})
gender_demo_df["Percentage of Players"] = gender_demo_df["Percentage of Players"].map("{:.2f}%".format)
gender_demo_df
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
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Male</td>
      <td>81.15%</td>
      <td>633</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Female</td>
      <td>17.44%</td>
      <td>136</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Other / Non-Disclosed</td>
      <td>1.41%</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>




```python
gender_data = pd.DataFrame(data.groupby('Gender')['Item ID'].count())
gender_data['Average Purchase Price'] = data.groupby('Gender')['Price'].mean()
gender_data['Total Purchase Value'] = data.groupby('Gender')['Price'].sum()

norm_data = pd.DataFrame(data.groupby('Gender')['SN'].nunique())
gender_data['Normalized Totals'] =  data.groupby('Gender')['Price'].sum()/ norm_data['SN']
gender_data.rename(columns={'Item ID':'Purchase Count'}, inplace=True)

gender_data["Average Purchase Price"] = gender_data["Average Purchase Price"].map("${:.2f}".format)
gender_data["Total Purchase Value"] = gender_data["Total Purchase Value"].map("${:.2f}".format)
gender_data["Normalized Totals"] = gender_data["Normalized Totals"].map("${:.2f}".format)

gender_data
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
      <td>$1867.68</td>
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




```python
bins = [0, 10, 15, 20, 25, 30, 35, 39, 40]
group_names = ["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"]
data["Age Groups"] = pd.cut(data["Age"], bins, labels=group_names)
ages_count = data.groupby('Age Groups')['Gender'].count()
ages_analysis = pd.DataFrame(ages_count)
ages_analysis["Percentage of Players"] = ages_analysis["Gender"]/ all_total *100
ages_analysis["Percentage of Players"] = ages_analysis["Percentage of Players"].map("{0:,.2f}%".format)
ages_analysis.rename(columns={'Gender':'Total Count'}, inplace=True)

ages_analysis
 


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
      <th>Total Count</th>
      <th>Percentage of Players</th>
    </tr>
    <tr>
      <th>Age Groups</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>32</td>
      <td>4.10%</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>78</td>
      <td>10.00%</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>184</td>
      <td>23.59%</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>305</td>
      <td>39.10%</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>76</td>
      <td>9.74%</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>58</td>
      <td>7.44%</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>30</td>
      <td>3.85%</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>14</td>
      <td>1.79%</td>
    </tr>
  </tbody>
</table>
</div>




```python
purch_item_count = pd.DataFrame(data.groupby('Age Groups')['Item ID'].count())
purch_item_count.rename(columns={"Item ID":'Purchase Count'}, inplace=True)
purch_item_count['Average Purchase Price'] = data.groupby('Age Groups')['Price'].sum()/ purch_item_count['Purchase Count']
purch_item_count['Total Purchase Price'] = data.groupby('Age Groups')['Price'].sum()

norm_count = pd.DataFrame(data.groupby('Age Groups')['SN'].nunique())
purch_item_count['Normalized Totals'] =  data.groupby('Age Groups')['Price'].sum()/ norm_count['SN']

purch_item_count["Normalized Totals"] = purch_item_count["Normalized Totals"].map("${:.2f}".format)
purch_item_count["Average Purchase Price"] = purch_item_count["Average Purchase Price"].map("${:.2f}".format)
purch_item_count["Total Purchase Price"] = purch_item_count["Total Purchase Price"].map("${:.2f}".format)

purch_item_count.head()
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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Price</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Age Groups</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>32</td>
      <td>$3.02</td>
      <td>$96.62</td>
      <td>$4.39</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>78</td>
      <td>$2.87</td>
      <td>$224.15</td>
      <td>$4.15</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>184</td>
      <td>$2.87</td>
      <td>$528.74</td>
      <td>$3.80</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>305</td>
      <td>$2.96</td>
      <td>$902.61</td>
      <td>$3.86</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>76</td>
      <td>$2.89</td>
      <td>$219.82</td>
      <td>$4.23</td>
    </tr>
  </tbody>
</table>
</div>




```python
spenders = pd.DataFrame(data.groupby('SN')['Price'].sum())
spenders = spenders.sort_values('Price', ascending=False)
spenders['Purchase Count'] = data.groupby('SN')['Item ID'].count()
spenders['Average Purchase Price'] = spenders['Price']/ spenders['Purchase Count']

spenders["Average Purchase Price"] = spenders["Average Purchase Price"].map("${:.2f}".format)
spenders["Price"] = spenders["Price"].map("${:.2f}".format)

spenders.head()
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
      <th>Price</th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
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
      <td>$17.06</td>
      <td>5</td>
      <td>$3.41</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>$13.56</td>
      <td>4</td>
      <td>$3.39</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>$12.74</td>
      <td>4</td>
      <td>$3.18</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>$12.73</td>
      <td>3</td>
      <td>$4.24</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>$11.58</td>
      <td>3</td>
      <td>$3.86</td>
    </tr>
  </tbody>
</table>
</div>




```python
top_items = pd.DataFrame(data.groupby('Item ID')['SN'].count())
top_items = top_items.sort_values('SN', ascending=False)
top_items["Item Name"] = data['Item Name']
top_items["Item Price"] = data['Price']
top_items["Total Purchase Value"] = top_items['SN']* top_items['Item Price']
top_items.rename(columns={"SN":'Purchase Count'}, inplace=True)

top_items["Item Price"] = top_items["Item Price"].map("${:.2f}".format)
top_items["Total Purchase Value"] = top_items["Total Purchase Value"].map("${:.2f}".format)
top_items = top_items[['Item Name', 'Purchase Count', 'Item Price', 'Total Purchase Value']]

top_items.head()
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
      <th>Item Name</th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <td>Stormfury Mace</td>
      <td>11</td>
      <td>$1.27</td>
      <td>$13.97</td>
    </tr>
    <tr>
      <th>84</th>
      <td>Thorn, Satchel of Dark Souls</td>
      <td>11</td>
      <td>$4.51</td>
      <td>$49.61</td>
    </tr>
    <tr>
      <th>31</th>
      <td>Shadow Strike, Glory of Ending Hope</td>
      <td>9</td>
      <td>$1.93</td>
      <td>$17.37</td>
    </tr>
    <tr>
      <th>175</th>
      <td>Retribution Axe</td>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Piety, Guardian of Riddles</td>
      <td>9</td>
      <td>$3.68</td>
      <td>$33.12</td>
    </tr>
  </tbody>
</table>
</div>




```python
top_value = pd.DataFrame(data.groupby('Item ID')['SN'].count())

top_value["Item Name"] = data['Item Name']
top_value["Item Price"] = data['Price']

top_value.rename(columns={"SN":'Purchase Count'}, inplace=True)
top_value["Total Purchase Value"] = top_value['Purchase Count'] * top_value['Item Price']

top_value = top_value.sort_values('Total Purchase Value', ascending=False)
top_value["Item Price"] = top_value["Item Price"].map("${:.2f}".format)
top_value["Total Purchase Value"] = top_value["Total Purchase Value"].map("${:.2f}".format)
top_value = top_value[['Item Name', 'Purchase Count', 'Item Price', 'Total Purchase Value']]
top_value.head()
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
      <th>Item Name</th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>84</th>
      <td>Thorn, Satchel of Dark Souls</td>
      <td>11</td>
      <td>$4.51</td>
      <td>$49.61</td>
    </tr>
    <tr>
      <th>65</th>
      <td>Undead Crusader</td>
      <td>8</td>
      <td>$4.67</td>
      <td>$37.36</td>
    </tr>
    <tr>
      <th>175</th>
      <td>Retribution Axe</td>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>92</th>
      <td>Reaper's Toll</td>
      <td>8</td>
      <td>$4.56</td>
      <td>$36.48</td>
    </tr>
    <tr>
      <th>107</th>
      <td>Spectral Diamond Doomblade</td>
      <td>8</td>
      <td>$4.25</td>
      <td>$34.00</td>
    </tr>
  </tbody>
</table>
</div>


