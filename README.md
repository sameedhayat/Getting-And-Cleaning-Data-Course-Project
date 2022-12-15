# Getting-And-Cleaning-Data-Course-Project
========================================
1. First I loaded the train and test data from train and test folders.
I merged X_test,y_test and subject_test files from test folder columnwise
I merged X_train,y_train and subject_train files from train folder columnwise
Then I merged the merged train and test data columnwise

2. For Step 2 I load the features.txt file.I then used grep to find out -mean() and -std() from feature data
then I used the indexes that I got from grep to get specific features
I also used these indexes to get the specific columns from merged data

3. For step 3 I assigned the values to the rows of activity label using for loop

4. For step 4 I used the names i extracted from features and assign them
to the merged data.For making these column names descriptive
I used gsub to replace specific patterns like I replaced "_"with "",
"BodyAcc" with "BodyAcceleration","mean()" with "Mean" etc.

5. For step 5 I used ddply to average the data by activityLabel and subjectLabel.

## All the code is explained below.

### Setting UCI HAR Dataset as Working Directory 
```r
setwd("./UCI HAR Dataset")
```


### Setting test as Working Directory 
```r
setwd("./test")
```

### Reading test files(X_test,y_test and subject_test)
```r
d1 <- read.table("X_test.txt",header=FALSE)
d2 <- read.table("y_test.txt",header=FALSE)
d3 <- read.table("subject_test.txt",header=FALSE)
```
### Binding X_test,y_test and subject_test by column
```r
test <- cbind(d1,d2)
test <- cbind(test,d3)
```

### Setting train as Working Directory 

```r
setwd("..")
setwd("./train")
```

### Reading test files(X_train,y_train and subject_train)

```r
d1 <- read.table("X_train.txt",header=FALSE)
d2 <- read.table("y_train.txt",header=FALSE)
d3 <- read.table("subject_train.txt",header=FALSE)
```

### Binding X_train,y_train and subject_train by column
```r
train <- cbind(d1,d2)
train <- cbind(train,d3)
```

## Step 1. Merges the training and the test sets to create one data set.
### Merging test and train data by row
```r
mer <- rbind(test,train)
```

### Setting Working Directory for reading features
```r
setwd("..")
```

### Reading features
```r
feature <- read.table("features.txt",header=FALSE)
```
## Step 2:Extracts only the measurements on the mean and standard deviation for each measurement

### Using grep and regular expression to look for mean() and std()

```r
c <- grep("-mean\\(\\)|std\\(\\)",feature$V2)
```

### From feature extracting the rows that contain mean() or std()
```r
name <- feature[c,]
```
### Appending extra columns for activityLabel and subjectLabel 
```r
cappend<- append(c,562)
cappend<- append(cappend,563)
```
### Selecting the columns having mean() or std() and also activityLabel and subjectLabel columns
```r
selectedcol <- mer[,cappend]
```

### Giving names to activity label and subject label columns
```r
names(selectedcol)[67] <-paste("activityLabel")
names(selectedcol)[68] <-paste("subjectLabel")
```


## Step 3:Uses descriptive activity names to name the activities in the data set
### Giving descriptive activity names for activityLabel
```r
for(i in seq_along(selectedcol$activityLabel)){
  if(selectedcol$activityLabel[i]==1){selectedcol$activityLabel[i]<-"Walking"}
  if(selectedcol$activityLabel[i]==2){selectedcol$activityLabel[i]<-"WalkingUpstairs"}
  if(selectedcol$activityLabel[i]==3){selectedcol$activityLabel[i]<-"WalkingDownstairs"}
  if(selectedcol$activityLabel[i]==4){selectedcol$activityLabel[i]<-"Sitting"}
  if(selectedcol$activityLabel[i]==5){selectedcol$activityLabel[i]<- "Standing"}
  if(selectedcol$activityLabel[i]==6){selectedcol$activityLabel[i]<-"Laying"}
}
```
## Step 4 :Appropriately labels the data set with descriptive variable names
### Giving column names using feature data
```r
for(i in seq_along(c)){
  names(selectedcol)[i] <- paste(name$V2[i])
  i=i+1
}
```

### Making column names descriptive
```r
names(selectedcol) <-gsub("BodyBody","Body",names(selectedcol))
names(selectedcol) <-gsub("BodyAcc","BodyAcceleration",names(selectedcol))
names(selectedcol) <- gsub("mean\\(\\)","Mean",names(selectedcol))
names(selectedcol) <- gsub("GravityAcc","GravityAcceleration",names(selectedcol))
names(selectedcol) <- gsub("BodyGyro","BodyGyroscope",names(selectedcol))
names(selectedcol) <- gsub("^t","Time",names(selectedcol))
names(selectedcol) <- gsub("^f","Fourier",names(selectedcol))
names(selectedcol) <- gsub("std\\(\\)","StandardDeviation",names(selectedcol))
names(selectedcol) <- gsub("Mag","Magnitude",names(selectedcol))
names(selectedcol) <- gsub("-","",names(selectedcol))
```
### Using plyr package
```r
library('plyr')
```
## Step # 5 :Creates a second, independent tidy data set with the 
### average of each variable for each activity and each subject
### Grouping to get tidy dataset
```r
tidyData <- ddply(selectedcol, .(activityLabel,subjectLabel), numcolwise(mean))
```
### Writing Data.frame to table
```r
write.table(tidyData,"tidy.txt")
```
