
//This is a readme file explaining how the in run_analysis.R works.

library(dplyr)

// *Note* All the txt files have been unzipped and I stored them in the working directory 
// Reading X , Y and subject data of test and train
// Colname for activity and subject for ease in merging


X_train<-read.table("X_train.txt",header=FALSE)
Y_train<-read.table("y_train.txt",header=F)
colnames(Y_train)<-c("Activity Label")
Sub_train<-read.table("subject_train.txt",header=F)
colnames(Sub_train)<-"Subject"
X_test<-read.table("X_test.txt")
Y_test<-read.table("y_test.txt",header = FALSE,col.names = "Activity Label")
colnames(Y_test)<-"Activity Label"
Sub_test<-read.table("subject_test.txt",header=F)
colnames(Sub_test)<-"Subject"

// rBind the X , Y and Subject values for test and train 

X_total<-rbind(X_train,X_test)
Y_total<-rbind(Y_train,Y_test)
Sub_total<-rbind(Sub_train,Sub_test)

// We read the features.txt and extract the columns for mean and standart deviation.
// and name the variable columns

var_names<-read.table("features.txt")

//m has indecies of the mean variables.

m<-grep("mean()",var_names[,2],fixed=TRUE)

// s has the indeces of the standard deviation varaiables

s<-grep("std()",var_names[,2],fixed = TRUE)

// b is the object with the required varaiable names

b<-var_names[c(m,s),2]

// X_revised, the object with only the required variables
X_revised<-X_total[,c(m,s),colnames=b]

// read activity names

r<-read.table("activity_labels.txt")

// We bind the columns of activity with the main data.
Data<-cbind(Sub_total,Y_total,X_revised)

// We convert the activity labels into factors with activity names. 

Activity_Label<-factor(Data$`Activity Label`,labels = r[,2])
Data<-tbl_df(Data)
Data<-cbind(Activity_Label,Data)

// Finally we group the data and summarise it into an object "Answer"
// Data is the file with the complete data required for conversion.

Data<-group_by(Data,Activity_Label,Subject)
Summary<-summarise_all(Data,mean)
Answer<-Data %>% group_by(Activity_Label,Subject) %>% summarise_all(mean) %>% select(1:69)

// 2 unnecessary columns were generated so we selected all the columns except those 2.
// Creating required tidy data set

write.table(Answer,file = "tidy_data_set.txt",row.names = F)




