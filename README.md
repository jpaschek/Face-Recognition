# Face-Recognition

library(MASS)
library(class)
library(mclust)
library(kernlab)

#split the data
id<-rep(1:40 ,each=10)
faces.data.frame<-data.frame(cbind(id=id , faces ))

testid<-seq(10 ,400 ,by=10)
train.faces<-faces.data.frame[-testid ,]
test.faces<-faces.data.frame[testid ,]

#计算主成分方向，选择主成分个数以及计算主成分得分
xc<-scale(train.faces[,-1], scale=FALSE)
A<-t(xc)/sqrt(360-1)        # 4096x360
#thus , the covariance matrix is A%*%t(A)
A.egn<-eigen(t(A)%*%A)      # 360x360
pc<-A%*%A.egn$vectors
pc<-apply(pc ,2 , function(i) i/sqrt(sum(i*i)))   # normalize the pc
n<-80
sum(A.egn$value [1:n])/sum(A.egn$value)
#取前80个成分可解释的总体方差比例为92.08573%, 此处选取前80个成分降维
pcs<-pc[ ,1:n]
yt<-xc%*%pcs    # pc scores for training data

#FLDA Fisher
#在主成分空间使用线性判别分析训练分类器，并对训练样本进行判别
ft<-data.frame(cbind(id[-testid] , yt))
ptm<-proc.time()      #计时
flda<-lda(X1~., ft )  #分类器
proc.time()-ptm
fl<-predict(flda , ft )$class
table(fl)
diag(table(fl , id[-testid]))
classError(fl, id[-testid])  # 错误及错误率

#对检验集，计算其在主成分空间下的得分，用之前训练好的分类器分类，检验分类效果
xv<-as.matrix( test.faces[ , -1])
xvs<-scale(xv, scale=FALSE)
yv<-xvs%*%pcs    # pc scores for testing data
fv<-data.frame(cbind(id[testid] ,yv))

ptm<-proc.time()  #计时
pl<-predict(flda , fv)$class
proc.time()-ptm
table(pl)
diag(table(pl ,id[testid]))
classError(pl, id[testid])
