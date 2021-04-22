# bigdata-final-project
## Process text using Databricks Community Edition and PySpark:
## About me:
- Swaroopa Tirumalareddy
## Text Data:
![Sacred Books of the East by Aśvaghosha](https://www.gutenberg.org/cache/epub/12894/pg12894.txt)
## Tools and Languages:
- Databricks Cloud Environment
- Spark Processing Engine
- PySpark API
- Python Programming Language
- Word cloud
## The Process and Commands :
### Gathering the Data:
- Get text data from url
```
import urllib.request
urllib.request.urlretrieve("https://www.gutenberg.org/cache/epub/12894/pg12894.txt" , "/tmp/swaroopa.txt")
```

- moved file from tmp folder to data folder of dbfs
```
dbutils.fs.mv("file:/tmp/swaroopa.txt","dbfs:/data/swaroopa.txt")
```

- transfer the data file into Spark
```
swaroopaRDD= sc.textFile("dbfs:/data/swaroopa.txt")
```

### Cleaning the data:
```
swaroopaTokensRDD = swaroopaRDD.flatMap(lambda line: line.lower().strip().split(" "))
 ```
 ```
 import re
swaroopaRDD = swaroopaTokensRDD.map(lambda letter: re.sub(r'[^A-Za-z]', '', letter))
 ```
 
 - remove stop words
```
from pyspark.ml.feature import StopWordsRemover
remover = StopWordsRemover()
stopwords = remover.getStopWords()
swaroopaWordsRDD = swaroopaTokensRDD.filter(lambda PointLessW: PointLessW not in stopwords)
```
- remove empty spaces
```
swaroopaRemoveRDD = swaroopaWordsRDD.filter(lambda x: x != "")
```
### Processing the Data
- map words to immediate keyvlue pairs
```
swaroopaPairsRDD = swaroopaRemoveRDD.map(lambda word: (word,1))
```
- tranform pairs to word count
```
swaroopaWordCountRDD = swaroopaPairsRDD.reduceByKey(lambda acc, value: acc + value)
```
- sort words in decending order
```
swaroopaResults = swaroopaWordCountRDD.map(lambda x: (x[1], x[0])).sortByKey(False).take(10)
print(swaroopaResults)
```
## Charting the Results
- visualize the results
```
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

source = 'The Project Gutenberg eBook, Sacred Books of the East, by Various, et al'
title = 'Top Words in ' + source
xlabel = 'Count'
ylabel = 'Words'

df = pd.DataFrame.from_records(swaroopaResults, columns =[xlabel, ylabel]) 
plt.figure(figsize=(20,4))
sns.barplot(xlabel, ylabel, data=df, palette="rainbow").set_title(title)
```
## Results:

* ![Result](https://github.com/swaroopatirumalareddy/bigdata-final-project/blob/main/bigdat.PNG)
## References:
- https://spark.apache.org/
- https://www.tutorialspoint.com/pyspark/index.htm
