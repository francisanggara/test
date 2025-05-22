### Bank Statement Anaysis

https://colab.research.google.com/drive/1aQmPEM0EWlCxCnJ0FiAmgw5wc6mKckMG?authuser=1#scrollTo=1kIu15tqkwp5

<h2 style="color:green">Financial Statements Formatting</h2>
I want to be able to format UWCU and Visa monthly statements into a format I where can easily visualize data.

I'd like to be able to see how much I spent on specific categories(eating out, school supplies, etc.) semester to semester.

Excel already has features that allows me to screenshot a statement and put it into a .csv file already, but I'm curious if I can use my coding knowledge and the internet to:
- make a pie chart of what I spent in which categories
- select a date range of the analysis
- compile the data from my credit and debit cards.
  
UWCU already has a built-in pie chart feature, but it doesn't allow me to exclude certain categories.

- Create a user-input interface
- Ask for a filename
- Ask if want to add another file
- Ask if want to look at spending or income
- Ask if want a date range
- Ask if want to filter out any categories

So far, I've gotten to:
- Starting a user-input interface
- Asked for a filename
- Asked if I want to add another file
- Asked if I wanted 


```python
import csv
import pandas as pd
import datetime
import matplotlib.pyplot as plt
```

My program is currently pretty finicky. There *needs* to be **four** columns labled *exactly* as shown, with the data in the 'Amount' column in a *number* format (if you see a dollar sign or comma in that column, my program will crash). 

To get a .csv like this, follow these instructions(specific to UWCU's Personal Statements and VISA's Credit Card Statements):




<img src= "examplecsv.png" width="700">

This cell takes in a .csv, cleans it up, and returns it as a DataFrame.


```python
def process_csv(file):
    df = pd.read_csv(file) # load the .csv and converts it into a DataFrame
    df = df.iloc[:, 0:4] # in case the .csv has more than four columns, the program is hardcoded to chop it down into four
    df = df.dropna() # in case there are extra rows in the DataFrame, this removes them
    df['Amount'] = df['Amount'].astype(float) # formats the 'Amount' column as floats
    
    for cell in range(len(df['Date'])):
        current_cell = df.loc[cell, 'Date']
        # print(current_cell)
        if len(current_cell) == 5:
            current_cell = pd.to_datetime(df.loc[cell, 'Date'] + '/2025') # currently is hardcoded to add the year 2025 to the date
            current_cell = current_cell.strftime("%x") # format the dates to be MM/DD/YY
            # current_cell = pd.to_datetime(current_cell) # uncomment this line to convert to DateTime format
            df.loc[cell, 'Date'] = current_cell
        else:
            current_cell = pd.to_datetime(df.loc[cell, 'Date'])
            current_cell = current_cell.strftime("%x")
            df.loc[cell, 'Date'] = current_cell
    return df
current_df = process_csv("1.csv")
current_df
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
      <th>Date</th>
      <th>Description</th>
      <th>Category</th>
      <th>Amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>03/31/25</td>
      <td>ACH:The Regent       -Rent</td>
      <td>Rent</td>
      <td>-300.00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>04/01/25</td>
      <td>ACH:IRS  TREAS 310   -  TAX REF</td>
      <td>Miscellaneous Income</td>
      <td>13.00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>04/04/25</td>
      <td>ACH:WI DEPT REVENUE  -WISTTAXRFD</td>
      <td>Miscellaneous Income</td>
      <td>41.00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>04/04/25</td>
      <td>TRADER JOE S #71 TRADER MADISON       WI US</td>
      <td>Groceries</td>
      <td>-83.09</td>
    </tr>
    <tr>
      <th>4</th>
      <td>04/05/25</td>
      <td>Web Branch:Zelle Rishabh  Dw 800-533-6773</td>
      <td>Rent</td>
      <td>-1079.60</td>
    </tr>
    <tr>
      <th>5</th>
      <td>04/08/25</td>
      <td>Web Branch:MOBILE DEPOSIT</td>
      <td>Rent</td>
      <td>300.00</td>
    </tr>
    <tr>
      <th>6</th>
      <td>04/15/25</td>
      <td>DEBITCARD 1845:PURCHASE HEADWAY.CO    NY</td>
      <td>Healthcare</td>
      <td>-94.09</td>
    </tr>
    <tr>
      <th>7</th>
      <td>04/18/25</td>
      <td>Web Branch:UWCU VISA PAYMENT XX-7436</td>
      <td>Credit Card Payoff</td>
      <td>-94.64</td>
    </tr>
    <tr>
      <th>8</th>
      <td>04/21/25</td>
      <td>Web Branch:UWCU VISA PAYMENT XX-7436</td>
      <td>Credit Card Payoff</td>
      <td>-55.85</td>
    </tr>
    <tr>
      <th>9</th>
      <td>04/25/25</td>
      <td>TRADER JOE S #71 TRADER MADISON       WI US</td>
      <td>Groceries</td>
      <td>-9.50</td>
    </tr>
    <tr>
      <th>10</th>
      <td>04/29/25</td>
      <td>DIVIDEND PAID</td>
      <td>Miscellaneous Income</td>
      <td>0.05</td>
    </tr>
  </tbody>
</table>
</div>



This cell filters the DataFrame into only positive values(income).


```python
pos_df = current_df[current_df['Amount'] > 0]
pos_df
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
      <th>Date</th>
      <th>Description</th>
      <th>Category</th>
      <th>Amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>04/01/25</td>
      <td>ACH:IRS  TREAS 310   -  TAX REF</td>
      <td>Miscellaneous Income</td>
      <td>13.00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>04/04/25</td>
      <td>ACH:WI DEPT REVENUE  -WISTTAXRFD</td>
      <td>Miscellaneous Income</td>
      <td>41.00</td>
    </tr>
    <tr>
      <th>5</th>
      <td>04/08/25</td>
      <td>Web Branch:MOBILE DEPOSIT</td>
      <td>Rent</td>
      <td>300.00</td>
    </tr>
    <tr>
      <th>10</th>
      <td>04/29/25</td>
      <td>DIVIDEND PAID</td>
      <td>Miscellaneous Income</td>
      <td>0.05</td>
    </tr>
  </tbody>
</table>
</div>




```python
repeat = True
while repeat:
    add_more = input("Add another dataFrame(y/n)?")
    if add_more == 'y':
        new_df = process_csv(input("Enter a .csv file:"))
        new_df = pos_df.merge(new_df, how = 'outer')
        add_more = input("Add another dataFrame(y/n)?")
    if add_more == 'n':
        repeat = False
new_df
```

    Add another dataFrame(y/n)? n
    


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    Cell In[12], line 10
          8     if add_more == 'n':
          9         repeat = False
    ---> 10 new_df
    

    NameError: name 'new_df' is not defined



```python
reset_df = pos_df.reset_index()
reset_df = reset_df.iloc[:, 1:]

combined_data = {}
for idx in range(len(pos_df.iloc[:, 2])):
    current_category = pos_df.iloc[idx, 2]
    if current_category not in combined_data:
        amount_list = 0
        combined_data[current_category] = amount_list
        # amount_list.append(pos_df.iloc[idx,3])
    for key in combined_data:
        if key == current_category:
            combined_data[current_category] += pos_df.iloc[idx,3]
print(combined_data)
reset_df
```

    {'Groceries': 11.07, 'Office Supplies': 22.439999999999998, 'Dining Out': 86.91999999999999, 'Accessories': 10.5}
    




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
      <th>Date</th>
      <th>Description</th>
      <th>Category</th>
      <th>Amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3/29/2025</td>
      <td>TRADER JOE S #712 MADISON  WI</td>
      <td>Groceries</td>
      <td>11.07</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3/30/2025</td>
      <td>TARGET        00034058 MADISON  WI</td>
      <td>Office Supplies</td>
      <td>4.58</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4/4/2025</td>
      <td>UW MADISON WISC UNION MADISON  WI</td>
      <td>Dining Out</td>
      <td>10.50</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4/7/2025</td>
      <td>UW GORDON DINING MADISON  WI</td>
      <td>Dining Out</td>
      <td>13.40</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4/10/2025</td>
      <td>SUNNY PHO ASIAN RESTAURAN MADISON  WI</td>
      <td>Dining Out</td>
      <td>33.04</td>
    </tr>
    <tr>
      <th>5</th>
      <td>4/11/2025</td>
      <td>TROPIC JEWEL LLC MADISON  WI</td>
      <td>Accessories</td>
      <td>10.50</td>
    </tr>
    <tr>
      <th>6</th>
      <td>4/15/2025</td>
      <td>UW MADISON WISC UNION MADISON  WI</td>
      <td>Dining Out</td>
      <td>11.55</td>
    </tr>
    <tr>
      <th>7</th>
      <td>4/19/2025</td>
      <td>CHEN'S DUMPLING HOUSE MADISON  WI</td>
      <td>Dining Out</td>
      <td>18.43</td>
    </tr>
    <tr>
      <th>8</th>
      <td>4/21/2025</td>
      <td>TARGET        00034058 MADISON  WI</td>
      <td>Office Supplies</td>
      <td>17.86</td>
    </tr>
  </tbody>
</table>
</div>




```python
merged_values = combined_data.values()
merged_labels = combined_data.keys()

plt.pie(merged_values, labels=merged_labels, autopct='%1.1f%%')
plt.title('Income', fontsize=15, fontweight='bold')
plt.show()
```


    
![png](output_10_0.png)
    



```python
df.loc[:, "Amount"] = -1 * df.loc[:, "Amount"]
df = df[df['Amount'] > 0]

combined_data = {}
for idx in range(len(df.iloc[:, 2])):
    current_category = df.iloc[idx, 2]
    if current_category not in combined_data:
        amount_list = 0
        combined_data[current_category] = amount_list
        # amount_list.append(pos_df.iloc[idx,3])
    for key in combined_data:
        if key == current_category:
            combined_data[current_category] += df.iloc[idx,3]
merged_values = combined_data.values()
merged_labels = combined_data.keys()


plt.pie(merged_values, labels=merged_labels, autopct='%1.1f%%')
plt.title('Spending', fontsize=15, fontweight='bold')
plt.show()
```


    
![png](output_11_0.png)
    


For the sake of convenience, here's the DataFrame 


```python
df
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
      <th>Date</th>
      <th>Description</th>
      <th>Category</th>
      <th>Amount</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
</div>




```python
reset_df = df.reset_index()
reset_df = reset_df.iloc[:, 1:]
reset_df
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
      <th>Date</th>
      <th>Description</th>
      <th>Category</th>
      <th>Amount</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
</div>


This cell filters out a certain category


```python
filtered_category = input("Enter the category you want to filter out:")

fresh_df = []
for cell in range(len(reset_df)):
    if reset_df.loc[cell, 'Category'] != filtered_category:
        current_cell = reset_df.loc[cell, 'Category']
        fresh_df.append(reset_df.loc[cell])
fresh_df = pd.DataFrame(fresh_df)
fresh_df
```

    Enter the category you want to filter out: 
    




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
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
</div>




```python
def combine_slices(input_df):
    combined_data = {}
    for idx in range(len(input_df.iloc[:, 2])):
        current_category = input_df.iloc[idx, 2]
        if current_category not in combined_data:
            amount_list = 0
            combined_data[current_category] = amount_list
            # amount_list.append(pos_df.iloc[idx,3])
        for key in combined_data:
            if key == current_category:
                combined_data[current_category] += input_df.iloc[idx,3]
    merged_values = combined_data.values()
    merged_labels = combined_data.keys()
    return combined_data
```


```python
dict_here = combine_slices(fresh_df)
plt.pie(dict_here.values(), labels=dict_here.keys(), autopct='%1.1f%%')
plt.title('Income', fontsize=15, fontweight='bold')
plt.show()
```


    ---------------------------------------------------------------------------

    IndexError                                Traceback (most recent call last)

    Cell In[526], line 1
    ----> 1 dict_here = combine_slices(fresh_df)
          2 plt.pie(dict_here.values(), labels=dict_here.keys(), autopct='%1.1f%%')
          3 plt.title('Income', fontsize=15, fontweight='bold')
    

    Cell In[524], line 3, in combine_slices(input_df)
          1 def combine_slices(input_df):
          2     combined_data = {}
    ----> 3     for idx in range(len(input_df.iloc[:, 2])):
          4         current_category = input_df.iloc[idx, 2]
          5         if current_category not in combined_data:
    

    File ~\anaconda3\lib\site-packages\pandas\core\indexing.py:1184, in _LocationIndexer.__getitem__(self, key)
       1182     if self._is_scalar_access(key):
       1183         return self.obj._get_value(*key, takeable=self._takeable)
    -> 1184     return self._getitem_tuple(key)
       1185 else:
       1186     # we by definition only have the 0th axis
       1187     axis = self.axis or 0
    

    File ~\anaconda3\lib\site-packages\pandas\core\indexing.py:1690, in _iLocIndexer._getitem_tuple(self, tup)
       1689 def _getitem_tuple(self, tup: tuple):
    -> 1690     tup = self._validate_tuple_indexer(tup)
       1691     with suppress(IndexingError):
       1692         return self._getitem_lowerdim(tup)
    

    File ~\anaconda3\lib\site-packages\pandas\core\indexing.py:966, in _LocationIndexer._validate_tuple_indexer(self, key)
        964 for i, k in enumerate(key):
        965     try:
    --> 966         self._validate_key(k, i)
        967     except ValueError as err:
        968         raise ValueError(
        969             "Location based indexing can only have "
        970             f"[{self._valid_types}] types"
        971         ) from err
    

    File ~\anaconda3\lib\site-packages\pandas\core\indexing.py:1592, in _iLocIndexer._validate_key(self, key, axis)
       1590     return
       1591 elif is_integer(key):
    -> 1592     self._validate_integer(key, axis)
       1593 elif isinstance(key, tuple):
       1594     # a tuple should already have been caught by this point
       1595     # so don't treat a tuple as a valid indexer
       1596     raise IndexingError("Too many indexers")
    

    File ~\anaconda3\lib\site-packages\pandas\core\indexing.py:1685, in _iLocIndexer._validate_integer(self, key, axis)
       1683 len_axis = len(self.obj._get_axis(axis))
       1684 if key >= len_axis or key < -len_axis:
    -> 1685     raise IndexError("single positional indexer is out-of-bounds")
    

    IndexError: single positional indexer is out-of-bounds



```python
min_date = input('Enter the minimum date you want to filter your data(MM/DD/YYYY):')
max_date = input('Enter the maximum date you want to filter your data(MM/DD/YYYY:')

max_date = pd.to_datetime(max_date)
min_date = pd.to_datetime(min_date)

sliced_df = df[min_date <= pd.to_datetime(df['Date'], format='%Y-%m-%d %H:%M:%S.%f')]
sliced_df = df[max_date >= pd.to_datetime(df['Date'], format='%Y-%m-%d %H:%M:%S.%f')]
sliced_df
```

### This cell will create a pie chart of the values you chose in the cell above.


```python
new_dict = combine_slices(sliced_df)
plt.pie(new_dict.values(), labels=new_dict.keys(), autopct='%1.1f%%')
plt.title('Spending', fontsize=15, fontweight='bold')
plt.show()
```


    
![png](output_21_0.png)
    



```python
# df.to_csv("better_movies.csv", index = False)
#write the df to a csv
```


```python
new_file = input("Welcome! Please input the file name(must be .csv) you'd like to analyze: ")
current_df = process_csv(new_file)

add_new = True
while add_new:
    add_true = input("Add another file(y/n)?")
    if add_true == 'y':
        combine_df = process_csv(input("Enter a .csv file:"))
        combined_df = combine_df.merge(current_df, how = 'outer')
        current_df = combined_df.copy()
        add_true = input("Add another dataFrame(y/n)?")
    if add_true == 'n':
        add_new = False
        combined_df = current_df

current_df
```

    Welcome! Please input the file name(must be .csv) you'd like to analyze:  1.csv
    Add another file(y/n)? n
    




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
      <th>Date</th>
      <th>Description</th>
      <th>Category</th>
      <th>Amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>03/31/25</td>
      <td>ACH:The Regent       -Rent</td>
      <td>Rent</td>
      <td>-300.00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>04/01/25</td>
      <td>ACH:IRS  TREAS 310   -  TAX REF</td>
      <td>Miscellaneous Income</td>
      <td>13.00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>04/04/25</td>
      <td>ACH:WI DEPT REVENUE  -WISTTAXRFD</td>
      <td>Miscellaneous Income</td>
      <td>41.00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>04/04/25</td>
      <td>TRADER JOE S #71 TRADER MADISON       WI US</td>
      <td>Groceries</td>
      <td>-83.09</td>
    </tr>
    <tr>
      <th>4</th>
      <td>04/05/25</td>
      <td>Web Branch:Zelle Rishabh  Dw 800-533-6773</td>
      <td>Rent</td>
      <td>-1079.60</td>
    </tr>
    <tr>
      <th>5</th>
      <td>04/08/25</td>
      <td>Web Branch:MOBILE DEPOSIT</td>
      <td>Rent</td>
      <td>300.00</td>
    </tr>
    <tr>
      <th>6</th>
      <td>04/15/25</td>
      <td>DEBITCARD 1845:PURCHASE HEADWAY.CO    NY</td>
      <td>Healthcare</td>
      <td>-94.09</td>
    </tr>
    <tr>
      <th>7</th>
      <td>04/18/25</td>
      <td>Web Branch:UWCU VISA PAYMENT XX-7436</td>
      <td>Credit Card Payoff</td>
      <td>-94.64</td>
    </tr>
    <tr>
      <th>8</th>
      <td>04/21/25</td>
      <td>Web Branch:UWCU VISA PAYMENT XX-7436</td>
      <td>Credit Card Payoff</td>
      <td>-55.85</td>
    </tr>
    <tr>
      <th>9</th>
      <td>04/25/25</td>
      <td>TRADER JOE S #71 TRADER MADISON       WI US</td>
      <td>Groceries</td>
      <td>-9.50</td>
    </tr>
    <tr>
      <th>10</th>
      <td>04/29/25</td>
      <td>DIVIDEND PAID</td>
      <td>Miscellaneous Income</td>
      <td>0.05</td>
    </tr>
  </tbody>
</table>
</div>




```python
repeat = True
while repeat:
    flow = input("Would you like to look at spending(s) or income(i)?")
    if flow == "i":
        current_df = current_df[current_df['Amount'] > 0]
        repeat = False
    elif flow == "s":
        current_df.loc[:, "Amount"] = -1 * current_df.loc[:, "Amount"]
        # current_df = df.reset_index()
        # current_df = current_df.iloc[:, 1:]
        current_df = current_df[current_df['Amount'] > 0]
        repeat = False
    else:
        print("Invalid input, dude!")
current_df
```

    Would you like to look at spending(s) or income(i)? s
    




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
      <th>Date</th>
      <th>Description</th>
      <th>Category</th>
      <th>Amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>03/31/25</td>
      <td>ACH:The Regent       -Rent</td>
      <td>Rent</td>
      <td>300.00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>04/04/25</td>
      <td>TRADER JOE S #71 TRADER MADISON       WI US</td>
      <td>Groceries</td>
      <td>83.09</td>
    </tr>
    <tr>
      <th>4</th>
      <td>04/05/25</td>
      <td>Web Branch:Zelle Rishabh  Dw 800-533-6773</td>
      <td>Rent</td>
      <td>1079.60</td>
    </tr>
    <tr>
      <th>6</th>
      <td>04/15/25</td>
      <td>DEBITCARD 1845:PURCHASE HEADWAY.CO    NY</td>
      <td>Healthcare</td>
      <td>94.09</td>
    </tr>
    <tr>
      <th>7</th>
      <td>04/18/25</td>
      <td>Web Branch:UWCU VISA PAYMENT XX-7436</td>
      <td>Credit Card Payoff</td>
      <td>94.64</td>
    </tr>
    <tr>
      <th>8</th>
      <td>04/21/25</td>
      <td>Web Branch:UWCU VISA PAYMENT XX-7436</td>
      <td>Credit Card Payoff</td>
      <td>55.85</td>
    </tr>
    <tr>
      <th>9</th>
      <td>04/25/25</td>
      <td>TRADER JOE S #71 TRADER MADISON       WI US</td>
      <td>Groceries</td>
      <td>9.50</td>
    </tr>
  </tbody>
</table>
</div>




```python
def filter():
    repeat = True
    while repeat:
        cut = input("Would you like to filter out a category(y/n)? This question will repeat if you type 'y' and give an input to the next question:")
        if cut == "y":
            cut_category = input("Enter the category you'd like to filter out:")
            filter()
            repeat = False
        elif cut == "n":
            repeat = False
        else:
            print("Invalid input, dude!")
frame = filter()
```

    Would you like to filter out a category(y/n)? This question will repeat if you type 'y' and give an input to the next question: n
    


```python
# %history
```


```python

```
