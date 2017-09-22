# gettingcleaningdataQuiz3
##R script for quiz 3 in Coursera Getting and Cleaning Data course

#1
The American Community Survey distributes downloadable data about United States communities. Download the 2006 microdata survey about housing for the state of Idaho using download.file() from here: 
https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06hid.csv
and load the data into R. The code book, describing the variable names is here:
https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FPUMSDataDict06.pdf

Create a logical vector that identifies the households on greater than 10 acres who sold more than $10,000 worth of agriculture products. Assign that logical vector to the variable agricultureLogical. Apply the which() function like this to identify the rows of the data frame where the logical vector is TRUE.
which(agricultureLogical)

What are the first 3 values that result?
```
if(!file.exists("./data")) {dir.create("./data")}
Url1 = "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06hid.csv"
download.file(Url1, destfile = "./data/community.csv", method = "curl")
community = read.csv("./data/community.csv")

agricultureLogical <- community$ACR > 2 & community$AGS > 5
which(agricultureLogical)
```
##2

Using the jpeg package read in the following picture of your instructor into R

https://d396qusza40orc.cloudfront.net/getdata%2Fjeff.jpg

Use the parameter native=TRUE. What are the 30th and 80th quantiles of the resulting data? (some Linux systems may produce an answer 638 different for the 30th quantile)
```
library(jpeg)
if(!file.exists("./data")) {dir.create("./data")}
Url2 = "https://d396qusza40orc.cloudfront.net/getdata%2Fjeff.jpg"
download.file(Url2, destfile = "./data/jeff.jpg", method = "curl")
jeff = readJPEG("./data/jeff.jpg", native = TRUE)

quantile(jeff, probs = c(.3,.8))
```
##3
Load the Gross Domestic Product data for the 190 ranked countries in this data set:
  
https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FGDP.csv

Load the educational data from this data set:
  
https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FEDSTATS_Country.csv

Match the data based on the country shortcode. How many of the IDs match? Sort the data frame in descending order by GDP rank (so United States is last). What is the 13th country in the resulting data frame?
Original data sources:
  
http://data.worldbank.org/data-catalog/GDP-ranking-table
http://data.worldbank.org/data-catalog/ed-stats

```
##download/read file
library(dplyr)
if(!file.exists("./data")) {dir.create("./data")}
fileUrl1 = "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FGDP.csv"
fileUrl2 = "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FEDSTATS_Country.csv"
download.file(fileUrl1, destfile = "./data/gdp.csv", method = "curl")
download.file(fileUrl2, destfile = "./data/educational.csv", method = "curl")
## first 4 rows were blank. According to the source, there are 217 economies (plus a header)
GDP = read.csv("./data/gdp.csv", skip = 4, nrows = 218); Ed_Data <- read.csv("./data/educational.csv")

#subset "CountryCode" to remove NAs, also remove blanks
GDP <- subset(GDP, !is.na(X) & X!= "", select = c("X", "X.1", "X.3", "X.4"))
GDP <- dplyr::rename(GDP, CountryCode = X)

#merge two data sets
Merged <- merge(GDP, Ed_Data, all=TRUE, by.x = "CountryCode")

#find how many ids matched
num_matches <- sum(!is.na(unique(Merged$X.1)))

arranged_data <- arrange(Merged, desc(X.1))
#country number 13
arranged_data[13, "Long.Name"]
```

##4
What is the average GDP ranking for the "High income: OECD" and "High income: nonOECD" group?
```
#First group
high_inc_OECD <- filter(Merged, Income.Group == "High income: OECD")
mean(high_inc_OECD$X.1)

#Second group
high_inc_nonOECD <- filter(Merged, Income.Group == "High income: nonOECD")
mean(high_inc_nonOECD$X.1, na.rm = T)
```

#5
Cut the GDP ranking into 5 separate quantile groups. Make a table versus Income.Group. How many countries are Lower middle income but among the 38 nations with highest GDP?
```
##Did a simple filter rather than a table
GDPRank_top38 <- filter(Merged, X.1 <= 38)
low_mid_top38 <- filter(GDPRank_top38, Income.Group == "Lower middle income")

nrow(low_mid_top38)
```
