# Face-Recognition

#packages 
install.packages("kernlab")
library(kernlab)
library(e1071)
library("gmodels")

#read in the data after dimension reduction (ICA)
setwd("C:/Users/....")
faces <- read.table("newfaces1.txt")

#data splitting: train set and test set (here: one picture per person as test set and 9 pictures per person as train set)
id<-rep(1:40 ,each=10)

faces.data.frame<-data.frame(cbind(id=id , faces ))
faces.data.frame

testid<-seq(10 ,400 ,by=10)
train.faces<-faces.data.frame[-testid ,]
test.faces<-faces.data.frame[testid ,]

#calculate the best c
cverr<-rep(0,40)
for(i in 1:40){
  set.seed(i)  # set the seed 
  svmcl<-svm(train.faces[,-1],test.faces[,-1],train.faces$id,C=i,prob=F,use.all=T)
  cverr[i]<-classError(svmcl,CL)$errorRate
}
plot(1:40,cverr,type="l",col="blue",xlab="k",ylab="ErrorRate",main="Ralationship between k and errorRate")
lines(1:40,cverr,type="l",col="red",lwd=2)
which.min(cverr)

#SVM model
svm.model<-ksvm(id~.,data=train.faces,type="C-svc",kernel="vanilladot",C = 10,drop=FALSE)

#prediction
svm.prediction<-predict(svm.model,test.faces)
svm.prediction
summary(svm.prediction)

#comparison between predicted and truth values --> accuracy measurement
CL<-faces.data.frame$id[testid]

tab <- table(pred = svm.model, true = CL)
table(CL,svm.prediction)
sum(svm.prediction!=CL)/length(svm.prediction)

crosstable.svm <- CrossTable(x = svm.prediction[1:10] , y = CL[1:10], prop.chisq=FALSE)
crosstable.svm

#calculation of running time
ptm<-proc.time() 
svm=ksvm(id~.,data=train.faces,type="C-svc",kernel="vanilladot", C=10, drop=FALSE) 
proc.time()-ptm 
