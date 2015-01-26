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
For each record it is provided:
Triaxial acceleration from the accelerometer (total acceleration) and the estimated body acceleration.
Triaxial Angular velocity from the gyroscope.
A 561-feature vector with time and frequency domain variables.
Its activity label.
An identifier of the subject who carried out the experiment.
The dataset includes the following files:
'README.txt'

'features_info.txt': Shows information about the variables used on the feature vector.

'features.txt': List of all features.

'activity_labels.txt': Links the class labels with their activity name.

'train/X_train.txt': Training set.

'train/y_train.txt': Training labels.

'test/X_test.txt': Test set.

'test/y_test.txt': Test labels.

The following files are available for the train and test data. Their descriptions are equivalent.

'train/subject_train.txt': Each row identifies the subject who performed the activity for each window sample. Its range is from 1 to 30.

'train/Inertial Signals/total_acc_x_train.txt': The acceleration signal from the smartphone accelerometer X axis in standard gravity units 'g'. Every row shows a 128 element vector. The same description applies for the 'total_acc_x_train.txt' and 'total_acc_z_train.txt' files for the Y and Z axis.

'train/Inertial Signals/body_acc_x_train.txt': The body acceleration signal obtained by subtracting the gravity from the total acceleration.

'train/Inertial Signals/body_gyro_x_train.txt': The angular velocity vector measured by the gyroscope for each window sample. The units are radians/second.

Notes:
Features are normalized and bounded within [-1,1].
Each feature vector is a row on the text file.
For more information about this dataset contact: activityrecognition@smartlab.ws

License:
Use of this dataset in publications must be acknowledged by referencing the following publication [1]

[1] Davide Anguita, Alessandro Ghio, Luca Oneto, Xavier Parra and Jorge L. Reyes-Ortiz. Human Activity Recognition on Smartphones using a Multiclass Hardware-Friendly Support Vector Machine. International Workshop of Ambient Assisted Living (IWAAL 2012). Vitoria-Gasteiz, Spain. Dec 2012

This dataset is distributed AS-IS and no responsibility implied or explicit can be addressed to the authors or their institutions for its use or misuse. Any commercial use is prohibited.

Jorge L. Reyes-Ortiz, Alessandro Ghio, Luca Oneto, Davide Anguita. November 2012.

Introduction

This second programming assignment will require you to write an R function that is able to cache potentially time-consuming computations. For example, taking the mean of a numeric vector is typically a fast operation. However, for a very long vector, it may take too long to compute the mean, especially if it has to be computed repeatedly (e.g. in a loop). If the contents of a vector are not changing, it may make sense to cache the value of the mean so that when we need it again, it can be looked up in the cache rather than recomputed. In this Programming Assignment you will take advantage of the scoping rules of the R language and how they can be manipulated to preserve state inside of an R object.

Example: Caching the Mean of a Vector

In this example we introduce the <<- operator which can be used to assign a value to an object in an environment that is different from the current environment. Below are two functions that are used to create a

Feature Selection
The features selected for this database come from the accelerometer and gyroscope 3-axial raw signals tAcc-XYZ and tGyro-XYZ. These time domain signals (prefix 't' to denote time) were captured at a constant rate of 50 Hz. Then they were filtered using a median filter and a 3rd order low pass Butterworth filter with a corner frequency of 20 Hz to remove noise. Similarly, the acceleration signal was then separated into body and gravity acceleration signals (tBodyAcc-XYZ and tGravityAcc-XYZ) using another low pass Butterworth filter with a corner frequency of 0.3 Hz.

Subsequently, the body linear acceleration and angular velocity were derived in time to obtain Jerk signals (tBodyAccJerk-XYZ and tBodyGyroJerk-XYZ). Also the magnitude of these three-dimensional signals were calculated using the Euclidean norm (tBodyAccMag, tGravityAccMag, tBodyAccJerkMag, tBodyGyroMag, tBodyGyroJerkMag).

Finally a Fast Fourier Transform (FFT) was applied to some of these signals producing fBodyAcc-XYZ, fBodyAccJerk-XYZ, fBodyGyro-XYZ, fBodyAccJerkMag, fBodyGyroMag, fBodyGyroJerkMag. (Note the 'f' to indicate frequency domain signals).

These signals were used to estimate variables of the feature vector for each pattern:
'-XYZ' is used to denote 3-axial signals in the X, Y and Z directions.

tBodyAcc-XYZ tGravityAcc-XYZ tBodyAccJerk-XYZ tBodyGyro-XYZ tBodyGyroJerk-XYZ tBodyAccMag tGravityAccMag tBodyAccJerkMag tBodyGyroMag tBodyGyroJerkMag fBodyAcc-XYZ fBodyAccJerk-XYZ fBodyGyro-XYZ fBodyAccMag fBodyAccJerkMag fBodyGyroMag fBodyGyroJerkMag

The set of variables that were estimated from these signals are:

mean(): Mean value std(): Standard deviation mad(): Median absolute deviation max(): Largest value in array min(): Smallest value in array sma(): Signal magnitude area energy(): Energy measure. Sum of the squares divided by the number of values. iqr(): Interquartile range entropy(): Signal entropy arCoeff(): Autorregresion coefficients with Burg order equal to 4 correlation(): correlation coefficient between two signals maxInds(): index of the frequency component with largest magnitude meanFreq(): Weighted average of the frequency components to obtain a mean frequency skewness(): skewness of the frequency domain signal kurtosis(): kurtosis of the frequency domain signal bandsEnergy(): Energy of a frequency interval within the 64 bins of the FFT of each window. angle(): Angle between to vectors.

Additional vectors obtained by averaging the signals in a signal window sample. These are used on the angle() variable:

gravityMean tBodyAccMean tBodyAccJerkMean tBodyGyroMean tBodyGyroJerkMean

The complete list of variables of each feature vector is available in 'features.txt'
