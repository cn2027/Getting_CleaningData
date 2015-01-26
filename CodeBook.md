#Getting and Cleaning Data: Course Project
========================================================

The data used for this project experiments have been carried out with a group of 30 volunteers within an age bracket of 19-48 years. Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone (Samsung Galaxy S II) on the waist. 

Please utilize this document to better understand the Run_analysis.R script. More details are below.

Create a tidy data set for UCI data
Assign column names for the signals in the Feature Selection file "features.txt"
```{r}
features.signals <- read.csv("/Users/ChannyNic/UCI HAR Dataset/features.txt", header=FALSE,col.names = c('feature.ID', 'signal'),sep = ' ');
```
Assign column names for the activity names in the Activity Labels file "activity_labels.txt"
```{r}
activity.labels <- read.csv("/Users/ChannyNic/UCI HAR Dataset/activity_labels.txt", header=FALSE, col.names = c('activity.ID','activity.Name'),sep = ' ')
```
Read the three test files: "subject_test.txt","X_test.txt", and "y_test.txt" and label columns
'test/subject_test.txt': Each row identifies the subject who performed the activity for each window sample. Its range is from 1 to 30. 
```{r}
subject.test <- read.csv("/Users/ChannyNic/UCI HAR Dataset/test/subject_test.txt", header=FALSE, col.names = c('subject.ID'));
```
'test/X_test.txt': Test set.
```{r}
test.set <- read.table("~/UCI HAR Dataset/test/X_test.txt", quote="\"")
head(test.set)
```
test/y_test.txt': Test labels.
```{r}
test.labels <- read.csv("/Users/ChannyNic/UCI HAR Dataset/test/y_test.txt",header=FALSE, col.names = c('activity.ID'))
```
Appropriately label the data set with descriptive variable names.
```{r}
test.set.names <- features.signals[,2]
colnames(test.set) <- test.set.names
labels.test.set<-cbind(test.labels,test.set)
full.test.set <- cbind(subject.test,labels.test.set)
head(full.test.set)
```
Create/tidy up the trainiing set data.
Read the three training files: "subject_train.txt","X_train.txt", and "y_train.txt" and label columns
'train/subject_train.txt': Each row identifies the subject who performed the activity for each window sample. Its range is from 1 to 30. 
```{r}
subject.train <- read.csv("/Users/ChannyNic/UCI HAR Dataset/train/subject_train.txt", header=FALSE, col.names = c('subject.ID'))
```
'train/X_train.txt': Training set.
```{r}
train.set <- read.table("~/UCI HAR Dataset/train/X_train.txt", quote="\"")
```
'train/y_train.txt': Training labels.
```{r}
train.labels <- read.csv("/Users/ChannyNic/UCI HAR Dataset/train/y_train.txt",header=FALSE, col.names = c('activity.ID'))
```
Appropriately label the data set with descriptive variable names.
```{r}
train.set.names <- features.signals$signal
colnames(train.set) <- train.set.names
labels.train.set<-cbind(train.labels,train.set)
full.train.set <- cbind(subject.train,labels.train.set)
```
Merge the training and the test sets to create one data set.
```{r}
tidy.dataset <- rbind(full.train.set,full.test.set)
```
Use descriptive activity names to name the activities in the data set
```{r}
tidy.dataset$activity.Name<-as.character(activity.labels$activity.Name[match(tidy.dataset$activity.ID,activity.labels$activity.ID)])
head(tidy.dataset)
```
Extract only the measurements on the mean and standard deviation for each measurement. 
```{r}
tidy.mean<-subset(tidy.dataset[,grep("mean",colnames(tidy.dataset))])
head(tidy.mean) #Gives you the columns that have the string mean in it, which corresponds to measurement means that the assignment asked to be reported
tidy.Mean<-subset(tidy.dataset[,grep("mean",colnames(tidy.dataset))])
tidy.std<-subset(tidy.dataset[,grep("mean",colnames(tidy.dataset))])
IDs<-cbind(tidy.dataset$subject.ID,tidy.dataset$activity.ID,tidy.dataset$activity.Name)
colnames(IDs)<-c("subject.ID","activity.ID","activity.Name")
tidy.mean.std<-cbind(IDs,tidy.mean,tidy.Mean,tidy.std) #Ensures that the ID variables are in the dataset
tidy.mean.std$activity.Name<-as.character(activity.labels$activity.Name[match(tidy.mean.std$activity.ID,activity.labels$activity.ID)])
head(tidy.mean.std)
```
From the tidy data set creates a second, independent tidy data set with the average of each variable for each activity and each subject.
```{r}
average.subject.activity<-aggregate(tidy.mean.std, by=list(tidy.mean.std$subject.ID,tidy.mean.std$activity.ID), FUN=mean)
```
Write to file the tidy data sets
```{r}
write.table(tidy.mean.std,"tidy.mean.std.txt",row.name=FALSE)
write.table(average.subject.activity,"average.subject.activity.txt",row.name=FALSE)
```
