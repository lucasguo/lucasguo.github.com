---
layout: post
category : Python
tagline: Cheat Sheet
tags : [webdriver, selenium, mysql, requests, regexp]
---
{% include JB/setup %}

### requests & BeautifulSoup
pip install requests

pip install bs4

```
from bs4 import BeautifulSoup

headers = {"User-Agent": "Mozilla/xxxxxx"}
r = requests.get("https://www.baidu.com", headers=headers);
soup = BeautifulSoup(r.content, "lxml") #r.content为经过gzip解压（如果有压缩的话）并经过header指示编码的内容
#soup = BeautifulSoup(r.text, "lxml") #r原字节流，没有经过gzip解压，没有经过编码
paras = soup.find_all("p")
for para in paras:
	print(t.text.strip())
```

### webdriver
pip install selenium

download geckodriver and put it in PATH

```
from selenium import webdriver

driver = webdriver.Firefox()
driver.get("https://www.baidu.com")
driver.find_element_by_css_selector("p#container > a").text.strip()
```
	
### regexp
```
import re

results = re.find_all(r'[0-9]+', '123 is a good number but 234 is not')
for result in results:
	print(result)
```

### mysql
yum install mysql-devel

pip install MySQLdb

```
import MySQLdb

conn = MySQLdb.Connect(host="localhost", user="root", passwd="root", db="mydb", charset="utf8")
cur = conn.cursor()
cur.execute("""UPDATE TABLE_A
                  SET FIELD_A = %s,
                  SET FIELD_B = %s
                WHERE ID = %s""", ['a', 'b' , 11])
result = cur.execute("""SELECT FIELD_A FROM TABLE_A WHERE ID = %s""", [234]).fetchone()
field_a_value = result[0]
cur.close()
conn.commit()
conn.close()
```
