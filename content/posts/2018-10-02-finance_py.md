---
toc: true
layout: post
description: Python workflow for applying deep learning on stock market tickers
categories: [markdown]
title: Python for finance
---
# Python for finance

### Uses pandas, matplotlib, mpl_finance, scikit-learn and the AlphaVantage Stock Market API
#### Written entirely in python.

```python
import bs4 as bs
import pickle
import requests
import os
import pandas_datareader.data as web
import datetime as dt
import matplotlib.pyplot as plt
import datetime as dt
import numpy as np
import csv
import urllib.request, json
import pandas as pd

'''def save_sensex_tickers():
    resp = requests.get('https://en.wikipedia.org/wiki/BSE_SENSEX')
    soup = bs.BeautifulSoup(resp.text, 'lxml')
    table = soup.find('table', {'class': 'wikitable sortable'})
    tickers = []
    for row in table.findAll('tr')[1:]:
        ticker = row.findAll('td')[1].text.replace('.','-')
        tickers.append(ticker)
    with open("sensextickers.pickle","wb") as f:
        pickle.dump(tickers,f)
    print(tickers)
    return tickers
save_sensex_tickers()   
'''
# save sensex_tickers()
def convert_to_list():
    with open('ListOfScripsFull.csv', 'r') as f:
        reader = csv.reader(f)
        scrips = list(reader)
    tickers = []
    for i in range(len(scrips)):

        tickers.append(scrips[i][1])

    del tickers[0]
    for ticker in tickers:
        ticker = ticker.replace('.','-').strip()
    tickers = ['BSE:'+ x for x in tickers]

    outfile = open("bsetickers.pickle","wb")
    pickle.dump(tickers,outfile)
    outfile.close()
    return tickers

#convert_to_list()
def get_data_from_av(reload_sensex=False):
    if reload_sensex:
        tickers = convert_to_list()
    else:
        with open("bsetickers.pickle", "rb") as f:
            tickers = pickle.load(f)

    if not os.path.exists('stocks_dfs'):
        os.makedirs('stocks_dfs')

    start = dt.datetime(2019, 6, 12)
    end = dt.datetime.now()
    api_key = 'ZV3NL5EA4BW0OKYT'
    tick_names = [s.replace(':', '-') for s in tickers]
    

    for ticker in tickers:
        url_string = "https://www.alphavantage.co/query?function=TIME_SERIES_DAILY&symbol=%s&outputsize=full&apikey=%s"%(ticker,api_key)
        file_to_save = 'stock_dfs/%s.csv'%ticker.replace(':','-')
        print(ticker)

        if not os.path.exists(file_to_save):
            with urllib.request.urlopen(url_string) as url:
                data = json.loads(url.read().decode())
            # extract stock market data
            data = data['Time Series (Daily)']
            df = pd.DataFrame(columns=['Date','Low','High','Close','Open'])
            for k,v in data.items():
                date = dt.datetime.strptime(k, '%Y-%m-%d')
                data_row = [date.date(),float(v['3. low']),float(v['2. high']),
                            float(v['4. close']),float(v['1. open'])]
                df.loc[-1,:] = data_row
                df.index = df.index + 1
            print('Data saved to : %s'%file_to_save)        
            df.to_csv(file_to_save)

        

get_data_from_av(True)
```
```python
import datetime as dt
import matplotlib.pyplot as plt
from matplotlib import style
import pandas as pd
import pandas_datareader.data as web
import matplotlib.dates as mdates
from mpl_finance import candlestick_ohlc
style.use('ggplot')

start = dt.datetime(2019, 1 ,1)
end = dt.datetime.now()

df = web.DataReader("AAPL", 'av-daily', start, end, access_key='xxxxx')

df = pd.read_csv('tsla.csv', parse_dates=True, index_col=0)
df['100ma'] = df['close'].rolling(window=10, min_periods=0).mean()
print(df.head())
"""ax1 = plt.subplot2grid((6,1), (0,0), rowspan=5, colspan=1)
ax2 = plt.subplot2grid((6,1), (5,0), rowspan=1, colspan=1, sharex=ax1)
ax1.plot(df.index, df['close'])
ax1.plot(df.index, df['100ma'])
ax2.bar(df.index, df['volume'])
"""
df_ohlc = df['close'].resample('10D').ohlc()
df_volume = df['volume'].resample('10D').sum()

df_ohlc.reset_index(inplace=True)

df_ohlc['date'] = df_ohlc['date'].map(mdates.date2num)


ax1 = plt.subplot2grid((6,1), (0,0), rowspan=5, colspan=1)
ax2 = plt.subplot2grid((6,1), (5,0), rowspan=1, colspan=1, sharex=ax1)
ax1.xaxis_date()

candlestick_ohlc(ax1, df_ohlc.values, width=5, colorup='g')
ax2.fill_between(df_volume.index.map(mdates.date2num),df_volume.values,0)
plt.show()
```

Example: AAPL Stock Price

date | open | high | low | close | volume
---- | ---- | ---- | --- | ----- | ------
02-01-2019 | 154.89 | 158.85 | 154.23 | 157.92 | 37039737
03-01-2019 | 143.98 | 145.72 | 142 | 142.19 | 91312195
04-01-2019 | 144.53 | 148.5499 | 143.8 | 148.26 | 58607070
07-01-2019 | 148.7 | 148.83 | 145.9 | 147.93 | 54777764
08-01-2019 | 149.56 | 151.82 | 148.52 | 150.75 | 41025314
09-01-2019 | 151.29 | 154.53 | 149.63 | 153.31 | 45099081
10-01-2019 | 152.5 | 153.97 | 150.86 | 153.8 | 35780670
11-01-2019 | 152.88 | 153.7 | 151.51 | 152.29 | 27023241
14-01-2019 | 150.85 | 151.27 | 149.22 | 150 | 32439186
15-01-2019 | 150.27 | 153.39 | 150.05 | 153.07 | 28710324
16-01-2019 | 153.08 | 155.88 | 153 | 154.94 | 30569706
17-01-2019 | 154.2 | 157.66 | 153.26 | 155.86 | 29821160
18-01-2019 | 157.5 | 157.88 | 155.9806 | 156.82 | 33751023
22-01-2019 | 156.41 | 156.73 | 152.62 | 153.3 | 30393970
23-01-2019 | 154.15 | 155.14 | 151.7 | 153.92 | 23130570
24-01-2019 | 154.11 | 154.48 | 151.74 | 152.7 | 25441549
25-01-2019 | 155.48 | 158.13 | 154.32 | 157.76 | 33547893
28-01-2019 | 155.79 | 156.33 | 153.66 | 156.3 | 26192058
29-01-2019 | 156.25 | 158.13 | 154.11 | 154.68 | 41587239
30-01-2019 | 163.25 | 166.15 | 160.23 | 165.25 | 61109780
31-01-2019 | 166.11 | 169 | 164.56 | 166.44 | 40739649
01-02-2019 | 166.96 | 168.98 | 165.93 | 166.52 | 32668138
04-02-2019 | 167.41 | 171.655 | 167.28 | 171.25 | 31495582
05-02-2019 | 172.86 | 175.08 | 172.3501 | 174.18 | 36101628
06-02-2019 | 174.65 | 175.57 | 172.8531 | 174.24 | 28239591
07-02-2019 | 172.4 | 173.94 | 170.34 | 170.94 | 31741690
08-02-2019 | 168.99 | 170.66 | 168.42 | 170.41 | 23819966
11-02-2019 | 171.05 | 171.21 | 169.25 | 169.43 | 20993425
12-02-2019 | 170.1 | 171 | 169.7 | 170.89 | 22283523
13-02-2019 | 171.39 | 172.48 | 169.92 | 170.18 | 22490233
14-02-2019 | 169.71 | 171.2615 | 169.38 | 170.8 | 21835747
15-02-2019 | 171.25 | 171.7 | 169.75 | 170.42 | 24626814
19-02-2019 | 169.71 | 171.44 | 169.49 | 170.93 | 18972826
20-02-2019 | 171.19 | 173.32 | 170.99 | 172.03 | 26114362
21-02-2019 | 171.8 | 172.37 | 170.3 | 171.06 | 17249670
22-02-2019 | 171.58 | 173 | 171.38 | 172.97 | 18913154
25-02-2019 | 174.16 | 175.87 | 173.95 | 174.23 | 21873358
26-02-2019 | 173.71 | 175.3 | 173.1732 | 174.33 | 17070211
27-02-2019 | 173.21 | 175 | 172.73 | 174.87 | 27835389
28-02-2019 | 174.32 | 174.91 | 172.92 | 173.15 | 28215416
01-03-2019 | 174.28 | 175.15 | 172.89 | 174.97 | 25886167
04-03-2019 | 175.69 | 177.75 | 173.97 | 175.85 | 27436203
05-03-2019 | 175.94 | 176 | 174.54 | 175.53 | 19737419
06-03-2019 | 174.67 | 175.49 | 173.94 | 174.52 | 20810384
07-03-2019 | 173.87 | 174.44 | 172.02 | 172.5 | 24796374
08-03-2019 | 170.32 | 173.07 | 169.5 | 172.91 | 23999358
11-03-2019 | 175.49 | 179.12 | 175.35 | 178.9 | 32011034
12-03-2019 | 180 | 182.67 | 179.37 | 180.91 | 32467584
13-03-2019 | 182.25 | 183.3 | 180.92 | 181.71 | 31032524
14-03-2019 | 183.9 | 184.1 | 182.56 | 183.73 | 23579508
15-03-2019 | 184.85 | 187.33 | 183.74 | 186.12 | 39042912
18-03-2019 | 185.8 | 188.39 | 185.79 | 188.02 | 26219832
19-03-2019 | 188.35 | 188.99 | 185.92 | 186.53 | 31646369
20-03-2019 | 186.23 | 189.49 | 184.73 | 188.16 | 31035231
21-03-2019 | 190.02 | 196.33 | 189.81 | 195.09 | 51034237
22-03-2019 | 195.34 | 197.69 | 190.78 | 191.05 | 42407666
25-03-2019 | 191.51 | 191.98 | 186.6 | 188.74 | 43845293
26-03-2019 | 191.664 | 192.88 | 184.58 | 186.79 | 49800538
27-03-2019 | 188.75 | 189.76 | 186.55 | 188.47 | 29848427
28-03-2019 | 188.95 | 189.559 | 187.53 | 188.72 | 20780363
29-03-2019 | 189.83 | 190.08 | 188.54 | 189.95 | 23563961
01-04-2019 | 191.64 | 191.68 | 188.38 | 191.24 | 27861964
02-04-2019 | 191.09 | 194.46 | 191.05 | 194.02 | 22765732
03-04-2019 | 193.25 | 196.5 | 193.15 | 195.35 | 23271830
04-04-2019 | 194.79 | 196.37 | 193.14 | 195.69 | 19114275
05-04-2019 | 196.45 | 197.1 | 195.93 | 197 | 18526644
08-04-2019 | 196.42 | 200.23 | 196.34 | 200.1 | 25881697
09-04-2019 | 200.32 | 202.85 | 199.23 | 199.5 | 35768237
10-04-2019 | 198.68 | 200.74 | 198.18 | 200.62 | 21695288
11-04-2019 | 200.85 | 201 | 198.4431 | 198.95 | 20900808
12-04-2019 | 199.2 | 200.14 | 196.21 | 198.87 | 27760668
15-04-2019 | 198.58 | 199.85 | 198.01 | 199.23 | 17536646
16-04-2019 | 199.46 | 201.37 | 198.56 | 199.25 | 25696385
17-04-2019 | 199.54 | 203.38 | 198.61 | 203.13 | 28906780
18-04-2019 | 203.12 | 204.15 | 202.52 | 203.86 | 24195766
22-04-2019 | 202.83 | 204.94 | 202.34 | 204.53 | 19439545
23-04-2019 | 204.43 | 207.75 | 203.9 | 207.48 | 23322991
24-04-2019 | 207.36 | 208.48 | 207.05 | 207.16 | 17540609
25-04-2019 | 206.83 | 207.76 | 205.12 | 205.28 | 18543206
26-04-2019 | 204.9 | 205 | 202.12 | 204.3 | 18649102
29-04-2019 | 204.4 | 205.97 | 203.86 | 204.61 | 22204716
30-04-2019 | 203.06 | 203.4 | 199.11 | 200.67 | 46534923
01-05-2019 | 209.88 | 215.31 | 209.23 | 210.52 | 64827328
02-05-2019 | 209.84 | 212.65 | 208.13 | 209.15 | 31996324
03-05-2019 | 210.89 | 211.84 | 210.23 | 211.75 | 20892378
06-05-2019 | 204.29 | 208.84 | 203.5 | 208.48 | 32443113
07-05-2019 | 205.88 | 207.4175 | 200.825 | 202.86 | 38763698
08-05-2019 | 201.9 | 205.34 | 201.75 | 202.9 | 26339504
09-05-2019 | 200.4 | 201.68 | 196.66 | 200.72 | 34908607
10-05-2019 | 197.419 | 198.85 | 192.77 | 197.18 | 41208712
13-05-2019 | 187.71 | 189.48 | 182.85 | 185.72 | 57430623
14-05-2019 | 186.41 | 189.7 | 185.41 | 188.66 | 36529677
15-05-2019 | 186.27 | 191.75 | 186.02 | 190.92 | 26544718
16-05-2019 | 189.91 | 192.4689 | 188.84 | 190.08 | 33031364
17-05-2019 | 186.93 | 190.9 | 186.76 | 189 | 32879090
20-05-2019 | 183.52 | 184.349 | 180.2839 | 183.09 | 38612290
21-05-2019 | 185.22 | 188 | 184.7 | 186.6 | 28364848
22-05-2019 | 184.66 | 185.71 | 182.55 | 182.78 | 29748556
23-05-2019 | 179.8 | 180.54 | 177.81 | 179.66 | 36529736
24-05-2019 | 180.2 | 182.14 | 178.62 | 178.97 | 23714686
28-05-2019 | 178.92 | 180.59 | 177.91 | 178.23 | 27948160
29-05-2019 | 176.42 | 179.35 | 176 | 177.38 | 28481165
30-05-2019 | 177.95 | 179.23 | 176.67 | 178.3 | 21218412
31-05-2019 | 176.23 | 177.99 | 174.99 | 175.07 | 27043584
03-06-2019 | 175.6 | 177.92 | 170.27 | 173.3 | 40396069
04-06-2019 | 175.44 | 179.83 | 174.52 | 179.64 | 30967961
05-06-2019 | 184.28 | 184.99 | 181.14 | 182.54 | 29773427
06-06-2019 | 183.08 | 185.47 | 182.1489 | 185.22 | 22526311
07-06-2019 | 186.51 | 191.92 | 185.77 | 190.15 | 30684393
10-06-2019 | 191.81 | 195.37 | 191.62 | 192.58 | 26220851
11-06-2019 | 194.86 | 196 | 193.6 | 194.81 | 25020913
