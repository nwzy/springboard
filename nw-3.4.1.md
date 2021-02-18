# Springboard
# 3.4.1 - API data wrangling
---
Nicolas Wong

## Goal
Use the [Quandl API](https://docs.quandl.com/docs/time-series) API to explore equities data from the Fankfurt Stock Exchange. We'll analyze the stock prices of a company called [Carl Zeiss Meditec](https://www.zeiss.com/meditec/int/home.html) (stock ticker AFX_X)

## Notes
Try to only used the package *[requests](http://docs.python-requests.org/en/master/)* and packages provided from the *stdlib* (such as _Collections_)

### These are your tasks for this mini project:

1. Collect data from the Franfurt Stock Exchange, for the ticker AFX_X, for the whole year 2017 (keep in mind that the date format is YYYY-MM-DD).
2. Convert the returned JSON object into a Python dictionary.
3. Calculate what the highest and lowest opening prices were for the stock in this period.
4. What was the largest change in any one day (based on High and Low price)?
5. What was the largest change between any two days (based on Closing Price)?
6. What was the average daily trading volume during this year?
7. (Optional) What was the median trading volume during this year. (Note: you may need to implement your own function for calculating the median.)

## Setup


```python
import os
API_KEY = os.environ['QUANDL_API_KEY']
```

### 1. Collect data from the Franfurt Stock Exchange, for the ticker AFX_X, for the whole year 2017 (keep in mind that the date format is YYYY-MM-DD).


```python
# Set up URL info
from requests import request

url_info = {
    'database_code': 'FSE',
    'dataset_code': 'AFX_X',
    'filetype': 'json',
}

url = f"https://www.quandl.com/api/v3/datasets/{url_info['database_code']}/{url_info['dataset_code']}.{url_info['filetype']}"

# Set up params according to https://docs.quandl.com/docs/parameters-2
params = {
    'start_date': '2017-01-01',
    'end_date': '2018-01-01',
    'order': 'asc',
    'collapse': 'daily',
    'api_key': API_KEY,
}

r = request('GET', url, params=params)

data = r.json()
# data['dataset']['data'][0] # Checking data
```

### 2. Convert the returned JSON object into a Python dictionary.


```python
type(data)
```




    dict




```python
# Column	Description
# Date	Date of the trading day
# Open	Price of the first regular trade of the day
# High	Highest price of the trading day
# Low	Lowest price of the trading day
# Close	Official closing price of the trading day
# Volume	Volume of trades for the day
# Adjustment Factor	The factor by which historical share prices/volumes are adjusted. This field is populated only in the adjusted time-series.
# Adjustment Type	A numeric code (integer) corresponding to the corporate action that precipitated adjustment, such as dividend, consolidation, etc. If more than one corporate action occurs for the day, the individual codes are combined. For more details, see the Adjustment Types section below.

for k,v in enumerate(data['dataset']['column_names']):
    print(k,v)
```

    0 Date
    1 Open
    2 High
    3 Low
    4 Close
    5 Change
    6 Traded Volume
    7 Turnover
    8 Last Price of the Day
    9 Daily Traded Units
    10 Daily Turnover


### 3. Calculate what the highest and lowest opening prices were for the stock in this period.


```python
d = data['dataset']['data']
open_data = [x[1] for x in d if x[1]]

max(open_data), min(open_data)

# Another way to accomplish the same thing:
# open_data.sort()
# open_data[-1]
```




    (53.11, 34.0)



### 4. What was the largest change in any one day (based on High and Low price)?


```python
high_low_diff = [x[2]-x[3] for x in d if x[2] and x[3]]

round(
    max(high_low_diff), 2
)
```




    2.81



### 5. What was the largest change between any two days (based on Closing Price)?


```python
# Basic loop

max_value = 0.0
counter = 0 # 255**2 (255 entries) = 65025
for x in d:
    if not x:
        continue
    for y in d:
        if not y:
            continue
        value = abs(y[4]-x[4])
        # print(value)
        counter += 1
        if value > max_value:
            max_value = value

max_value, counter
```




    (19.03, 65025)




```python
# Using enumerate
max_value = 0.0

for k,v in enumerate(d):
    for i in range(len(d)):
        val = abs(v[4] - d[i][4])
        if val > max_value:
            max_value = val

max_value
```




    19.03



### 6. What was the average daily trading volume during this year?


```python
tvol_list = [x[6] for x in d if x[6]]
ave = sum(tvol_list)/len(tvol_list)

round(ave, 2)
```




    89124.34




```python
# Using statistics built-in
from statistics import mean

round(
    mean(tvol_list), 2
)
```




    89124.34



### 7. (Optional) What was the median trading volume during this year. (Note: you may need to implement your own function for calculating the median.)


```python
def my_median(in_list):
    if len(in_list) % 2 == 0:
        median_tuple = (
            in_list[len(in_list)//2 - 1], 
            in_list[len(in_list)//2],
        )
        median_val = sum(median_tuple)/2
        return median_val
    else:
        return in_list[len(in_list)//2]

tvol_list.sort()

my_median(tvol_list)
```




    76286.0




```python
from statistics import median

round(
    median(tvol_list), 2
)
```




    76286.0




```python
# Test case
import random

random_list_even = random.sample(range(10,100), 10)
random_list_odd = random.sample(range(10,100), 9)

# Even test
random_list_even.sort()
print(random_list_even)
assert(median(random_list_even) == my_median(random_list_even)) # True
assert(median(random_list_even) != my_median(random_list_even[:-1])) # Fails

# Odd test
random_list_odd.sort()
print(random_list_odd)
assert(median(random_list_odd) == my_median(random_list_odd)) # True
assert(median(random_list_odd) != my_median(random_list_odd[:-1])) # Fails

```

    [11, 18, 36, 45, 51, 60, 66, 71, 75, 89]
    [23, 35, 43, 50, 51, 64, 73, 76, 98]

