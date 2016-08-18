# Face-Recognition
# classification algorithm: k-Nearest Neighbour

#packages 
install.packages("class")
install.packages("gmodels")
install.packages("mclust")
library("class")
library("gmodels")
library("mclust")

#read in the data after dimension reduction
setwd("C:/Users/...")
faces <- read.table("newfaces1.txt")

#preprocessing for kNN Algorithm (normalize)
normalize <- function(x) {return ((x-min(x))/(max(x)-min(x)))}
faces_n <- as.data.frame(lapply(faces,normalize))

#splitting the data in test and training data
id<-rep(1:40 ,each=10)

faces.data.frame_n<-data.frame(cbind(id=id , faces_n ))
faces.data.frame_n

testid<-seq(10 ,400 ,by=10)
train.faces.a<-faces.data.frame_n[-testid ,]
train.faces.knn <- train.faces.a [,-1]
test.faces.b<-faces.data.frame_n[testid ,]
test.faces.knn <- test.faces.b [,-1]

train_target <- faces.data.frame_n[-testid,id]
faces.data.frame<-data.frame(cbind(id=id , faces))
train.faces.c<-faces.data.frame[-testid ,]
train.faces.target <- train.faces.c [,1]
test.faces.d<-faces.data.frame[testid ,]
test.faces.target <- test.faces.d [,1]

#calculate the k (how many nearest neighbours we want to have?)
cverr<-rep(0,40)
for(i in 1:40){
  set.seed(i)  # set the seed 
  kcl<-knn(train.faces[,-1],test.faces[,-1],train.faces$id,k=i,prob=F,use.all=T)
  cverr[i]<-classError(kcl,CL)$errorRate
}
plot(1:40,cverr,type="l",col="blue",xlab="k",ylab="ErrorRate",main="Ralationship between k and errorRate")
lines(1:40,cverr,type="l",col="red",lwd=2)
which.min(cverr)

#k-nn calculation
knn.model <- knn(train = train.faces.knn, test = test.faces.knn, cl = train.faces.target, k=5)

#k-NN confusion matrix
tab <- table(test.faces.target,knn.model)

summary(tab)
sum(knn.model!=test.faces.target)/length(knn.model)

#k-NN cross table
a <- test.faces.target[1:11]
b <- knn.model[1:11]
crosstable <- CrossTable(x = a, y = b, prop.chisq=FALSE)

#calculation of running time
ptm<-proc.time() 
knn=knn(train = train.faces.knn, test = test.faces.knn, cl = train.faces.target, k=5)
proc.time()-ptm 
