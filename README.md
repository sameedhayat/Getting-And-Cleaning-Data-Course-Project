Getting-And-Cleaning-Data-Course-Project
========================================

##Setting UCI HAR Dataset as Working Directory 
setwd("./UCI HAR Dataset")

##Setting test as Working Directory 
setwd("./test")

##Reading test files(X_test,y_test and subject_test)
d1 <- read.table("X_test.txt",header=FALSE)
d2 <- read.table("y_test.txt",header=FALSE)
d3 <- read.table("subject_test.txt",header=FALSE)

##Binding X_test,y_test and subject_test by column
test <- cbind(d1,d2)
test <- cbind(test,d3)

##Setting train as Working Directory 
setwd("..")
setwd("./train")

##Reading test files(X_train,y_train and subject_train)
d1 <- read.table("X_train.txt",header=FALSE)
d2 <- read.table("y_train.txt",header=FALSE)
d3 <- read.table("subject_train.txt",header=FALSE)

##Binding X_train,y_train and subject_train by column
train <- cbind(d1,d2)
train <- cbind(train,d3)

##Step # 1,Merges the training and the test sets to create one data set.
##Merging test and train data by row
mer <- rbind(test,train)

##Setting Working Directory for reading features
setwd("..")

##Reading features
feature <- read.table("features.txt",header=FALSE)

##Step 2:Extracts only the measurements on the mean and standard deviation for each measurement
##Using grep and regular expression to look for mean() and std()
c <- grep("-mean\\(\\)|std\\(\\)",feature$V2)

##From feature extracting the rows that contain mean() or std()
name <- feature[c,]

##appending extra columns for activityLabel and subjectLabel 
cappend<- append(c,562)
cappend<- append(cappend,563)

##Selecting the columns having mean() or std() and also activityLabel and subjectLabel columns
selectedcol <- mer[,cappend]

##Step 3:Uses descriptive activity names to name the activities in the data set
##Giving column names using feature data
for(i in seq_along(c)){
  names(selectedcol)[i] <- paste(name$V2[i])
  i=i+1
}

##Making column names descriptive
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

##Giving names to activity label and subject label columns
names(selectedcol)[67] <-paste("activityLabel")
names(selectedcol)[68] <-paste("subjectLabel")

##Step 4 :Appropriately labels the data set with descriptive variable names
##Giving descriptive variable names for activityLabel
for(i in seq_along(selectedcol$activityLabel)){
  if(selectedcol$activityLabel[i]==1){selectedcol$activityLabel[i]<-"Walking"}
  if(selectedcol$activityLabel[i]==2){selectedcol$activityLabel[i]<-"WalkingUpstairs"}
  if(selectedcol$activityLabel[i]==3){selectedcol$activityLabel[i]<-"WalkingDownstairs"}
  if(selectedcol$activityLabel[i]==4){selectedcol$activityLabel[i]<-"Sitting"}
  if(selectedcol$activityLabel[i]==5){selectedcol$activityLabel[i]<- "Standing"}
  if(selectedcol$activityLabel[i]==6){selectedcol$activityLabel[i]<-"Laying"}
}

##Using plyr package
library('plyr')

##Step # 5 :Creates a second, independent tidy data set with the 
##average of each variable for each activity and each subject
##Grouping to get tidy dataset
tidyData <- ddply(selectedcol, .(activityLabel,subjectLabel), numcolwise(mean))

##Writing Data.frame to table
write.table(tidyData,"tidy.txt")
