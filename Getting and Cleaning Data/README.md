# The procedure of my analysis
### 1. Choose the right variables:
My note: Import the dataset "features.txt" and use regular expressions to select means and standard deviations (Be careful, should not choose meanFreq!)

R code: 
```r
name <- read.delim('features.txt',header=FALSE,sep=" ",stringsAsFactors = FALSE)
colid <- grep('mean(?!Freq)|std',name$V2,perl = TRUE)
colname <- grep('mean(?!Freq)|std',name$V2,perl = TRUE,value = TRUE)
```

### 2. Import all needed datasets into R: 
My note: 
* First, import training datasets of features X, labels Y and subject IDs and subsequently merge them into a single data set called "train"
* Second, import testing datasets of features X, labels Y and subject IDs and then merge all of them into a single data set called "test"
* Combine the datasets "train" and "test"

R code: 
```r
testx <- read.table('test/X_test.txt',header=FALSE)
testx <- testx[,colid]
testy <- read.table('test/y_test.txt',header=FALSE)
subtest <- read.table('test/subject_test.txt',header=FALSE)
test <- cbind(subtest,testy,testx)
trainx <- read.table('train/X_train.txt',header=FALSE)
trainx <- trainx[,colid]
trainy <- read.table('train/y_train.txt',header=FALSE)
subtrain <- read.table('train/subject_train.txt',header=FALSE)
train <- cbind(subtrain,trainy,trainx)
har <- rbind(train,test)
```

### 3. Rename all columns: 
```r
names(har) <- c('sub','act',colname)
```

### 4. Transform activity as factor and use its value labels: 
```r
har <- within(har,{act=factor(act,levels=seq(1,6),labels=c('Walking','Walking_Upstairs','Walking_Downstairs','Sitting','Standing','Laying'))})
```

### 5. Transform the tidy dataset use group_by and summarize function: 
```r
har <- as_tibble(har)
har1 <- har%>%group_by(sub,act)
descp <- summarise_all(har1,mean)
write.table(descp,'descp.txt',row.names=FALSE)
```
