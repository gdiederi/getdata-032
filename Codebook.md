#Codebook.md
### Gerhard Diedericks
### 27 september 2015
#### getdata-032

#Getting and Cleaning Data (Coursera) : Course Project

## Project Objectives
Prepare a tidy dataset that can be used for subsequent analysis.

## Original Dataset
Can be obtained at :
https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

A full description of the data is available at :
http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

Unzipped files from Dataset.zip that will be used :

test/subject_test.txt
train/subject_train.txt
test/X_test.txt
train/X_train.txt
test/y_test.txt
train/y_train.txt
features.txt - Names of column variables in the dataTable
activity_labels.txt - Links the class labels with their activity name.

## Description of Dataset
The features selected for this database come from the accelerometer and gyroscope 3-axial raw signals tAcc-XYZ and tGyro-XYZ. These time domain signals (prefix ‘t’ to denote time) were captured at a constant rate of 50 Hz. and the acceleration signal was then separated into body and gravity acceleration signals (tBodyAcc-XYZ and tGravityAcc-XYZ) – both using a low pass Butterworth filter.
The body linear acceleration and angular velocity were derived in time to obtain Jerk signals (tBodyAccJerk-XYZ and tBodyGyroJerk-XYZ). Also the magnitude of these three-dimensional signals were calculated using the Euclidean norm (tBodyAccMag, tGravityAccMag, tBodyAccJerkMag, tBodyGyroMag, tBodyGyroJerkMag).
A Fast Fourier Transform (FFT) was applied to some of these signals producing fBodyAcc-XYZ, fBodyAccJerk-XYZ, fBodyGyro-XYZ, fBodyAccJerkMag, fBodyGyroMag, fBodyGyroJerkMag. (Note the ‘f’ to indicate frequency domain signals).

## Abbreviations used in measurements
Description of abbreviations of measurements

1. leading t or f is based on time or frequency measurements.
2. Body = related to body movement.
3. Gravity = acceleration of gravity
4. Acc = accelerometer measurement
5. Gyro = gyroscopic measurements
6. Jerk = sudden movement acceleration
7. Mag = magnitude of movement
8. mean and SD are calculated for each subject for each activity for each mean and SD measurements.
10. The units given are g’s for the accelerometer and rad/sec for the gyro and g/sec and rad/sec/sec for the corresponding jerks.

These signals were used to estimate variables of the feature vector for each pattern:
‘-XYZ’ is used to denote 3-axial signals in the X, Y and Z directions. They total 33 measurements including the 3 dimensions - the X,Y, and Z axes.

tBodyAcc-XYZ
tGravityAcc-XYZ
tBodyAccJerk-XYZ
tBodyGyro-XYZ
tBodyGyroJerk-XYZ
tBodyAccMag
tGravityAccMag
tBodyAccJerkMag
tBodyGyroMag
tBodyGyroJerkMag
fBodyAcc-XYZ
fBodyAccJerk-XYZ
fBodyGyro-XYZ
fBodyAccMag
fBodyAccJerkMag
fBodyGyroMag
fBodyGyroJerkMag

The set of variables that were estimated from these signals are:

mean(): Mean value
std(): Standard deviation

## Description of Study
The experiments have been carried out with a group of 30 volunteers within an age bracket of 19-48 years. Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone (Samsung Galaxy S II) on the waist. Using its embedded accelerometer and gyroscope, we captured 3-axial linear acceleration and 3-axial angular velocity at a constant rate of 50Hz. The experiments have been video-recorded to label the data manually. The obtained dataset has been randomly partitioned into two sets, where 70% of the volunteers was selected for generating the training data and 30% the test data.
The sensor signals (accelerometer and gyroscope) were pre-processed by applying noise filters and then sampled in fixed-width sliding windows. From each window, a vector of features was obtained by calculating variables from the time and frequency domain.

## Description of Script

		## run_analysis.R
		##Script for Course Project (getdata-032)
		##Gerhard Diedericks

		## Sets working directory
		*setwd("C:/Data Science Resources/GACD Week3 Project")*

#A. Downloads datafile from URL and store in a temp file

		##1. Downloads the zipped datafile from the URL provided by Coursera.
		## Saves the datafile in temporary file called temp.
		## This elimnates the need for the user to download, unzip and move needed files into working directory.

		*temp <- tempfile()*
		*download.file("https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip", temp)*

#B. Reads files from tempfile

		##2. Reads the Activity, Subject and Features files
		      
		      *subject_test <-   read.table(unz(temp, "UCI HAR Dataset/test/subject_test.txt"))*
		      *X_test <-         read.table(unz(temp, "UCI HAR Dataset/test/X_test.txt"))*
		      *Y_test <-         read.table(unz(temp, "UCI HAR Dataset/test/y_test.txt"))*
		      *subject_train <-  read.table(unz(temp, "UCI HAR Dataset/train/subject_train.txt"))*
		      *X_train <-        read.table(unz(temp, "UCI HAR Dataset/train/X_train.txt"))*
		      *Y_train <-        read.table(unz(temp, "UCI HAR Dataset/train/y_train.txt"))*
		      *features <-       read.table(unz(temp, "UCI HAR Dataset/features.txt"))*
		      *activities <-     read.table(unz(temp, "UCI HAR Dataset/activity_labels.txt"))*

#C. Merge into one dataset

		##4. Merge X_train & X_test using row binding
		*x.full <-   rbind(X_train, X_test)*
		
		##5. Merge y_train and y_test using row binding
		*y.full <-   rbind(Y_train, Y_test)*

		##6. Merge subject_train and subject_test using row binding
		*sub.full <- rbind(subject_train, subject_test)*

		##7. Merge y_train and y_test using column binding
		*all.full <- cbind(sub.full, y.full, x.full)*

		##8. Remove from memory, not needed anymore
		*rm(X_test,Y_test,X_train,Y_train,subject_train,subject_test,x.full,y.full,sub.full)*

#D. Name columns
		##9. Name measurement columns from 2nd column in features table, give names to the id columns
		*FeatureNames <-         as.character(features[,2])*
		*NewCols <-              c("Subject", "Activity", FeatureNames)*
		*colnames(all.full) <-   NewCols*

#E. Create new dataframe with mean ans stdev

		##10. Create new data frame with only mean and st. dev features
		*MeansCol <-       grep("mean()", colnames(all.full))*
		*StDevsCol <-      grep("std()", colnames(all.full))*
		*NewColumns <-     c(MeansCol, StDevsCol)*
		*SortedColumns <-  sort(NewColumns) *
		*NewDataFrame <-   all.full[, c(1,2,SortedColumns)]*
		*NewDataFrame2 <-  NewDataFrame[, !grepl("Freq", colnames(NewDataFrame))]*

		##11. Remove unwanted dataframes from memory
		*rm(NewDataFrame, all.full)*

#F. Create tidy data frame

		##12. Two nested for routines to create a new data frame with 180 observations (30 subjects with 6 activities each)
		*TidyFrame <- data.frame()*
		*for (i in 1:30) {*
		      *subj<- subset(NewDataFrame2,Subject==i)*
		           * for (j in 1:6){*
		                  *actv<- subset(subj, Activity==j)*
		                  *myresult<-as.vector(apply(actv,2,mean))*
		                  *TidyFrame<-rbind(TidyFrame,myresult) *
		     * }*
      
		*}*

		##13. Add in column names again
		*colnames(TidyFrame)<-   colnames(NewDataFrame2)*

		##14. Adds in activity descriptors
		*levels(TidyFrame[,2])<- c('WALKING','WALKING UPSTAIRS','WALKING DOWNSTAIRS', 'SITTING','STANDING', 'LAYING')*

		##15. Make variable and descriptor names easier to read
		*names(TidyFrame)<- gsub("std()", "SD", names(TidyFrame))*
		*names(TidyFrame)<- gsub("mean()", "MEAN", names(TidyFrame))*
		*names(TidyFrame)<- gsub("^t", "time", names(TidyFrame))*
		*names(TidyFrame)<- gsub("^f", "frequency", names(TidyFrame))*
		*names(TidyFrame)<- gsub("Acc", "Accelerometer", names(TidyFrame))*
		*names(TidyFrame)<- gsub("Gyro", "Gyroscope", names(TidyFrame))*
		*names(TidyFrame)<- gsub("Mag", "Magnitude", names(TidyFrame))*
		*names(TidyFrame)<- gsub("BodyBody", "Body", names(TidyFrame))*

#G. Writes data frame to txt file.
		##16. Writes the tidy data set to a file called "Tidy_Data_Samsung.txt" in the working directory
		*write.table(TidyFrame, "Tidy_Data_Samsung.txt", sep = "", row.names = FALSE)*

		##17. End of run_Analysis.R

## Description of Tidy Dataset
This tidy data set has 180 rows and 69 columns. The first row is a header row containing the names for each column. 
In total there are 68 columns (33 mean variables, 33 stdev variables, a subject column and an activity column)
The following variables are presented in the tidy dataset :
 
Subject                                       
Activity                                      
timeBodyAccelerometer-MEAN()-X                
timeBodyAccelerometer-MEAN()-Y              
timeBodyAccelerometer-MEAN()-Z                
timeBodyAccelerometer-SD()-X                  
timeBodyAccelerometer-SD()-Y                 
timeBodyAccelerometer-SD()-Z                
timeGravityAccelerometer-MEAN()-X          
timeGravityAccelerometer-MEAN()-Y          
timeGravityAccelerometer-MEAN()-Z           
timeGravityAccelerometer-SD()-X             
timeGravityAccelerometer-SD()-Y           
timeGravityAccelerometer-SD()-Z             
timeBodyAccelerometerJerk-MEAN()-X           
timeBodyAccelerometerJerk-MEAN()-Y           
timeBodyAccelerometerJerk-MEAN()-Z           
timeBodyAccelerometerJerk-SD()-X            
timeBodyAccelerometerJerk-SD()-Y             
timeBodyAccelerometerJerk-SD()-Z            
timeBodyGyroscope-MEAN()-X                   
timeBodyGyroscope-MEAN()-Y                   
timeBodyGyroscope-MEAN()-Z                    
timeBodyGyroscope-SD()-X                     
timeBodyGyroscope-SD()-Y                     
timeBodyGyroscope-SD()-Z                      
timeBodyGyroscopeJerk-MEAN()-X                
timeBodyGyroscopeJerk-MEAN()-Y                
timeBodyGyroscopeJerk-MEAN()-Z                
timeBodyGyroscopeJerk-SD()-X                  
timeBodyGyroscopeJerk-SD()-Y                  
timeBodyGyroscopeJerk-SD()-Z                  
timeBodyAccelerometerMagnitude-MEAN()         
timeBodyAccelerometerMagnitude-SD()          
timeGravityAccelerometerMagnitude-MEAN()      
timeGravityAccelerometerMagnitude-SD()        
timeBodyAccelerometerJerkMagnitude-MEAN()     
timeBodyAccelerometerJerkMagnitude-SD()       
timeBodyGyroscopeMagnitude-MEAN()             
timeBodyGyroscopeMagnitude-SD()               
timeBodyGyroscopeJerkMagnitude-MEAN()         
timeBodyGyroscopeJerkMagnitude-SD()           
frequencyBodyAccelerometer-MEAN()-Y           
frequencyBodyAccelerometer-MEAN()-Z           
frequencyBodyAccelerometer-SD()-X             
frequencyBodyAccelerometer-SD()-Y             
frequencyBodyAccelerometer-SD()-Z             
frequencyBodyAccelerometerJerk-MEAN()-X       
frequencyBodyAccelerometerJerk-MEAN()-Y       
frequencyBodyAccelerometerJerk-MEAN()-Z       
frequencyBodyAccelerometerJerk-SD()-X         
frequencyBodyAccelerometerJerk-SD()-Y         
frequencyBodyAccelerometerJerk-SD()-Z         
frequencyBodyGyroscope-MEAN()-X               
frequencyBodyGyroscope-MEAN()-Y               
frequencyBodyGyroscope-MEAN()-Z              
frequencyBodyGyroscope-SD()-X                 
frequencyBodyGyroscope-SD()-Y                 
frequencyBodyGyroscope-SD()-Z                 
frequencyBodyAccelerometerMagnitude-MEAN()    
frequencyBodyAccelerometerMagnitude-SD()    
frequencyBodyAccelerometerJerkMagnitude-MEAN()
frequencyBodyAccelerometerJerkMagnitude-SD()  
frequencyBodyGyroscopeMagnitude-MEAN()        
frequencyBodyGyroscopeMagnitude-SD()          
frequencyBodyGyroscopeJerkMagnitude-MEAN()    
frequencyBodyGyroscopeJerkMagnitude-SD()

## End of Markup Document