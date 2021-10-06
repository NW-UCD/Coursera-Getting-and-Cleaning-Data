# Coursera-Getting-and-Cleaning-Data


Requires all data to be contained in the folder "UCI HAR Dataset", as it was when downloaded from the course website i.e.  https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip   

Run the script : run_analysis.R   to merge training and test data, isolate only variables containing
mean or standard deviation of measurements from the watch data, swap out the activity codes 1,2,3,4,5,6 
with their names LAYING, SITTING, STANDING, WALKING, WALKING_DOWNSTAIRS, WALKING_UPSTAIRS, rename the
variable with more descriptive names that are easier to understand by a non-specialist,  and finally
collects the mean average of each of these variables for each activity undertaken by each of the 30 
subjects (each person) and writes it into the file "qu5.txt" as a tidy data set, as per Hadley Wickham's 2013 paper, "Tidy Data".


There is a CodeBook.md file available giving details of the script run_analysis.R, explaining what each step of the code does to the data
and the meaning of any abbreviations used. 

The summarised data of averages can be read in R using  
averages <- read.table("qu5.txt", header = TRUE) 
    View(averages)
