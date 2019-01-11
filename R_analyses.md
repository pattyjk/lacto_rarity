## Analyses in R

### Fix headers of files for use in R 
```
#in linux shell
cd lacto_sum 
for i in *.txt
do
sed -i 's/# Constructed from biom file//' $i
done

for i in *.txt
do
sed -i 's/#//' $i
done
```

### Analyze data in R
```
#R version 3.4.4, 64-bit
library(ggplot2)
library(readr)
library(plyr)
library(reshape2)
library(tidyr)


#read in data
```
