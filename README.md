# cyclecloud
python SQL java C 

import requests
url = 'https://mops.twse.com.tw/nas/t21/sii/t21sc03_108_1_0.html'
r = requests.get(url)
from io import StringIO
import pandas as pd
r.encoding = 'big5'
dfs = pd.read_html(StringIO(r.text))
pd.__version__

df = pd.concat([df for df in dfs if df.shape[1] <= 11 and df.shape[1] > 5])
df.columns = df.columns.get_level_values(1)
df['當月營收'] = pd.to_numeric(df['當月營收'], 'coerce')
df = df[~df['當月營收'].isnull()]
df = df[df['公司代號'] != '合計']
df = df.set_index(['公司代號', '公司名稱'])

df.to_csv('test.csv', encoding = 'utf_8_sig')
df = pd.read_csv('test.csv', index_col = ['公司代號','公司名稱'])
df.head()

import sqlite3

conn = sqlite3.connect('test.sqlite3')
df.to_sql('monthly_report', conn, if_exists = 'replace')

df = pd.read_sql('select * from monthly_report', conn, index_col = ['公司代號','公司名稱'])
df.head()
