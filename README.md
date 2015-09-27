## getdata-032

Course project for Getting &amp; Cleaning Data course project

####Please refer to the Codebook.md file for description of datasets and scripts.

You should create one R script called run_analysis.R that does the following.
 
 - Merges the training and the test sets to create one data set.(refer to Codebook.md and run_Analysis.R)
 - Extracts only the measurements on the mean and standard deviation for each measurement. (refer to Codebook.md and run_Analysis.R)
 - Uses descriptive activity names to name the activities in the data set (refer to Codebook.md and run_Analysis.R)
 - Appropriately labels the data set with descriptive variable names. (refer to Codebook.md and run_Analysis.R)
 - From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject. (refer Tidy_Data_Samsung.txt)

Thank you!

## Description of Script

		## run_analysis.R
		##Script for Course Project (getdata-032)
		##Gerhard Diedericks

		## Sets working directory
		setwd("C:/Data Science Resources/GACD Week3 Project")

###A. Downloads datafile from URL and store in a temp file

		##1. Downloads the zipped datafile from the URL provided by Coursera.
		## Saves the datafile in temporary file called temp.
		## This elimnates the need for the user to download, unzip and move needed files into working directory.

		temp <- tempfile()
		download.file("https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip", temp)

###B. Reads files from tempfile

		##2. Reads the Activity, Subject and Features files
		      
		      subject_test <-   read.table(unz(temp, "UCI HAR Dataset/test/subject_test.txt"))
		      X_test <-         read.table(unz(temp, "UCI HAR Dataset/test/X_test.txt"))
		      Y_test <-         read.table(unz(temp, "UCI HAR Dataset/test/y_test.txt"))
		      subject_train <-  read.table(unz(temp, "UCI HAR Dataset/train/subject_train.txt"))
		      X_train <-        read.table(unz(temp, "UCI HAR Dataset/train/X_train.txt"))
		      Y_train <-        read.table(unz(temp, "UCI HAR Dataset/train/y_train.txt"))
		      features <-       read.table(unz(temp, "UCI HAR Dataset/features.txt"))
		      activities <-     read.table(unz(temp, "UCI HAR Dataset/activity_labels.txt"))

###C. Merge into one dataset

		##4. Merge X_train & X_test using row binding
		x.full <-   rbind(X_train, X_test)
		
		##5. Merge y_train and y_test using row binding
		y.full <-   rbind(Y_train, Y_test)

		##6. Merge subject_train and subject_test using row binding
		sub.full <- rbind(subject_train, subject_test)

		##7. Merge y_train and y_test using column binding
		all.full <- cbind(sub.full, y.full, x.full)

		##8. Remove from memory, not needed anymore
		rm(X_test,Y_test,X_train,Y_train,subject_train,subject_test,x.full,y.full,sub.full)

###D. Name columns
		##9. Name measurement columns from 2nd column in features table, give names to the id columns
		FeatureNames <-         as.character(features[,2])
		NewCols <-              c("Subject", "Activity", FeatureNames)
		colnames(all.full) <-   NewCols

###E. Create new dataframe with mean and stdev

		##10. Create new data frame with only mean and st. dev features
		MeansCol <-       grep("mean()", colnames(all.full))
		StDevsCol <-      grep("std()", colnames(all.full))
		NewColumns <-     c(MeansCol, StDevsCol)
		SortedColumns <-  sort(NewColumns) 
		NewDataFrame <-   all.full[, c(1,2,SortedColumns)]
		NewDataFrame2 <-  NewDataFrame[, !grepl("Freq", colnames(NewDataFrame))]

		##11. Remove unwanted dataframes from memory
		rm(NewDataFrame, all.full)

###F. Create tidy data frame

		##12. Two nested for routines to create a new data frame with 180 observations (30 subjects with 6 activities each)
		TidyFrame <- data.frame()
		for (i in 1:30) {
		      subj<- subset(NewDataFrame2,Subject==i)
		            for (j in 1:6){
		                  actv<- subset(subj, Activity==j)
		                  myresult<-as.vector(apply(actv,2,mean))
		                  TidyFrame<-rbind(TidyFrame,myresult)
		      }
      
		}

		##13. Add in column names again
		colnames(TidyFrame)<-   colnames(NewDataFrame2)

		##14. Adds in activity descriptors
		levels(TidyFrame[,2])<- c('WALKING','WALKING UPSTAIRS','WALKING DOWNSTAIRS', 'SITTING','STANDING', 'LAYING')

		##15. Make variable and descriptor names easier to read
		names(TidyFrame)<- gsub("std()", "SD", names(TidyFrame))
		names(TidyFrame)<- gsub("mean()", "MEAN", names(TidyFrame))
		names(TidyFrame)<- gsub("^t", "time", names(TidyFrame))
		names(TidyFrame)<- gsub("^f", "frequency", names(TidyFrame))
		names(TidyFrame)<- gsub("Acc", "Accelerometer", names(TidyFrame))
		names(TidyFrame)<- gsub("Gyro", "Gyroscope", names(TidyFrame))
		names(TidyFrame)<- gsub("Mag", "Magnitude", names(TidyFrame))
		names(TidyFrame)<- gsub("BodyBody", "Body", names(TidyFrame))

###G. Writes data frame to txt file.
		##16. Writes the tidy data set to a file called "Tidy_Data_Samsung.txt" in the working directory
		write.table(TidyFrame, "Tidy_Data_Samsung.txt", sep = "", row.names = FALSE)

		##17. End of run_Analysis.R


