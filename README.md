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
# 將dfs中，row長度介於5~11的table合併（這些才是我們需要的table，其他table不需要）
df = pd.concat([df for df in dfs if df.shape[1] <= 11 and df.shape[1] > 5])

# 設定column名稱
df.columns = df.columns.get_level_values(1)

# 將 df 中的當月營收用 .to_numeric 變成數字，再把其中不能變成數字的部分以 NaN 取代（errors='coerce'）
df['當月營收'] = pd.to_numeric(df['當月營收'], 'coerce')

# 再把當月營收中，出現 NaN 的 row 用 .dropna 整行刪除
df = df[~df['當月營收'].isnull()]

# 刪除「公司代號」中出現「合計」的行數，其中「～」是否定的意思
df = df[df['公司代號'] != '合計']

# 將「公司代號」與「公司名稱」共同列為 df 的 indexes
df = df.set_index(['公司代號', '公司名稱'])

df.head()
