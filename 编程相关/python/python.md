

##### post请求 将数据写入文件
```
# -*- coding:utf-8 -*-
import csv
import requests
import codecs

with open("test_jiaohang.csv") as csvfile:
	reader = csv.DictReader(csvfile)
	write_file =  codecs.open("test.txt","a+","utf-8")
	url ="http://192.168.0.88:8088/cebsds/decisions"
	for row in reader:
		r = requests.post(url,data=row)
		write_file.write(r.text+'\n'.encode('utf-8'))
	write_file.close()

```