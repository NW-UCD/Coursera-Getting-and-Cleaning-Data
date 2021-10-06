The Variables and Data:

Subject number gives the ID number of each of the 30 participants in the study. 
Activity gives the names of one of the 6 activies each participants carried out: Laying, Sitting, Standing, Walking, Walking Upstairs, and Walking Downstairs

There are then 66 numerical variables giving them measurements taken by the accelerometer or the gyroscope on the samsung smart phone being carried by the participant. 
These variables give the average value from the measurement device across the whole time the participant was undertaking the activity. 
These either show the mean from each 50hz interval, or the standard deviation. Many are divided into the X, Y, or Z planes. Others show the magnitude 
of all these planes combined. Some are measurements of the body's movement, others of Gravity. And some are calculate the Jerk, isolating peaks in the measurement
to identify more vigorous and shorter movements. 
The last 25 of these 66 variables give the frequency values rather than acceleration, calculated before any transformations
or summaries using a Fast Fourier Transformation.


Transformations and Cleaning:

The data is initially divided into training and test groups. These data files are read using fread to make data frames, not data tables. 
Then combined with rbind into one large data frame. 

   library(data.table)
        library(dplyr)

        ## Load in the data file paths
        variablenamesfile <- "UCI HAR Dataset/features.txt"
        testdatafile <- "UCI HAR Dataset/test/X_test.txt"
        traindatafile <- "UCI HAR Dataset/train/X_train.txt"
        
        ## Read data files to R data tables, with data.table = false to make data frame rather than a table
        variablenames <- read.table(variablenamesfile)
        testdata <- fread(testdatafile, data.table = FALSE) 
        traindata <- fread(traindatafile, data.table = FALSE)
        
        ## rbind testdata and traindata. stacked one on the other. Then add the variable names. 
        fulldata <- rbind(testdata, traindata)
        colnames(fulldata) <- variablenames[, 2]

Columns are extracted that contain information on mean and standard deviation values. 

        ## 2. Extracts only columns giving mean or std, creating new data set: filterdata
        ## finds col names with 'mean()' or 'std()'and then creates new data frame containing
                ## only those columns, "filterdata".Uses sort() to put the whole search into order
                ## rather than have the "std()" names followed by "mean()" when applied as a filter.
        colfilter <- sort(c(grep("std\\(\\)", variablenames[,2]), grep("mean\\(\\)", variablenames[,2])))
        filterdata <- fulldata[, colfilter]          ##subsets the data by the column names found in colfilter
        
                        ## Did not include the strings 'mean' or 'Mean', to omitt the 'meanFreq() function columns
                        ## and hardware averaged data using a signal window sample, labeled as 'Mean' 


The activity name codes (1,2,3,4,5,6) were replaced with their respective names, taken from the data files y_text.txt and y_train.txt



      ## 3. descriptive activity names
        ## Reads the Activity numbers into R as data frames, before combining them and then binding
                ## them as the first column of the filterdata set, now named labeldata
        testactivityfile <- "UCI HAR Dataset/test/y_test.txt"
        trainactivityfile <- "UCI HAR Dataset/train/y_train.txt"
        testactivity <- fread(testactivityfile, data.table = FALSE)
        trainactivity <- fread(trainactivityfile, data.table = FALSE)
        labeldata <- cbind(rbind(testactivity, trainactivity), filterdata)
        
        ## Substitutes the Activity numbers with descriptive Activity Names 
        labeldata[,1] <- sub(1, "WALKING", labeldata[,1])
        labeldata[,1] <- sub(2, "WALKING_UPSTAIRS", labeldata[,1])
        labeldata[,1] <- sub(3, "WALKING_DOWNSTAIRS", labeldata[,1])
        labeldata[,1] <- sub(4, "SITTING", labeldata[,1])
        labeldata[,1] <- sub(5, "STANDING", labeldata[,1])
        labeldata[,1] <- sub(6, "LAYING", labeldata[,1])
        
        
        
        
The variable names were extended from their abbreviation codes to full words to make the data easier for a non-specialist to understand quickly
        
            ## 4. Descriptive labels: Appropriately labels the data set with descriptive variable names. 
        
                ## These are descriptive names because they....
                ## put in the code book why they are descriptive names
        
        
        ## the 't' prefix donates time domain measurements at 50hz, the 'f' prefix 
                # denotes frequency following Fast Fourier Transform
        ## measurements separated into body and gravity measurements
        
        ## Original variable name codes and replacements full words:
        
        # 1 - "tBody"     = "Body"
        # 2 - "tGravity"   = "Gravity"
        # 3 - "Acc" = " Acceleration"
        # 4 - "Jerk" = " Jerk"
        # 5 - "Gyro" = " Gyroscope"
        # 6 - "Mag" = " Magnitude"
        # 7 - "fBodyBody" = "Frequency of Body"
        # 8 - "fBody" = "Frequency of Body"                      -- comes after "fBodyBody" 'fBody' isn't swapped out then a 
                                                                ## "Body" string is left hanging around. 
        ## 9 "X" = " in the X plane"
        ## 10 "Y" = " in the Y plane"
        ## 11 "Z" = " in the Z plane"
        ## 12 "std()" = " standard deviation"
        ## 13 "mean()" = " mean value"
        
        
        ## put the above codes to labels text into two separate vectors, one for the original pattern in the variable
                        ## names, and one for the new fully worded labels
        labels.pattern <- c("tBody", "tGravity", "Acc", "Jerk", "Gyro", "Mag", "fBodyBody", "fBody",
                                "X", "Y", "Z", "std()", "mean()")
        
        labels.replacement <- c("Body", "Gravity", " Acceleration", " Jerk", " Gyroscope", " Magnitude",
                                "Frequency of Body" , "Frequency of Body", " in the X plane",
                                " in the Y plane", " in the Z plane", "standard deviation", "mean value")
        
        for (i in 1:(length(labels.pattern))) { 
        colnames(labeldata) <- sub(labels.pattern[i], labels.replacement[i], colnames(labeldata), fixed = TRUE)
        }
        
        colnames(labeldata)[1] <- "Activity"
        
        
A summary of the data giving the mean of each value (originally reapeatedly recorded at 50hz) for each activity performed by each subject. 
          This is presented in a Tidy Data set arranged by Subject first, and activity second. Giving 6 rows of data for each subject (one 
          for each of the 6 activities). 
        

        ## write.table(mydf, "mydf.txt")
        
        ## to go in the read.me praise.me :
        ## use:
        ## "data <- read.table(file_path, header = TRUE) #if they used some other way of saving the
                        ###file than a default write.table, this step will be different
        ## View(data)
        ##
        ## to read in the file to R. 

        
        ## Load in data containing subject numbers, combine test and training data, then combine with 
                ## mean and standard deviation data collected from the phones
        testsubjectsfile <- "UCI HAR Dataset/test/subject_test.txt"
        trainsubjectsfile <- "UCI HAR Dataset/train/subject_train.txt"
        testsubjects <- fread(testsubjectsfile, data.table = FALSE)
        trainsubjects <- fread(trainsubjectsfile, data.table = FALSE)
        subjects <- rbind(testsubjects, trainsubjects)
        colnames(subjects) <- "Subject Number"
        
        qu5 <- cbind(subjects, labeldata)
        
        qu5.groups <- group_by(qu5,  qu5[,"Subject Number"], qu5[,"Activity"])
        averages <- summarize_each(qu5.groups, mean)
        
        ## remove extra 2 columns added by summarize_each, and add the correct variable names back in
        
        colnames(averages)[1:2] <- c("Subject Number", "Activity")
        averages <- averages[,-(3:4)]
        write.table(averages, "qu5.txt", row.names = FALSE)
        
