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


#read mapping file
map<-read.delim("/home/pattyjk/Desktop/lacto_rarity/emp_qiime_mapping_release1.tsv", header=T)

#read in phylum level data
emp_l2<-read.delim("/home/pattyjk/Desktop/lacto_sum2/emp_lacto_L2.txt", header=T)

#change to relative abundance (divding by 10k)
emp_l2$D_0__Bacteria.D_1__Firmicutes<-emp_l2$D_0__Bacteria.D_1__Firmicutes/10000

#get global mean, sd, se, n for relative abundance
mean(emp_l2$D_0__Bacteria.D_1__Firmicutes)
#0.02968796

sd(emp_l2$D_0__Bacteria.D_1__Firmicutes)
#0.09284378

length(emp_l2$D_0__Bacteria.D_1__Firmicutes)
#23323

(sd(emp_l2$D_0__Bacteria.D_1__Firmicutes))/(length(emp_l2$D_0__Bacteria.D_1__Firmicutes))
#3.980782e-06

#merge map to data
emp_meta<-merge(emp_l2, map, by.x='OTU.ID', by.y='SampleID')

#summarize data by the category"env_material", 43 categories
emp_sum<-ddply(emp_meta, c("env_feature"), summarize, n=length(D_0__Bacteria.D_1__Firmicutes), sd=sd(D_0__Bacteria.D_1__Firmicutes), mean=mean(D_0__Bacteria.D_1__Firmicutes), se=sd/n)

#plot means and SD
ggplot(emp_sum, aes(env_feature, mean))+
  geom_point()+
  theme_bw()+
  coord_flip()+
  theme(text = element_text(size=14), axis.text = element_text(size=14), legend.text=element_text(size=14))+
  geom_errorbar(aes(ymin=mean-sd, ymax=mean+sd), width=.2)+
  xlab("EMP Env. Feature Category")+
  ylab("Mean Lactobacillales Relative Abundance")

#box and whisker plot
ggplot(emp_meta, aes(env_feature, D_0__Bacteria.D_1__Firmicutes))+
  geom_boxplot()+
  theme_bw()+
  coord_flip()+
  theme(text = element_text(size=14), axis.text = element_text(size=14), legend.text=element_text(size=14))+
  xlab("EMP Env. Material Category")+
  ylab("Lactobacillales Relative Abundance")

#subset to only include plant and soil
emp_sum_ps<-subset(emp_sum, emp_sum$env_feature == "tropical soil" | emp_sum$env_feature == "surface soil" | emp_sum$env_feature == "steppe soil" | emp_sum$env_feature == "plant-associated habitat" | emp_sum$env_feature == "pasture" | emp_sum$env_feature == "grassland soil" | emp_sum$env_feature == "garden soil" | emp_sum$env_feature == "forest soil" | emp_sum$env_feature == "forest soil" | emp_sum$env_feature == "farm soil" | emp_sum$env_feature == "agricultural soil" )

#add global category
glob<-matrix(c("Global Mean", "23323", "0.09284378", "0.02968796", "3.980782e-06"), nrow=1)
glob<-matrix(c("Global Mean", 23323, 0.09284378, 0.02968796, 3.980782e-06), nrow=1)
glob<-as.data.frame(glob)
names(glob)<-c("env_feature", 'n', 'sd', 'mean', 'se')
write.csv(glob, "glob.csv", row.names = F, quote=F)
glob<-read.csv('glob.csv')
emp_sum_ps<-rbind(emp_sum_ps, glob)

#plot with global mean
ggplot(emp_sum_ps, aes(env_feature, mean, colour=env_feature))+
  geom_point(aes(size=1.1))+
  theme_bw()+
  coord_cartesian(ylim=c(0,0.5))+
  coord_flip()+
  theme(text = element_text(size=14), axis.text = element_text(size=14), legend.text=element_text(size=14))+
  geom_errorbar(aes(ymin=mean-sd, ymax=mean+sd), width=.2)+
  xlab("EMP Env. Feature Category")+
  ylab("Mean Lactobacillales Relative Abundance")+
  scale_colour_manual(values=c("black", "black","black","black","black","black","black","black","black","black","red"))+
  theme(legend.position="none")
  
#write data to file
write.csv(emp_sum_ps, 'emp_sum_ps.csv', row.names = F, quote=F)
write.csv(emp_sum, 'emp_sum.csv', row.names = F, quote=F)
```
