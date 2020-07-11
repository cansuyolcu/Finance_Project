# Finance_Project
In this data project I focuse on exploratory data analysis of stock prices and see how they progressed throughout the [financial crisis](https://en.wikipedia.org/wiki/Financial_crisis_of_2007%E2%80%932008) all the way to early 2016.

## The Imports

```python
from pandas_datareader import data, wb
import pandas as pd
import numpy as np
import datetime
%matplotlib inline
```
## Data

I get stock information using pandas datareader for the following banks:
- Bank of America
- CitiGroup
- Goldman Sachs
- JPMorgan Chase
- Morgan Stanley
- Wells Fargo

I use [this documentation page](https://pandas-datareader.readthedocs.io/en/latest/remote_data.html) to learn how to get the stock data from Jan 1st 2006 to Jan 1st 2016 for each of these banks.
I choose to use Google's stock prices.
```python
start = datetime.datetime(2006, 1, 1)
end = datetime.datetime(2016, 1, 1)
# Bank of America
BAC = data.DataReader("BAC", 'google', start, end)

# CitiGroup
C = data.DataReader("C", 'google', start, end)

# Goldman Sachs
GS = data.DataReader("GS", 'google', start, end)

# JPMorgan Chase
JPM = data.DataReader("JPM", 'google', start, end)

# Morgan Stanley
MS = data.DataReader("MS", 'google', start, end)

# Wells Fargo
WFC = data.DataReader("WFC", 'google', start, end)
```

I create a list of the ticker symbols (as strings) in alphabetical order and call this list: tickers. Then I use pd.concat to concatenate the bank dataframes together to a single data frame called bank_stocks. I set the keys argument equal to the tickers list. And set the column name levels.Now I can check the head of the bank_stocks dataframe.

```python
tickers = ['BAC', 'C', 'GS', 'JPM', 'MS', 'WFC']
bank_stocks = pd.concat([BAC, C, GS, JPM, MS, WFC],axis=1,keys=tickers)
bank_stocks.columns.names = ['Bank Ticker','Stock Info']
bank_stocks.head()
```
<img src= "https://user-images.githubusercontent.com/66487971/87220282-27827700-c36b-11ea-9de3-af03fb34f742.png" width = 1000>

Lets answer some questions, what is the max Close price for each bank's stock throughout the time period?
```python
bank_stocks.xs(key='Close',axis=1,level='Stock Info').max()
```
<img src= "https://user-images.githubusercontent.com/66487971/87220535-3702bf80-c36d-11ea-9d6b-0f0f31e9ef72.png" width = 150>

I create a new empty DataFrame called returns. This dataframe will contain the returns for each bank's stock. returns are typically defined by:

<img src= "https://user-images.githubusercontent.com/66487971/87220561-7df0b500-c36d-11ea-9759-45d09dbe9f38.png" width = 200>

```python
returns = pd.DataFrame()
```
 I use pandas pct_change() method on the Close column to create a column representing this return value. Then I create a for loop that goes and for each Bank Stock Ticker creates this returns column and set's it as a column in the returns DataFrame.

```python
for tick in tickers:
    returns[tick+' Return'] = bank_stocks[tick]['Close'].pct_change()
returns.head()
```
<img src= "https://user-images.githubusercontent.com/66487971/87220616-10915400-c36e-11ea-95de-4e942ecf146e.png" width = 500>

Now I create a pairplot using seaborn of the returns dataframe.
```python
import seaborn as sns
sns.pairplot(returns[1:])
```
<img src= "https://user-images.githubusercontent.com/66487971/87220643-66fe9280-c36e-11ea-8403-b879d2666fe8.png" width = 1000>

We can see the Citigroup's crush from this plot. Which you can find more information [here](https://en.wikipedia.org/wiki/Citigroup#November_2008.2C_Collapse_.26_US_Government_Intervention_.28part_of_the_Global_Financial_Crisis.29)

Using this returns DataFrame, I look at  dates each bank stock had the best and worst single day returns.
```python
returns.idxmin()
```
<img src= "https://user-images.githubusercontent.com/66487971/87220757-77fbd380-c36f-11ea-810b-8185fcb53726.png" width = 150>
```python
returns.idmax()
```
<img src= "https://user-images.githubusercontent.com/66487971/87220906-85fe2400-c370-11ea-99bc-4391e808582a.png" width = 150>

I see that 4 of the banks share the same day for the worst drop. This may be correlated to Obama's inaugural speech.

I also notice that Citigroup's largest drop and biggest gain were very close to one another, and see that [Citigrouo had a stock split](https://www.google.com/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=citigroup+stock+2011+may)
When I check the standard deviation of the returns, I see that Citigroup is the riskiest over the entire time period.
```python
returns.std()
```
<img src= "https://user-images.githubusercontent.com/66487971/87220975-3a984580-c371-11ea-974f-b6117cf62962.png" width = 150>
 
But if I check just the year 2015, I see that they have very similar risk profiles, but Morgan Stanley or BofA may be the riskiest.

```python
returns.ix['2015-01-01':'2015-12-31'].std()
```
<img src= "https://user-images.githubusercontent.com/66487971/87221022-8ba83980-c371-11ea-998b-92eb612d7ece.png" width = 150>






