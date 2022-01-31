Below table provides the link to the data sources and the time periods during which the anomalous activity (market manupulations) occured. See the paper for more details. 

| Stock Code | Link to Data Source                                 | Anomaly Start | Anomaly End | Anomaly Type |
|------------|-----------------------------------------------------|---------------|-------------|--------------|
| TRBO       | https://finance.yahoo.com/quote/TRBO/history?p=TRBO | 30-Mar-20     | 9-Apr-20    | Point        |
| APPB       | https://finance.yahoo.com/quote/APPB/history?p=APPB | 25-Mar-20     | 13-Apr-20   | Point        |
| AEMD       | https://finance.yahoo.com/quote/AEMD/history?p=AEMD | 22-Jan-20     | 7-Feb-20    | Contextual   |
| NBDR       | https://finance.yahoo.com/quote/NBDR/history?p=NBDR | 11-Mar-20     | 3-Apr-20    | Point        |
| GME        | https://finance.yahoo.com/quote/GME/history?p=GME   | 11-Jan-21     | 29-Jan-21   | Point        |

# Data can be obtained using the following process in Python

Jillian Tallboys, Ye Zhu, and Sutharshan Rajasegarar, Identification of Stock Market Manipulation with Deep Learning, in Proceedings of the International Conference on Advanced Data Mining and Applications (ADMA), Sydney, Australia, February, 2022.

```
# Install
!pip install yfinance

# Imports
import numpy as np
import pandas as pd
import yfinance as yf
from datetime import datetime
import time
import datetime

# Pull ticker
stock = yf.Ticker("AEMD")

# Get historical market data
hist_start = "2018-01-22"
hist_end = "2021-02-06"
stock_hist = stock.history(start = hist_start, end = hist_end )

# Convert dates 
start = time.mktime(datetime.datetime.strptime(hist_start, "%Y-%m-%d").timetuple())
end = time.mktime(datetime.datetime.strptime(hist_end , "%Y-%m-%d").timetuple())

# Set types
stock_hist = stock_hist.reset_index()
for i in ['Open', 'High', 'Close', 'Low', 'Volume']: 
      stock_hist[i]  =  stock_hist[i].astype('float64')

# Prep signal data 
stock_hist['timestamp'] = stock_hist['Date'].astype(np.int64) // 10**9
stock_signal = stock_hist.rename(columns = {"Volume":"value"})[['timestamp','value']]

# Convert known anomaly dates 
anom_start = pd.Timestamp("2020-01-22").timestamp()
anom_end = pd.Timestamp("2020-02-06").timestamp()

# Create anomaly data frame 
stock_anomalies = pd.DataFrame([[int(anom_start), int(anom_end)]], columns = ['start', 'end'])
```
